# View动画

### 在xml中定义
* 文件位置：res/anim/filename.xml
* 引用资源：
  * Java代码中:`R.anim.filename`
  * XML中: `@[package:]anim/filename`

根元素需要为\<alpha\>，\<scale\>， \<translate\>， \<rotate\> 或者\<set\>。

\<alpha\> -- `AlphaAnimation`      改变View的透明度

\<scale\> -- `ScaleAnimation`       放大或缩小View

 \<translate\> -- `TranslateAnimation`        移动View

 \<rotate\>  --  `RotateAnimation`                 旋转View



``` xml
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@[package:]anim/interpolator_resource"
    android:shareInterpolator=["true" | "false"] >
    <alpha
        android:fromAlpha="float"
        android:toAlpha="float" />
    <scale
        android:fromXScale="float"
        android:toXScale="float"
        android:fromYScale="float"
        android:toYScale="float"
        android:pivotX="float"
        android:pivotY="float" />
    <translate
        android:fromXDelta="float"
        android:toXDelta="float"
        android:fromYDelta="float"
        android:toYDelta="float" />
    <rotate
        android:fromDegrees="float"
        android:toDegrees="float"
        android:pivotX="float"
        android:pivotY="float" />
    <set>
        ...
    </set>
</set>
```

`android:interpolator` ：表示动画集合所采用的插值器。默认值为`@android:anim/accelerate_decelerate_interpolator` 。

`android:shareInterpolator` ：表示集合中的动画是否共享同一个插值器。

`android:duration` ：动画的持续时间。

`android:fillAfter` ：动画结束之后View是否停留在结束位置。



\<alpha\>标签的属性有：

`android:fromAlpha` ：表示透明度的起始值。

`android:toAlpha` ：表示透明度的结束值。



\<scale\>标签的属性有：

`android:fromXScale` ：水平方向缩放的起始值。

`android:toXScale` ：水平方向缩放的结束值。

`android:fromYScale` ：竖直方向缩放的起始值。

`android:toYScale` ：竖直方向缩放的结束值。

`android:pivotX` ：缩放的中心点的x坐标。

`android:pivotY` ：缩放的中心点的y坐标。



\<translate\>标签的属性有：

`android:fromXDelta` ：表示x的初始值。

`android:toXDelta` ：表示x的结束值。

`android:fromYDelta` ：表示y的初始值。

`android:toYDelta` ：表示y的结束值。



 \<rotate\>标签的属性有：

`android:fromDegrees` ：旋转开始的角度。

`android:toDegrees` ：旋转结束的角度。

`android:pivotX` ：旋转的中心点的x坐标。

`android:pivotY` ：旋转的中心点的y坐标。




### 插值器
Android提供的插值器都为`Interpolator`类的子类。
![](https://github.com/nita22/StudyRoad/blob/master/Res/Pic/interpolator.png?raw=true)

需要通过`android:interpolator`属性来引用插值器。

``` xml
<set android:interpolator="@android:anim/accelerate_interpolator">
    ...
</set>
```

#### 自定义插值器
* 文件位置：res/anim/filename.xml
* 在XML中引用插值器：`@[package:]anim/filename`

可以在Android提供的插值器的基础上修改参数来实现自定义插值器
``` xml
<InterpolatorName xmlns:android="http://schemas.android.com/apk/res/android"
    android:attribute_name="value"
    />
```

#### 使用View动画

``` java
Animation hyperspaceJump = AnimationUtils.loadAnimation(this, R.anim.hyperspace_jump);
image.startAnimation(hyperspaceJump);
```

### 在代码中定义动画

``` java
AlphaAnimation alphaAnimation = new AlphaAnimation(0,1);
alphaAnimation.setDuration(300);
mButton.setAnimation(alphaAnimation);
```

