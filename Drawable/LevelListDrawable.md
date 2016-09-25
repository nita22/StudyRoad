## LevelListDrawable
LevelListDrawable表示一个Drawable集合，集合中的每个Drawable都有一个level，根据不同的level数值，LevelListDrawable会切换为对应的Drawable。<br>
每个item表示一个Drawable，而且有对应的等级范围，由`android:minLevel`和`android:maxLevel`指定，可以通过`setLevel`方法或者`setImageLevel()`方法来设置不同的等级从而切换对应的Drawable。<br>

* 文件位置：res/drawable/filename.xml
* 引用Drawable：
  * Java代码中引用：`R.drawable.filename`
  * XML代码中引用：`@[package:]drawable/filename`
  
``` xml
<level-list
    xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@drawable/drawable_resource"
        android:maxLevel="integer"
        android:minLevel="integer" />
</level-list>
```

Drawable的等级是有范围的，0 ~ 10000，最小等级是0（默认值也为0），最大等级是10000。

范例：
``` xml
<level-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@drawable/status_off"
        android:maxLevel="0" />
    <item
        android:drawable="@drawable/status_on"
        android:maxLevel="1" />
</level-list>
```
