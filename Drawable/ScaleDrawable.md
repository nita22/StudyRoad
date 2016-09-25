## ScaleDrawable
ScaleDrawable可以根据自己的等级将指定的Drawable缩放到一定比例。<br>

ScaleDrawable的等级默认为0，表示ScaleDrawable不可见，而要想ScaleDrawable可见，需要等级不能为0。 <br>

* 文件位置：res/drawable/filename.xml
* 引用Drawable：
  * Java代码中引用：`R.drawable.filename`
  * XML代码中引用：`@[package:]drawable/filename`
  
``` xml
<scale
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/drawable_resource"
    android:scaleGravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
                          "fill_vertical" | "center_horizontal" | "fill_horizontal" |
                          "center" | "fill" | "clip_vertical" | "clip_horizontal"]
    android:scaleHeight="percentage"
    android:scaleWidth="percentage" />
```

`android:scaleGravity`：指示缩放后的显示位置<br>
`android:scaleHeight`：对指定Drawable高的缩放比例，以百分比的形式表示<br>
`android:scaleWidth`：对指定Drawable宽的缩放比例，以百分比的形式表示<br>

范例：
``` xml
<scale xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/logo"
    android:scaleGravity="center_vertical|center_horizontal"
    android:scaleHeight="80%"
    android:scaleWidth="80%" />
```

Java代码中引用ScaleDrawable：<br>
必须设置ScaleDrawable的等级为大于0且小于等于10000的值。
``` java
View testScale = findViewById(R.id.test_scale);
ScaleDrawable testScaleDrawable = (ScaleDrawable) testScale.getBackground();
testScaleDrawable.setLevel(1);
```
