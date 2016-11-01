## 属性动画

Android属性动画是Android 3.0之后添加的一个非常强大的动画。与视图动画不同的地方在于，属性动画能够真正的改变View的属性，并且可以对自定义属性进行操作。

### 在xml中定义

* 文件位置：res/animator/filename.xml
* 引用资源：
  * Java代码中:`R.animator.filename`
  * XML中:`@[package:]animator/filename`

根元素需要为\<set\>, \<objectAnimator\>, 或者 \<valueAnimator\>，对应关系为：
* ValueAnimator - \<animator\>
* ObjectAnimator - \<objectAnimator\>
* AnimatorSet - \<set\>
  `ObjectAnimator`继承自`ValueAnimator`，而`AnimatorSet`是动画集合。

``` xml
<set
  android:ordering=["together" | "sequentially"]>

    <objectAnimator
        android:propertyName="string"
        android:duration="int"
        android:valueFrom="float | int | color"
        android:valueTo="float | int | color"
        android:startOffset="int"
        android:repeatCount="int"
        android:repeatMode=["repeat" | "reverse"]
        android:valueType=["intType" | "floatType"]/>

    <animator
        android:duration="int"
        android:valueFrom="float | int | color"
        android:valueTo="float | int | color"
        android:startOffset="int"
        android:repeatCount="int"
        android:repeatMode=["repeat" | "reverse"]
        android:valueType=["intType" | "floatType"]/>

    <set>
        ...
    </set>
</set>
```

`android:propertyName` ：表示属性动画的作用对象的属性的名称；

`android:duration` ：表示动画的时长；

`android:valueFrom` ：表示属性的起始值；

`android:valueTo` ：表示属性的结束值；

`android:startOffset` ：表示动画的延迟时间；

`android:repeatCount` ：表示动画的重复次数；默认值为0，此值为-1时表示无限循环；

`android:repeatMode` ：表示动画的重复模式；

`android:valueType` ：表示`android:propertyName`所指定的属性的类型。如果`android:propertyName`所指定的属性表示的是颜色，则不需要指定`android:valueType` 。

在Java代码中使用该动画：

``` java
AnimatorSet set = (AnimatorSet) AnimatorInflater.loadAnimator(myContext,
    R.anim.property_animator);
set.setTarget(myObject);
set.start();
```

### 使用ValueAnimator

直接使用`ValueAnimator`不产生任何动画效果，通常需要监听动画过程，在`onAnimationUpdate()`方法中利用`getAnimatedValue()`方法获取值来操作对象的属性，以实现动画效果。

可以通过`ValueAnimator`的工厂方法`ofInt()` 、`ofFloat()` 、`ofObject()` 来获得它的实例。

``` java
ValueAnimator animation = ValueAnimator.ofFloat(0f, 1f);
animation.setDuration(1000);
animation.start();
```

还可以定义一个设置自定义类型的动画：

``` java
ValueAnimator animation = ValueAnimator.ofObject(new MyTypeEvaluator(), startPropertyValue, endPropertyValue);
animation.setDuration(1000);
animation.start();
```

### 使用ObjectAnimator

`ObjectAnimator`是`ValueAnimator`的子类。

可以通过`ObjectAnimator`的工厂方法`ofInt()` 、`ofFloat()` 、`ofArgb()` 来获得它的实例。

``` java
ObjectAnimator anim = ObjectAnimator.ofFloat(foo, "alpha", 0f, 1f);
anim.setDuration(1000);
anim.start();
```

如果想让属性动画正确地修改属性来达成动画的效果，需要满足以下条件：

1. object属性必须要提供setter方法，方法的名字形式为`setAbc()`。
   * 可以的话，给对象添加setter以及getter方法
   * 用一个类包装原始对象，间接为其提供getter方法
   * 使用`valueAnimator`类，监听动画过程并且实现属性的改变
