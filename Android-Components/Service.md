## Service基础知识
服务在其托管进程的主线程中运行，它既不创建自己的线程，也不在单独的进程中运行（除非另行指定）。 这意味着，如果服务将执行任何 CPU 密集型工作或阻止性操作，则应在服务内创建新线程来完成这项工作。

### 使用清单文件声明服务
``` xml
<application ... >
      <service android:name=".ExampleService" />
      ...
 </application>
 ```
 通过添加`android:exported`属性并将其设置为 "false"，确保服务仅适用于您的应用,这可以有效阻止其他应用启动您的服务。
### 创建启动Service
可以扩展两个类来创建启动服务：
* 扩展 IntentService 类<br>
IntentService 执行以下操作：<br>
  * 创建默认的工作线程，用于在应用的主线程外执行传递给`onStartCommand()`的所有 Intent
  * 创建工作队列，用于将一个 Intent 逐一传递给`onHandleIntent()`实现，这样您就永远不必担心多线程问题
  * 在处理完所有启动请求后停止服务，因此您永远不必调用`stopSelf()`
  * 提供`onBind()`的默认实现（返回 null）
  * 提供`onStartCommand()`的默认实现，可将 Intent 依次发送到工作队列和`onHandleIntent()`实现<br>
  
扩展 IntentService 类只需要一个构造函数和一个`onHandleIntent()`实现即可<br>

如果您决定还重写其他回调方法（如`onCreate()`、`onStartCommand()` 或`onDestroy()`）,请确保调用超类实现，以便 IntentService 能够妥善处理工作线程的生命周期。除`onHandleIntent()`之外，您无需从中调用超类的唯一方法就是`onBind()`（仅当服务允许绑定时，才需要实现该方法）。
* 扩展Service类<br><br>
需要自行处理对`onStartCommand()`的每次调用<br><br>
`onStartCommand()`方法必须返回整型数。整型数是一个值，用于描述系统应该如何在服务终止的情况下继续运行服务。从`onStartCommand()`返回的值必须是以下常量之一：<br><br>
  * `START_NOT_STICKY`:如果系统在`onStartCommand()`返回后终止服务，则除非有挂起 Intent 要传递，否则系统不会重建服务。
  * `START_STICKY`:如果系统在`onStartCommand()`返回后终止服务，则会重建服务并调用`onStartCommand()`，但绝对不会重新传递最后一个 Intent。相反，除非有挂起 Intent 要启动服务（在这种情况下，将传递这些 Intent ），否则系统会通过空 Intent 调用`onStartCommand()`。
  * `START_REDELIVER_INTENT`:如果系统在`onStartCommand()`返回后终止服务，则会重建服务，并通过传递给服务的最后一个 Intent 调用`onStartCommand()`。任何挂起 Intent 均依次传递。

### 启动Service
可以通过将 Intent（指定要启动的服务）传递给`startService()`，从 Activity 或其他应用组件启动服务。Android 系统调用服务的`onStartCommand()`方法，并向其传递 Intent。
``` java 
Intent intent = new Intent(this, HelloService.class);
startService(intent);
```
`startService()`方法将立即返回，且 Android 系统调用服务的`onStartCommand()`方法。如果服务尚未运行，则系统会先调用`onCreate()`，然后再调用`onStartCommand()`。<br>

如果服务亦未提供绑定，则使用`startService()`传递的 Intent 是应用组件与服务之间唯一的通信模式。但是，如果您希望服务返回结果，则启动服务的客户端可以为广播创建一个`PendingIntent`（使用`getBroadcast()`），并通过启动服务的 Intent 传递给服务。然后，服务就可以使用广播传递结果。<br>

多个服务启动请求会导致多次对服务的`onStartCommand()`进行相应的调用。但是，要停止服务，只需一个服务停止请求（使用`stopSelf()`或`stopService()`）即可。

