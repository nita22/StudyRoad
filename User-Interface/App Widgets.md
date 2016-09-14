## 使用App Widgets
### 在清单文件中声明
`AppWidgetProvider`为`BroadcastReceiver`的子类，需要在清单文件中声明。
``` xml
<receiver android:name="ExampleAppWidgetProvider" >
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
    </intent-filter>
    <meta-data android:name="android.appwidget.provider"
               android:resource="@xml/example_appwidget_info" />
</receiver>
```
需要在\<intent-filter\>的\<action\>中明确指明`android.appwidget.action.APPWIDGET_UPDATE`作为小部件的标识<br>
\<meta-data\>元素中指明`AppWidgetProviderInfo`的资源，其中name使用`android.appwidget.provider`，resource指明`AppWidgetProviderInfo`的xml文件位置


### 添加`AppWidgetProviderInfo`数据
在res/xml/下新建xml文件，在其中定义小部件配置信息
``` xml
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:minWidth="40dp"
    android:minHeight="40dp"
    android:updatePeriodMillis="86400000"
    android:previewImage="@drawable/preview"
    android:initialLayout="@layout/example_appwidget"
    android:configure="com.example.android.ExampleAppWidgetConfigure"
    android:resizeMode="horizontal|vertical"
    android:widgetCategory="home_screen">
</appwidget-provider>
```
* minWidth 和minHeight<br>
  它们指定了App Widget布局需要的最小区域。
  如果App Widget的最小长度或宽度和网格单元的尺寸不匹配，那么这个App Widget将近似到最接近的单元尺寸。

* minResizeWidth 和 minResizeHeight <br>
  它们属性指定了 widget 的最小绝对尺寸。也就是说，如果 widget 小于该尺寸，便会因为变得模糊、看不清或不可用。 使用这两个属性，可以允许用户重新调整 widget 的大小，使 widget 的大小可以小于 minWidth 和 minHeight。<br>
  注意：当 minResizeWidth 的值比 minWidth 大时，minResizeWidth 无效；当 resizeMode 的取值不包括 horizontal 时，minResizeWidth 无效。<br>
        当 minResizeHeight 的值比 minHeight 大时，minResizeHeight 无效；当 resizeMode 的取值不包括 vertical 时，minResizeHeight 无效。<br>

* updatePeriodMillis <br>
  它定义了 widget 的更新频率。实际的更新时机不一定是精确的按照这个时间发生的，或者可以在配置 Activity 里面供用户对更新频率进行配置。 实际上，当updatePeriodMillis的值小于30分钟时，系统会自动将更新频率设为30分钟。

* initialLayout <br>
  指向 widget 的布局资源文件

* configure<br>
  可选属性，定义了 widget 的配置 Activity。如果定义了该项，那么当 widget 创建时，会自动启动该 Activity。

* previewImage<br>
  指定预览图，该预览图在用户选择 widget 时出现，如果没有提供，则会显示应用的图标。该字段对应在 AndroidManifest.xml 中 receiver 的`android:previewImage`字段。

* autoAdvanceViewId <br>
  指定一个子view ID，表明该子 view 会自动更新。

* resizeMode <br>
  指定了 widget 的调整尺寸的规则。可取的值有: "horizontal", "vertical", "none"。"horizontal"意味着widget可以水平拉伸，“vertical”意味着widget可以竖值拉伸，“none”意味着widget不能拉伸；默认值是"none"。

* widgetCategory <br>
  指定了 widget 能显示的地方：能否显示在 home Screen 或 lock screen 或 两者都可以。它的取值包括："home_screen" 和 "keyguard"。Android 5.0以上只能使用"home_screen" 。

* initialKeyguardLayout <br>
  指向 widget 位于 lockscreen 中的布局资源文件。

### 创建App Widget布局
RemoteViews所支持的所有类型如下：
* Layout<br>
FrameLayout、LinearLayout、RelativeLayout、GridLayout
* View<br>
AnalogClock、Button、Chronometer、ImageButton、ImageView、ProgressBar、TextView、ViewFlipper、ListView、GridView、StackView、AdapterViewFlipper、ViewStub

#### 设置桌面小部件的padding
res/values/dimens.xml:<br>
``` xml
<dimen name="widget_margin">8dp</dimen>
``` 

res/values-v14/dimens.xml:
``` xml
<dimen name="widget_margin">0dp</dimen>
```

### 使用AppWidgetProvider类
`AppWidgetProvider`继承自`BroadcastReceiver`，它能接收 widget 相关的广播<br>
AppWidgetProvider中的广播处理函数如下：<br>
* `onUpdate()`<br>
  当 widget 更新时被执行。同样，当用户首次添加 widget 时，onUpdate() 也会被调用，这样 widget 就能进行必要的设置工作(如果需要的话) 。但是，如果定义了 widget 的 configure属性(即android:configure)，那么当用户首次添加 widget 时，onUpdate()不会被调用；之后更新 widget 时，onUpdate才会被调用。

