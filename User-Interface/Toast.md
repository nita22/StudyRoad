# Toast

## 显示Toast

``` java
Context context = getApplicationContext();
CharSequence text = "Hello toast!";
int duration = Toast.LENGTH_SHORT;

Toast toast = Toast.makeText(context, text, duration);
toast.show();
```

## 改变Toast的显示位置

Toast默认显示在屏幕的底部中心位置。若想改变Toast的显示位置，可以通过`setGravity()`方法实现。

``` java
toast.setGravity(Gravity.TOP|Gravity.LEFT, 0, 0);
```

其中第一个参数是方向参数；第二个参数是横轴方向的偏移量；第三个参数是竖轴方向的偏移量。

## 自定义Toast

1. 创建自定义的布局文件

   ``` xml
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                 android:id="@+id/custom_toast_container"
                 android:orientation="horizontal"
                 android:layout_width="fill_parent"
                 android:layout_height="fill_parent"
                 android:padding="8dp"
                 android:background="#DAAA"
                 >
       <ImageView android:src="@drawable/droid"
                  android:layout_width="wrap_content"
                  android:layout_height="wrap_content"
                  android:layout_marginRight="8dp"
                  />
       <TextView android:id="@+id/text"
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:textColor="#FFF"
                 />
   </LinearLayout>
   ```

2. 使用`LayoutInflater`加载布局，并且通过`findViewById()`方法寻找并且设置布局子控件的属性。最后通过`setView()`方法显示自定义Toast。

   ``` java
   LayoutInflater inflater = getLayoutInflater();
   View layout = inflater.inflate(R.layout.custom_toast,
                   (ViewGroup) findViewById(R.id.custom_toast_container));

   TextView text = (TextView) layout.findViewById(R.id.text);
   text.setText("This is a custom toast");

   Toast toast = new Toast(getApplicationContext());
   toast.setGravity(Gravity.CENTER_VERTICAL, 0, 0);
   toast.setDuration(Toast.LENGTH_LONG);
   toast.setView(layout);
   toast.show();
   ```

   ​