2. 如果在实现动画的时候没有传递初始值，那么还要提供getter方法，方法的名字形式为`getAbc()`。
3. getter方法以及setter方法必须作用于指定给`ObjectAnimator`的属性上。
4. 需要在`onAnimationUpdate()`回调方法中调用`invalidate()`方法来重绘视图达到动画的效果。View中的常用属性会自动调用`invalidate()`方法，不需要手动调用该方法。

#### 常用属性

- translationX 和 translationY : 控制View距离左边和顶部的距离的增加值。是一个相对值。
- rotation 、 rotationX 和 rotationY : rotation 是控制View围绕其支点进行旋转。 rotationX 和 rotationY 分别是围绕X轴和Y轴旋转。
- scaleX 和 scaleY : 控制View的缩放。
- pivotX 和 pivotY : 控制View的支点位置，进行旋转和缩放，默认是View的中点。它们都是 float 值， 0 表示View的最左边和最顶端， 1 表示最右端和最下端。
- alpha : 控制View的透明度。
- x 和 y : 控制View在布局容器中距离左边和顶部的距离。是一个绝对值。

### 实现动画的协同效果

#### 使用AnimatorSet

`AnimatorSet`可以通过`playSequentially()` ，`playTogether()`来控制动画的串行和并行。通过`set.play().with()`、`before()`、`after()`等方法可以实现动画的多种协同效果。

``` java
AnimatorSet bouncer = new AnimatorSet();
bouncer.play(bounceAnim).before(squashAnim1);
bouncer.play(squashAnim1).with(squashAnim2);
bouncer.play(squashAnim1).with(stretchAnim1);
bouncer.play(squashAnim1).with(stretchAnim2);
bouncer.play(bounceBackAnim).after(stretchAnim2);
ValueAnimator fadeAnim = ObjectAnimator.ofFloat(newBall, "alpha", 1f, 0f);
fadeAnim.setDuration(250);
AnimatorSet animatorSet = new AnimatorSet();
animatorSet.play(bouncer).before(fadeAnim);
animatorSet.start();
```

#### 使用PropertyValuesHolder

```java
PropertyValuesHolder pvhX = PropertyValuesHolder.ofFloat("x", 50f);
PropertyValuesHolder pvhY = PropertyValuesHolder.ofFloat("y", 100f);
ObjectAnimator.ofPropertyValuesHolder(myView, pvhX, pvyY).start();
```

#### 使用ViewPropertyAnimator

`ViewPropertyAnimator`类提供了链式调用进行多个属性同时变化的动画，更加简洁方便操作组合动画。

在动画进行的时候只对多个属性的变化进行一次invalidate调用，而不是对变化每个属性进行调用。

每个属性方法都有两种调用形式，例如`alpha(float value)`和`alphaBy(float value)`，前者是变化到多少，后者是变化多少。

这个类没提供公开的构造方法，通过调用view的`animate()`获取引用。

``` java
imageView.animate()
          .setDuration(4000)
          .rotationY(45f)
          .translationX(imageView.getWidth())
          .alpha(0f);
```

### 属性动画的监听器

可以使用监听器来监听属性动画的动画过程，主要有以下两个接口：

* `Animator.AnimatorListener`

  有以下回调方法：

  * `onAnimationStart()` ：动画开始时调用；
  * `onAnimationEnd()` ：动画结束时调用；
  * `onAnimationRepeat()` ：动画重复时调用；
  * `onAnimationCancel()` ：动画被取消时调用，同时也会调用`onAnimationEnd()` 。

  如果只想实现其中的一个方法，可以使用`AnimatorListenerAdapter`接口。

  ``` java
  ValueAnimator fadeAnim = ObjectAnimator.ofFloat(newBall, "alpha", 1f, 0f);
  fadeAnim.setDuration(250);
  fadeAnim.addListener(new AnimatorListenerAdapter() {
  public void onAnimationEnd(Animator animation) {
      balls.remove(((ObjectAnimator)animation).getTarget());
  }
  ```

* `ValueAnimator.AnimatorUpdateListener`

  有以下回调方法：

  * `onAnimationUpdate()` ，`onAnimationUpdate()`方法在每一帧都会被调用。

### 使用TypeEvaluator