* `onAppWidgetOptionsChanged()`<br>
  当 widget 被初次添加 或者 当 widget 的大小被改变时，执行`onAppWidgetOptionsChanged()`。你可以在该函数中，根据 widget 的大小来显示/隐藏某些内容。可以通过`getAppWidgetOptions()`来返回 Bundle 对象以读取 widget 的大小信息，Bundle中包括以下信息：
  `OPTION_APPWIDGET_MIN_WIDTH` -- 包含 widget 当前宽度的下限，以dp为单位。
  `OPTION_APPWIDGET_MIN_HEIGHT` -- 包含 widget 当前高度的下限，以dp为单位。
  `OPTION_APPWIDGET_MAX_WIDTH` -- 包含 widget 当前宽度的上限，以dp为单位。
  `OPTION_APPWIDGET_MAX_HEIGHT` -- 包含 widget 当前高度的上限，以dp为单位。

* `onDeleted(Context, int[])`<br>
  当 widget 被删除时被触发。

* `onEnabled(Context)`<br>
  当第1个 widget 的实例被创建时触发。也就是说，如果用户对同一个 widget 增加了两次（两个实例），那么onEnabled()只会在第一次增加widget时触发。

* `onDisabled(Context)`<br>
  当最后1个 widget 的实例被删除时触发。

* `onReceive(Context, Intent)`<br>
  接收到任意广播时触发，并且会在上述的方法之前被调用。通常不用实现该方法。

``` java
public class ExampleAppWidgetProvider extends AppWidgetProvider {

    public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) {
        final int N = appWidgetIds.length;

        for (int i=0; i<N; i++) {
            int appWidgetId = appWidgetIds[i];

            Intent intent = new Intent(context, ExampleActivity.class);
            PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, intent, 0);

            RemoteViews views = new RemoteViews(context.getPackageName(), R.layout.appwidget_provider_layout);
            views.setOnClickPendingIntent(R.id.button, pendingIntent);

            appWidgetManager.updateAppWidget(appWidgetId, views);
        }
    }
}
```

如果在`onUpdate()`中需要处理耗时的任务，需要在`onUpdate()`中新建Service去完成耗时的任务，避免出现ANR问题

#### 自行处理App Widget的广播
可以自行继承`BroadcastReceiver`并实现`onReceive()`方法，需要注意以下intent
* ACTION_APPWIDGET_UPDATE
* ACTION_APPWIDGET_DELETED
* ACTION_APPWIDGET_ENABLED
* ACTION_APPWIDGET_DISABLED
* ACTION_APPWIDGET_OPTIONS_CHANGED

### 创建App Widget配置Activity
#### 在清单文件中声明
\<action\>中需要指明`android:name="android.appwidget.action.APPWIDGET_CONFIGURE"`来接收Intent
``` xml
<activity android:name=".ExampleAppWidgetConfigure">
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_CONFIGURE"/>
    </intent-filter>
</activity>
```

#### 在`AppWidgetProviderInfo`数据声明
`android:configure`中需要使用全称，因为有可能会被外部组件引用
``` xml
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    ...
    android:configure="com.example.android.ExampleAppWidgetConfigure"
    ... >
</appwidget-provider>
```

#### 利用配置Activity更新App Widget
* 从Intent中获取 App Widget ID
``` java
Intent intent = getIntent();
Bundle extras = intent.getExtras();
if (extras != null) {
    mAppWidgetId = extras.getInt(
            AppWidgetManager.EXTRA_APPWIDGET_ID,
            AppWidgetManager.INVALID_APPWIDGET_ID);
}
```

* 配置好App Widget的内容
* 配置完成后，获取`AppWidgetManager`的实例
``` java
AppWidgetManager appWidgetManager = AppWidgetManager.getInstance(context);
```
* 利用`updateAppWidget(int, RemoteViews)`方法更新App Widget
``` java
RemoteViews views = new RemoteViews(context.getPackageName(),
R.layout.example_appwidget);
appWidgetManager.updateAppWidget(mAppWidgetId, views);
```

* 创建返回的Intent，并结束Activity
``` java
Intent resultValue = new Intent();
resultValue.putExtra(AppWidgetManager.EXTRA_APPWIDGET_ID, mAppWidgetId);
setResult(RESULT_OK, resultValue);
finish();
```

当配置Activity第一次打开时，需要把返回的结果设置为 RESULT_CANCELED，避免在配置过程中出现错误而照样更新App Widget。

### 设置预览图片
如没有指定`android:previewImage`属性，系统则会利用App的图标作为预览图
``` xml
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
  ...
  android:previewImage="@drawable/preview">
</appwidget-provider>
```

## 使用带Collections的Widget
