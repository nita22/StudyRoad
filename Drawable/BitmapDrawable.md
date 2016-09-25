## Bitmap
Android可以支持的Bitmap类型为：`.png`(推荐), `.jpg`(允许), `.gif`(不鼓励)

### Bitmap文件
* 文件位置：res/drawable/filename.png (.png, .jpg, 或者 .gif)
* 引用Bitmap：
  * 在Java代码中引用：`R.drawable.filename`
  * 在XML中引用：`@[package:]drawable/filename`

### 在XML中定义的Bitmap
* 文件位置：res/drawable/filename.xml
* 引用Bitmap：
 * 在Java代码中引用：`R.drawable.filename`
 * 在XML中引用：`@[package:]drawable/filename`
  
``` xml
<bitmap
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@[package:]drawable/drawable_resource"
    android:antialias=["true" | "false"]
    android:dither=["true" | "false"]
    android:filter=["true" | "false"]
    android:gravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
                      "fill_vertical" | "center_horizontal" | "fill_horizontal" |
                      "center" | "fill" | "clip_vertical" | "clip_horizontal"]
    android:mipMap=["true" | "false"]
    android:tileMode=["disabled" | "clamp" | "repeat" | "mirror"] />
```
 
### 引用Bitmap
* Java代码中引用：
``` java
  Resources res = getResources();
  Drawable drawable = res.getDrawable(R.drawable.myimage);
```
  
* XML代码中引用：
``` xml
 <ImageView
    android:layout_height="wrap_content"
    android:layout_width="wrap_content"
    android:src="@drawable/myimage" />
```
  
