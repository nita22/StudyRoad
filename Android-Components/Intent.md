## Intent基础知识
### Intent 类型
Intent 分为两种类型：
<br>
* 显式 Intent<br>
按名称（完全限定类名）指定要启动的组件
* 隐式 Intent<br>
不会指定特定的组件，而是声明要执行的常规操作，从而允许其他应用中的组件来处理它

### 构建 Intent
Intent 中包含的主要信息如下：
<br>
* 组件名称:要启动的组件名称<br>
可以使用`setComponent()`、`setClass()`、`setClassName()`或 Intent 构造函数设置组件名称

* 操作:指定要执行的通用操作的字符串<br>
可以使用`setAction()`或 Intent 构造函数为 Intent 指定操作<br>
如果定义自己的操作，请确保将应用的软件包名称作为前缀
``` java
static final String ACTION_TIMETRAVEL = "com.example.action.TIMETRAVEL";
```

* 数据:引用待操作数据和(或)该数据 MIME 类型的 URI（Uri 对象）<br>
要仅设置数据 URI，请调用`setData()`。要仅设置 MIME 类型，请调用`setType()`。如有必要，您可以使用`setDataAndType()`同时显式设置二者

* 类别:一个包含应处理 Intent 组件类型的附加信息的字符串<br>
可以使用`addCategory()`指定类别

* Extra:携带完成请求操作所需的附加信息的键值对<br>
可以使用各种`putExtra()`方法添加附加数据，每种方法均接受两个参数：键名和值。您还可以创建一个包含所有附加数据的 Bundle 对象，然后使用`putExtras()`将 Bundle 插入 Intent 中<br>
Intent 类将为标准化的数据类型指定多个 EXTRA_* 常量。如需声明自己的附加数据 键（对于应用接收的 Intent ），请确保将应用的软件包名称作为前缀
``` java 
static final String EXTRA_GIGAWATTS = "com.example.EXTRA_GIGAWATTS";
```

* 标志:标志可以指示 Android 系统如何启动 Activity，以及启动之后如何处理<br>
可以使用`setFlags()`方法指定标志

#### 显式Intent
``` java
Intent downloadIntent = new Intent(this, DownloadService.class);
startService(downloadIntent);
```

#### 隐式Intent
``` java
Intent sendIntent = new Intent();
sendIntent.setAction(Intent.ACTION_SEND);
sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
sendIntent.setType("text/plain");
startActivity(sendIntent);
```

#### 强制使用应用选择器
要显示选择器，请使用`createChooser()`创建 Intent，并将其传递给`startActivity()`
``` java
Intent sendIntent = new Intent(Intent.ACTION_SEND);
String title = getResources().getString(R.string.chooser_title);
Intent chooser = Intent.createChooser(sendIntent, title);
startActivity(chooser);
```

### 接收隐式 Intent
每个 Intent 过滤器均由应用清单文件中的\<intent-filter\>元素定义，并嵌套在相应的应用组件中。在\<intent-filter\>内部，您可以使用以下三个元素中的一个或多个指定要接受的 Intent 类型
* \<action\> : 在 name 属性中，声明接受的 Intent 操作
* \<data\> : 使用一个或多个指定 数据 URI（scheme、host、port、path 等）各个方面和 MIME 类型的属性，声明接受的数据类型
* \<category\> : 在 name 属性中，声明接受的 Intent 类别
``` xml
<activity android:name="ShareActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity>
```
为了接收隐式 Intent，必须将`CATEGORY_DEFAULT`类别包括在 Intent 过滤器中。方法`startActivity()`和`startActivityForResult()`将按照已申明`CATEGORY_DEFAULT`类别的方式处理所有 Intent。 如果未在 Intent 过滤器中声明此类别，则隐式 Intent 不会被解析


### Intent匹配原则
#### 操作匹配
``` java
<intent-filter>
    <action android:name="android.intent.action.EDIT" />
    <action android:name="android.intent.action.VIEW" />
    ...
</intent-filter>
```
要通过此过滤器，您在 Intent 中指定的操作必须与过滤器中列出的某一操作匹配<br>

#### 类别匹配
``` java
<intent-filter>
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    ...
</intent-filter>
```
若要 Intent 通过类别测试，则 Intent 中的每个类别均必须与过滤器中的类别匹配。不含类别的 Intent 应当始终会通过此测试。

#### 数据匹配
``` java
<intent-filter>
    <data android:mimeType="video/mpeg" android:scheme="http" ... />
    <data android:mimeType="audio/mpeg" android:scheme="http" ... />
    ...
</intent-filter>
```
每个 <data> 元素均可指定 URI 结构和数据类型（MIME 介质类型）。URI 的每个部分均包含单独的 scheme、host、port 和 path 属性：<br>
scheme\://host:port/path/ <br>
仅当过滤器未指定任何 URI 或 MIME 类型时，不含 URI 和 MIME 类型的 Intent 才会通过测试。不然Intent中必须含有data数据，并且data数据能够完全匹配过滤规则中的某一个data。URI的默认值为content和file。

### PendingIntent
PendingIntent 的主要目的是授权外部应用使用包含的 Intent<br>
待定 Intent 的主要用例包括：<br>
* 声明用户使用您的通知执行操作时所要执行的 Intent（Android 系统的`NotificationManager`执行 Intent）。
* 声明用户使用您的 应用小工具执行操作时要执行的 Intent（`AppWidget`执行 Intent）。
* 声明未来某一特定时间要执行的 Intent（Android 系统的`AlarmManager`执行 Intent）。

通过调用相应的创建器方法创建 PendingIntent 时，您必须声明所需的组件类型：<br>
* `PendingIntent.getActivity()`，适用于启动 Activity 的 Intent。
* `PendingIntent.getService()`，适用于启动 Service 的 Intent。
* `PendingIntent.getBroadcast()`，适用于启动 BroadcastReceiver 的 Intent。

### 判断Intent
PackageManager 提供了一整套 query...() 方法来返回所有能够接受特定 Intent 的组件。此外，它还提供了一系列类似的 resolve...() 方法来确定响应 Intent 的最佳组件。<br>
要验证 Activity 是否会接收 Intent，请对 Intent 对象调用`resolveActivity()`。如果结果为非空，则至少有一个应用能够处理该 Intent，且可以安全调用`startActivity()`。如果结果为空，则不应使用该 Intent。<br>
`queryIntentActivities()`将返回能够执行那些作为参数传递的 Intent 的所有 Activity 列表，而`queryIntentServices()`则可返回类似的服务列表。这两种方法均不会激活组件，而只是列出能够响应的组件。对于广播接收器，有一种类似的方法`queryBroadcastReceivers()`。
