## InsetDrawable
InsetDrawable可以将其它Drawable内嵌到自己当中，并可以在四周留出一定的间距。<br>

* 文件位置：res/drawable/filename.xml
* 引用资源：
  * Java代码中引用：`R.drawable.filename`
  * XML代码中引用：`@[package:]drawable/filename`
  
``` xml
<inset
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/drawable_resource"
    android:insetTop="dimension"
    android:insetRight="dimension"
    android:insetBottom="dimension"
    android:insetLeft="dimension" />
```

`android:insetTop`：顶部内凹的大小<br>
`android:insetRight`：右边内凹的大小<br>
`android:insetBottom`：底部内凹的大小<br>
`android:insetLeft`：左边内凹的大小<br>

范例：
``` xml
<inset xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/background"
    android:insetTop="10dp"
    android:insetLeft="10dp" />
```