### 停止Service
除非系统必须回收内存资源，否则系统不会停止或销毁服务，而且服务在`onStartCommand()`返回后会继续运行。因此，服务必须通过调用`stopSelf()`自行停止运行，或者由另一个组件通过调用`stopService()`来停止它。
<br><br>
一旦请求使用`stopSelf()`或`stopService()`停止服务，系统就会尽快销毁服务。
<br><br>
但是，如果服务同时处理多个`onStartCommand()`请求，则您不应在处理完一个启动请求之后停止服务，因为您可能已经收到了新的启动请求（在第一个请求结束时停止服务会终止第二个请求）。为了避免这一问题，您可以调用`stopSelf(int)`时，传递与停止请求的 ID 对应的启动请求的 ID（传递给`onStartCommand()`的 startId） 。然后，如果在您能够调用`stopSelf(int)`之前服务收到了新的启动请求， ID 就不匹配，服务也就不会停止。

### 创建绑定Service
要创建绑定服务，必须实现`onBind()`回调方法以返回`IBinder`，用于定义与服务通信的接口。然后，其他应用组件可以调用`bindService()` 来检索该接口，并开始对服务调用方法。服务只用于与其绑定的应用组件，因此如果没有组件绑定到服务，则系统会销毁服务（您不必按通过 `onStartCommand()`启动的服务那样来停止绑定服务）。<br>

要创建绑定服务，首先必须定义指定客户端如何与服务通信的接口。 服务与客户端之间的这个接口必须是`IBinder`的实现，并且服务必须从`onBind()` 回调方法返回它。一旦客户端收到`IBinder`，即可开始通过该接口与服务进行交互。

多个客户端可以同时绑定到服务。客户端完成与服务的交互后，会调用`unbindService()`取消绑定。一旦没有客户端绑定到该服务，系统就会销毁它。

### 前台服务
要请求让服务运行于前台，请调用`startForeground()`。此方法取两个参数：唯一标识通知的整型数(整型 ID 不得为 0)和状态栏的`Notification`。
``` java
startForeground(ONGOING_NOTIFICATION_ID, notification);
```
要从前台删除服务，请调用`stopForeground()`。使用`stopForeground()`之后，通知会被撤销，当Service销毁（比如stopService()被调用）之后，通知也会被撤销。`stopForeground()`仅仅只是去掉service的foreground属性，并不会让service停止。

### 启动Service与绑定Service的关系
不管启动方式如何，任何服务均有可能允许客户端与其绑定。因此，最初使用`onStartCommand()`（通过客户端调用 `startService()`）启动的服务仍可接收对`onBind()`的调用（当客户端调用`bindService()`时）。此时，除非所有客户端均取消绑定，否则 `stopService()`或`stopSelf()`不会真正停止服务。

### Service生命周期
服务生命周期（从创建到销毁）可以遵循两条不同的路径：
* 启动服务
该服务在其他组件调用`startService()`时创建，然后无限期运行，且必须通过调用`stopSelf()`来自行停止运行。此外，其他组件也可以通过调用`stopService()`来停止服务。服务停止后，系统会将其销毁。
* 绑定服务
该服务在另一个组件（客户端）调用`bindService()`时创建。然后，客户端通过 IBinder 接口与服务进行通信。客户端可以通过调用`unbindService()`关闭连接。多个客户端可以绑定到相同服务，而且当所有绑定全部取消后，系统即会销毁该服务。（服务不必自行停止运行。）
<br>

```java
public class ExampleService extends Service {
    int mStartMode;       // indicates how to behave if the service is killed
    IBinder mBinder;      // interface for clients that bind
    boolean mAllowRebind; // indicates whether onRebind should be used

    @Override
    public void onCreate() {
        // The service is being created
    }
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        // The service is starting, due to a call to startService()
        return mStartMode;
    }
    @Override
    public IBinder onBind(Intent intent) {
        // A client is binding to the service with bindService()
        return mBinder;
    }
    @Override
    public boolean onUnbind(Intent intent) {
        // All clients have unbound with unbindService()
        return mAllowRebind;
    }
    @Override
    public void onRebind(Intent intent) {
        // A client is binding to the service with bindService(),
        // after onUnbind() has already been called
    }
    @Override
    public void onDestroy() {
        // The service is no longer used and is being destroyed
    }
}
```
与 Activity 生命周期回调方法不同，您不需要调用service的生命周期回调方法的超类实现。
![](https://developer.android.com/images/service_lifecycle.png)