`TypeEvaluator`的作用是根据当前属性改变的百分比来计算改变后的属性值。

Android系统预置的有`IntEvaluator`（针对整型属性）、`FloatEvaluator`（针对浮点型属性）、`ArgbEvaluator`（针对Color属性）。

如果要使属性动画作用于其它类型的属性，则需要继承`TypeEvaluator`类并且实现`evaluate()`方法：

``` java
public class FloatEvaluator implements TypeEvaluator {

    public Object evaluate(float fraction, Object startValue, Object endValue) {
        float startFloat = ((Number) startValue).floatValue();
        return startFloat + fraction * (((Number) endValue).floatValue() - startFloat);
    }
}
```

### 使用Interpolators

`TimeInterpolator`的作用是根据时间流逝的百分比来计算出当前属性改变的百分比。

Android系统预置的有：

* `AccelerateDecelerateInterpolator` ：先加速后减速
* `AccelerateInterpolator` ：加速
* `AnticipateInterpolator` ：先向相反方向改变一段再加速播放
* `AnticipateOvershootInterpolator` ：先向相反方向改变，再加速播放，会超出目标值然后缓慢移动至目标值
* `BounceInterpolator` ：快到目标值时值会跳跃
* `CycleInterpolator` ：动画循环一定次数，值的改变为一正弦函数
* `DecelerateInterpolator` ：减速
* `LinearInterpolator` ：线性均匀改变
* `OvershootInterpolator` ：最后超出目标值然后缓慢改变到目标值

如果要自定义插值器，需要继承`TimeInterpolator`类并且重写`getInterpolation()`方法。

### 通过KeyFrame定义动画

可以定义除了开始和结束以外的关键帧。KeyFrame是抽象类，要通过`ofInt()` ，`ofFloat()` ，`ofObject()`方法获得适当的KeyFrame，然后通过`PropertyValuesHolder.ofKeyframe()`获得PropertyValuesHolder对象

``` java
Keyframe kf0 = Keyframe.ofFloat(0f, 0f);
Keyframe kf1 = Keyframe.ofFloat(.5f, 360f);
Keyframe kf2 = Keyframe.ofFloat(1f, 0f);
PropertyValuesHolder pvhRotation = PropertyValuesHolder.ofKeyframe("rotation", kf0, kf1, kf2);
ObjectAnimator rotationAnim = ObjectAnimator.ofPropertyValuesHolder(target, pvhRotation)
rotationAnim.setDuration(5000ms);
```

### 容器布局动画

可以用`LayoutTransition`对 ViewGroup中的View进行动画设置显示。`LayoutTransition`的动画效果都是设置给ViewGroup，当你添加或者移除ViewGroup中的View时，或者你调用View的`setVisibility()`方法来控制其显示或消失时，会体现动画。

LayoutTransition类中主要有五种容器转换动画类：

- `LayoutTransition.APPEARING`：当View出现或者添加的时候View出现的动画。
- `LayoutTransition.CHANGE_APPEARING`：当添加View导致布局容器改变的时候其他项目变化的动画。
- `LayoutTransition.DISAPPEARING`：当View消失或者隐藏的时候View消失的动画。
- `LayoutTransition.CHANGE_DISAPPEARING`：当删除或者隐藏View导致布局容器改变的时候其他项目变化的动画。

#### XML方式使用**LayoutTransition动画**

可以通过如下方式使用系统提供的默认ViewGroup的LayoutTransition动画：

``` xml
android:animateLayoutChanges="true"
```

当调用ViewGroup的addView、removeView方法时就能看见系统默认的动画效果。

还可以使用自定义的动画效果：

``` xml
android:layoutAnimation="@anim/customer_anim"
```

#### Java代码中使用LayoutTransition动画

``` java
mTransitioner = new LayoutTransition();
ObjectAnimator anim = ObjectAnimator.ofFloat(this, "scaleX", 0, 1);
...
mTransition.setAnimator(LayoutTransition.APPEARING, anim);
...             
mViewGroup.setLayoutTransition(mTransitioner);
```





