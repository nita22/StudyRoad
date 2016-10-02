## ClipDrawable
ClipDrawable可以根据自己当前的等级来裁剪另一个Drawable，裁剪大小根据等级来控制，裁剪方向可以通过`android:clipOrientation`和`android:gravity`两个属性来共同控制。<br>

Drawable的最小等级是0，最大等级是10000。`setLevel()`方法中设置等级0表示完全裁剪，而设置等级为10000表示不裁剪。<br>
* 文件位置：res/drawable/filename.xml
* 引用Drawable：
  * Java代码中引用：`R.drawable.filename`
  * XML代码中引用：`@[package:]drawable/filename`
  
``` xml
<clip
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/drawable_resource"
    android:clipOrientation=["horizontal" | "vertical"]
    android:gravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
                     "fill_vertical" | "center_horizontal" | "fill_horizontal" |
                     "center" | "fill" | "clip_vertical" | "clip_horizontal"] />
```

`android:clipOrientation`：裁剪方向<br>
`android:gravity`：指明裁剪的部分，需要跟`android:clipOrientation`一起使用才能发挥作用<br>

范例：
``` xml
<clip xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/android"
    android:clipOrientation="horizontal"
    android:gravity="left" />
```

Java代码中使用ClipDrawable：
``` java
ImageView imageview = (ImageView) findViewById(R.id.image);
ClipDrawable drawable = (ClipDrawable) imageview.getDrawable();
drawable.setLevel(drawable.getLevel() + 1000);
```
