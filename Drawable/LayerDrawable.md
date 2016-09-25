## LayerDrawable
LayerDrawable表示一种层次化的Drawable集合，通过将不同的Drawable放置在不同的层上面从而达到一种叠加后的效果。最后一个\<item\>标签中的Drawable将会被放置在顶层。<br>
\<item\>标签中可以嵌套\<bitmap\>。 <br>
* 文件位置：res/drawable/filename.xml
* 引用资源：
  * 在Java代码中引用：`R.drawable.filename`
  * 在XML代码中引用：`@[package:]drawable/filename`
 
``` xml
<layer-list
    xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@[package:]drawable/drawable_resource"
        android:id="@[+][package:]id/resource_name"
        android:top="dimension"
        android:right="dimension"
        android:bottom="dimension"
        android:left="dimension" />
</layer-list>
```

范例：
``` xml
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
      <bitmap android:src="@drawable/android_red"
        android:gravity="center" />
    </item>
    <item android:top="10dp" android:left="10dp">
      <bitmap android:src="@drawable/android_green"
        android:gravity="center" />
    </item>
    <item android:top="20dp" android:left="20dp">
      <bitmap android:src="@drawable/android_blue"
        android:gravity="center" />
    </item>
</layer-list>
```
