# Canvas

**Canvas 提供了绘图方法，可以向底层的位图绘制基本图形。**

要创建Canvas，需要先创建底层的位图，再将该bitmap作为参数传入Canvas的构造方法中。

``` java
Bitmap b = Bitmap.createBitmap(100, 100, Bitmap.Config.ARGB_8888);
Canvas c = new Canvas(b);
```

## Canvas的常用方法
`Canvas.save()` ：将之前的所有已绘制图像保存起来；

`Canvas.restore()` ：将在`save()`方法之后绘制的图像与`save()`之前的图像进行合并；

`Canvas.translate()` ：将Canvas移动；

`Canvas.rotate()` ：将Canvas翻转一定角度；



Canvas还提供了一系列draw*方法来实现设计：

`drawRect(float left, float top, float right, float bottom, Paint paint)` ：绘制矩形；

`	drawRoundRect(float left, float top, float right, float bottom, float rx, float ry, Paint paint)` ：绘制圆角矩形；

`drawPath(Path path, Paint paint) ` ：绘制路径；

`drawBitmap(Bitmap bitmap, Rect src, Rect dst, Paint paint)` ：贴图，第一个参数是Bitmap对象，第二个参数是要画的bitmap的区域，第三个参数是要显示在屏幕上的区域，参数四是Paint画刷对象；

`drawLine(float startX, float startY, float stopX, float stopY, Paint paint)` ：绘制线；

`drawPoint(float x, float y, Paint paint)` ：绘制点；

`drawText(String text, float x, float y, Paint paint)`：绘制文本；

`	drawOval(float left, float top, float right, float bottom, Paint paint)` ：绘制椭圆；

`drawCircle(float cx, float cy, float radius, Paint paint)`：绘制圆；

`drawArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean useCenter, Paint paint)` ：绘制弧形、扇形；其中useCenter参数传入true，则绘制扇形，useCenter参数传入false则绘制弧形；

## 在View上绘图

在View上绘图适合处理量比较小，帧率比较小的画图。

在自定义View的`onDraw()`方法中利用Canvas参数来进行绘图。如果想要手动调用`onDraw()`方法，可以使用`invalidate()`方法通知系统进行重新绘制。如果是在非主线程需要重新绘制的话，需要调用`postInvalidate()`方法。

## 在SurfaceView上绘图

在SurfaceView上绘图主要用在游戏，高品质动画方面的画图。

通过SurfaceView的`getHolder()`函数可以获取SurfaceHolder对象。在SurfaceHolder中利用`Canvas.lockCanvas()`方法来获取Canvas对象，通过在Canvas上绘制内容来修改SurfaceView中的数据。一旦绘制完成，调用`unlockCanvasAndPost()`方法并传入绘制好的Canvas。

**从SurfaceHolder中获取到的Canvas对象会保留之前的状态，如果不对该Canvas进行重绘处理而直接在其上面做绘制，也会显示之前的Canvas图像。**



