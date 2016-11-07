## VectorDrawable

`VectorDrawable`可以让我们在XML中创建静态的SVG图形。

每一个path代表着SVG图形的绘制轮廓，而group代表着图形变换。可以通过group对不同的path进行组合。

![](https://github.com/nita22/StudyRoad/blob/master/Res/Pic/vector.png?raw=true)

``` xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    <!-- intrinsic size of the drawable -->
    android:height="24dp"
    android:width="24dp"
    <!-- size of the virtual canvas -->
    android:viewportWidth="24.0"
    android:viewportHeight="24.0">
   <group
         android:name="rotationGroup"
         android:pivotX="10.0"
         android:pivotY="10.0"
         android:rotation="15.0" >
      <path
        android:name="vect"
        android:fillColor="#FF000000"
        android:pathData="M15.67,4H14V2h-4v2H8.33C7.6,4 7,4.6 7,5.33V9h4.93L13,7v2h4V5.33C17,4.6 16.4,4 15.67,4z"
        android:fillAlpha=".3"/>
      <path
        android:name="draw"
        android:fillColor="#FF000000"
        android:pathData="M13,12.5h2L11,20v-5.5H9L11.93,9H7v11.67C7,21.4 7.6,22 8.33,22h7.33c0.74,0 1.34,-0.6 1.34,-1.33V9h-4v3.5z"/>
   </group>
</vector>
```

`android:height` ：SVG图形的高度；

`android:width` ：SVG图形的宽度；

`android:viewportHeight` ：SVG图形高度划分的比例；

`android:viewportWidth` ：SVG图形宽度划分的比例；

**height、width的比例与viewportHeight、viewportWidth的比例必须保持一致，不然图形会出现形变。**



### \<path>标签支持的常用SVG指令

**坐标轴以（0,0）为中心，X轴水平向右，Y轴水平向下；**

**大写代表绝对定位，小写代表相对定位；**

**指令跟数据之间的空格可以忽略；**



* M X,Y ：将画笔移动到指定的坐标位置；
* L X,Y ：画直线到指定的坐标位置；
* H X ：画水平线到指定的X坐标位置；
* V Y ：画垂直线到指定的Y坐标位置；
* A RX,RY,XROTATION,FLAG1,FLAG2,X,Y ：画弧线（RX,RY指椭圆的半轴大小；XROTATION指椭圆的X轴与水平方向顺时针方向夹角；FLAG1为1表示大角度弧线，为0表示小角度弧线；FLAG2表示从起点到终点的方向，值为1表示顺时针，值为0表示逆时针）；
* Z ：关闭路径；



## AnimatedVectorDrawable

`AnimatedVectorDrawable`可以给`VectorDrawable`提供动画效果。

要使用`AnimatedVectorDrawable` ，可以使用多个XML文件来实现SVG图形的动画效果，也可以使用单个XML文件来实现动画效果。

#### 多个XML文件实现动画效果

**`AnimatedVectorDrawable`中指定的target的name属性，必须与`VectorDrawable`中需要作用的name属性保持一致。**

**`objectAnimator`中通过指定`android:propertyName`属性来选择控制的属性，如果指定的属性为pathData，需要添加`android:valueType="pathType"`属性。**

* `VectorDrawable`XML文件

  ``` xml
  <vector xmlns:android="http://schemas.android.com/apk/res/android"
     android:height="64dp"
     android:width="64dp"
     android:viewportHeight="600"
     android:viewportWidth="600" >
     <group
        android:name="rotationGroup"
        android:pivotX="300.0"
        android:pivotY="300.0"
        android:rotation="45.0" >
        <path
           android:name="vectorPath"
           android:fillColor="#000000"
           android:pathData="M300,70 l 0,-70 70,70 0,0 -70,70z" />
     </group>
  </vector>
  ```

* `AnimatedVectorDrawable`XML文件

  ``` xml
  <animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
     android:drawable="@drawable/vd" >
       <target
           android:name="rotationGroup"
           android:animation="@anim/rotation" />
       <target
           android:name="vectorPath"
           android:animation="@anim/path_morph" />
  </animated-vector>
  ```

* 动画XML文件

  ``` xml
  <objectAnimator
     android:duration="6000"
     android:propertyName="rotation"
     android:valueFrom="0"
     android:valueTo="360" />
  ```

  ``` xml
  <set xmlns:android="http://schemas.android.com/apk/res/android">
     <objectAnimator
        android:duration="3000"
        android:propertyName="pathData"
        android:valueFrom="M300,70 l 0,-70 70,70 0,0   -70,70z"
        android:valueTo="M300,70 l 0,-70 70,0  0,140 -70,0 z"
        android:valueType="pathType"/>
  </set>
  ```

### 单个XML文件实现动画效果

``` xml
<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:aapt="http://schemas.android.com/aapt">
    <aapt:attr name="android:drawable">
        <vector
            android:width="24dp"
            android:height="24dp"
            android:viewportWidth="24"
            android:viewportHeight="24">
            <path
                android:name="root"
                android:strokeWidth="2"
                android:strokeLineCap="square"
                android:strokeColor="?android:colorControlNormal"
                android:pathData="M4.8,13.4 L9,17.6 M10.4,16.2 L19.6,7" />
        </vector>
    </aapt:attr>
    <target android:name="root">
        <aapt:attr name="android:animation">
            <objectAnimator
                android:propertyName="pathData"
                android:valueFrom="M4.8,13.4 L9,17.6 M10.4,16.2 L19.6,7"
                android:valueTo="M6.4,6.4 L17.6,17.6 M6.4,17.6 L17.6,6.4"
                android:duration="300"
                android:interpolator="@android:interpolator/fast_out_slow_in"
                android:valueType="pathType" />
        </aapt:attr>
    </target>
</animated-vector>
```

