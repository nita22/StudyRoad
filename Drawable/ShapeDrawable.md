## ShapeDrawable
\<shape\>标签创建的Drawable，其实体类实际上是GradientDrawable。<br>

* 文件位置：res/drawable/filename.xml
* 引用Drawable：
  * Java代码中引用：`R.drawable.filename`
  * XML代码中引用：`@[package:]drawable/filename`

``` xml
<shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape=["rectangle" | "oval" | "line" | "ring"] >
    <corners
        android:radius="integer"
        android:topLeftRadius="integer"
        android:topRightRadius="integer"
        android:bottomLeftRadius="integer"
        android:bottomRightRadius="integer" />
    <gradient
        android:angle="integer"
        android:centerX="float"
        android:centerY="float"
        android:centerColor="integer"
        android:endColor="color"
        android:gradientRadius="integer"
        android:startColor="color"
        android:type=["linear" | "radial" | "sweep"]
        android:useLevel=["true" | "false"] />
    <padding
        android:left="integer"
        android:top="integer"
        android:right="integer"
        android:bottom="integer" />
    <size
        android:width="integer"
        android:height="integer" />
    <solid
        android:color="color" />
    <stroke
        android:width="integer"
        android:color="color"
        android:dashWidth="integer"
        android:dashGap="integer" />
</shape>
```

`android:shape`：表示图形的形状，有四个选项：rectangle（矩形）、oval（椭圆）、line（横线）、ring（圆环）<br>

当`android:shape="ring`时，\<shape\>标签内可以使用以下属性：<br>
`android:innerRadius`：圆环的内半径<br>
`android:innerRadiusRatio`：内半径占整个Drawable宽度的比例，能被`android:innerRadius`属性覆盖<br>
`android:thickness`：圆环的厚度<br>
`android:thicknessRatio`：厚度占整个Drawable宽度的比例，能被`android:thickness`属性覆盖<br>
`android:useLevel`：如果被当作LevelListDrawable则设为true，一般设为false<br>
<br>
当`android:shape="rectangle`时，可以包含\<corners\>标签：<br>
`android:radius`：为四个角同时设定相同的角度<br>
`android:topLeftRadius`：左上角的角度<br>
`android:topRightRadius`：右上角的角度<br>
`android:bottomLeftRadius`：左下角的角度<br>
`android:bottomRightRadius`：右下角的角度<br>
<br>

