## 帧动画

Android系统提供了`AnimationDrawable`类来使用帧动画。使用帧动画时应该尽量避免使用过多尺寸较大的图片，以防引起OOM。

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

`android:oneshot` ：该属性表示帧动画是否重复播放。值为true代表只播放一次动画，值为false代表帧动画将无限循环播放。

### 在Java代码中使用帧动画

``` java
ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
rocketImage.setBackgroundResource(R.drawable.rocket_thrust);

rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
rocketAnimation.start();
```