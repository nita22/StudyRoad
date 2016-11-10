# SurfaceView

## SurfaceView 特点

* SurfaceView主要适用于被动频繁更新
* SurfaceView通常会通过一个子线程来刷新页面
* SurfaceView在底层实现机制中实现了双缓冲机制

## 使用SurfaceView

1. 创建自定义的SurfaceView子类，需要继承SurfaceView并且实现SurfaceHolder.Callback接口以及Runnable接口。

   需要实现以下接口方法：

   `surfaceCreated(SurfaceHolder holder)` ：SurfaceView创建时调用；

   `surfaceChanged(SurfaceHolder holder, int format, int width, int height)` ：SurfaceView改变时调用；

   `surfaceDestroyed(SurfaceHolder holder)` ：SurfaceView销毁时调用；

2. 初始化SurfaceHolder，并且将自定义的SurfaceView子类传入到`addCallback()`方法中进行注册：

   ``` java
   mHolder = getHolder();
   mHolder.addCallback(this);
   ```

3. 通过`Canvas.lockCanvas()`方法获取Canvas绘图对象，绘制完成后通过`unlockCanvasAndPost(Canvas canvas)`方法提交绘制画布：

   ​

