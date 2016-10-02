## TransitionDrawable
TransitionDrawable用于实现两个Drawable之间的淡入淡出效果。如果要向前实现效果，需要调用`startTransition()`方法；如果想反向实现效果，而需要调用`reverseTransition()`方法。<br>

* 文件位置：res/drawable/filename.xml
* 引用资源：
  * Java代码中引用：`R.drawable.filename`
  * XML代码中引用：`@[package:]drawable/filename`
  
``` xml
<transition
xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@[package:]drawable/drawable_resource"
        android:id="@[+][package:]id/resource_name"
        android:top="dimension"
        android:right="dimension"
        android:bottom="dimension"
        android:left="dimension" />
</transition>
```

`android:top`：Drawable相对于View的顶部的偏移量<br>
`android:bottom`：Drawable相对于View的底部的偏移量<br>
`android:left`：Drawable相对于View的左边的偏移量<br>
`android:right`：Drawable相对于View的右边的偏移量<br>

范例：
``` xml
<transition xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/on" />
    <item android:drawable="@drawable/off" />
</transition>
```

Java代码中实现效果：
``` java
TransitionDrawable drawable = (TransitionDrawable) button.getDrawable();
drawable.startTransition(500);
```
