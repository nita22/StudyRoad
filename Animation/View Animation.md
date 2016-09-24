# View动画

## 补间动画
### 在xml中定义
* 文件位置：res/anim/filename.xml
* 引用资源：
  * Java代码中:`R.anim.filename`
  * XML中: `@[package:]anim/filename`

根元素需要为\<alpha\>,\<scale\>, \<translate\>, \<rotate\>, 或者\<set\>
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

### Java代码中使用动画
``` java
Animation hyperspaceJump = AnimationUtils.loadAnimation(this, R.anim.hyperspace_jump);
image.startAnimation(hyperspaceJump);
```

### 插值器
Android提供的插值器都为`Interpolator`类的子类。
![](https://github.com/nita22/StudyRoad/blob/master/Res/Pic/interpolator.png?raw=true)
<br>
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

## 帧动画
### 在xml文件中定义
* 文件位置：res/drawable/filename.xml
* 引用帧动画：
  * 在Java代码中：`R.drawable.filename`
  * 在XML中：`@[package:]drawable.filename`

``` xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot=["true" | "false"] >
    <item
        android:drawable="@[package:]drawable/drawable_resource_name"
        android:duration="integer" />
</animation-list>
```

### 在Java代码中使用帧动画
``` java
ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
rocketImage.setBackgroundResource(R.drawable.rocket_thrust);

rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
rocketAnimation.start();
```
