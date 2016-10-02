## Nine-Patch

### Nine-Patch文件
* 文件位置：res/drawable/filename.9.png
* 引用资源：
  * 在Java代码中引用：`R.drawable.filename`
  * 在XML代码中引用：`@[package:]drawable/filename`
  
### 在XML中定义的Nine-Patch
* 文件位置：res/drawable/filename.xml
* 引用资源：
  * 在Java代码中引用：`R.drawable.filename`
  * 在XML代码中引用：`@[package:]drawable/filename`
  
``` xml
<nine-patch
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@[package:]drawable/drawable_resource"
    android:dither=["true" | "false"] />
```

`android:dither`：true表示，如果一个屏幕中位图有这不同的像素配置，启用位图的抖动。false表示不启用位图的抖动

范例：
``` xml
<nine-patch xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@drawable/myninepatch"
    android:dither="false" />
```
