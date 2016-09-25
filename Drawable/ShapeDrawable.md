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

当`android:shape="ring"`时，\<shape\>标签内可以使用以下属性：<br>
`android:innerRadius`：圆环的内半径<br>
`android:innerRadiusRatio`：内半径占整个Drawable宽度的比例，能被`android:innerRadius`属性覆盖<br>
`android:thickness`：圆环的厚度<br>
`android:thicknessRatio`：厚度占整个Drawable宽度的比例，能被`android:thickness`属性覆盖<br>
`android:useLevel`：如果被当作LevelListDrawable则设为true，一般设为false<br>
<br>
当`android:shape="rectangle"`时，可以包含\<corners\>标签：<br>
`android:radius`：为四个角同时设定相同的角度<br>
`android:topLeftRadius`：左上角的角度<br>
`android:topRightRadius`：右上角的角度<br>
`android:bottomLeftRadius`：左下角的角度<br>
`android:bottomRightRadius`：右下角的角度<br>
<br>

\<gradient\>标签与\<solid\>标签是互相排斥的。<br>
`android:angle`：渐变的角度<br>
`android:centerX`：渐变的中心点的横坐标<br>
`android:centerY`：渐变的中心点的纵坐标<br>
`android:centerColor`：渐变的中间色<br>
`android:endColor`：渐变的结束色<br>
`android:gradientRadius`：渐变半径，仅当`android:type="radial"`时有效<br>
`android:startColor`：渐变的开始色<br>
`android:type`：渐变的类型，有linear（线性渐变）、radial（径向渐变）、sweep（扫描线渐变）。默认值为linear<br>
`android:useLevel`：一般为false，当Drawable作为StateListDrawable使用时为true<br>
<br>

\<padding\>标签的属性：<br>
`android:left`：左边距<br>
`android:top`:上边距<br>
`android:right`：右边距<br>
`android:bottom`：底边距<br>
<br>

\<size\>标签的属性：<br>
`android:height`：图形的高度<br>
`android:width`：图形的宽度<br>
<br>

\<solid\>标签的属性：<br>
`android:color`：指定shape中填充的颜色<br>
<br>

\<stroke\>标签的属性：<br>
`android:width`：描边的宽度<br>
`android:color`：描边的颜色<br>
`android:dashGap`：组成虚线的线段之间的间隔<br>
`android:dashWidth`：组成虚线的线段的宽度<br>

范例：
``` xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <gradient
        android:startColor="#FFFF0000"
        android:endColor="#80FF00FF"
        android:angle="45"/>
    <padding android:left="7dp"
        android:top="7dp"
        android:right="7dp"
        android:bottom="7dp" />
    <corners android:radius="8dp" />
</shape>
```

Java代码中引用ShapeDrawable：
``` java
Resources res = getResources();
Drawable shape = res. getDrawable(R.drawable.gradient_box);

TextView tv = (TextView)findViewByID(R.id.textview);
tv.setBackground(shape);
```
