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

范例：
``` xml
<nine-patch xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@drawable/myninepatch"
    android:dither="false" />
```
