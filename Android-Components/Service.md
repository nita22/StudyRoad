## Service基础知识
### 创建启动Service
可以扩展两个类来创建启动服务：
* 扩展 IntentService 类<br><br>
IntentService 执行以下操作：<br>
  * 创建默认的工作线程，用于在应用的主线程外执行传递给`onStartCommand()`的所有 Intent
  * 创建工作队列，用于将一个 Intent 逐一传递给`onHandleIntent()`实现，这样您就永远不必担心多线程问题
  * 在处理完所有启动请求后停止服务，因此您永远不必调用`stopSelf()`
  * 提供`onBind()`的默认实现（返回 null）
  * 提供`onStartCommand()`的默认实现，可将 Intent 依次发送到工作队列和`onHandleIntent()`实现<br><br>
  
扩展 IntentService 类只需要一个构造函数和一个`onHandleIntent()`实现即可<br>

如果您决定还重写其他回调方法（如`onCreate()`、`onStartCommand()` 或`onDestroy()`）,请确保调用超类实现，以便 IntentService 能够妥善处理工作线程的生命周期。除`onHandleIntent()`之外，您无需从中调用超类的唯一方法就是`onBind()`（仅当服务允许绑定时，才需要实现该方法）。
* 拓展Service类<br><br>
需要自行处理对`onStartCommand()`的每次调用<br><br>
`onStartCommand()`方法必须返回整型数。整型数是一个值，用于描述系统应该如何在服务终止的情况下继续运行服务。从`onStartCommand()`返回的值必须是以下常量之一：<br>
  * `START_NOT_STICKY`:如果系统在`onStartCommand()`返回后终止服务，则除非有挂起 Intent 要传递，否则系统不会重建服务。
  * `START_STICKY`:如果系统在`onStartCommand()`返回后终止服务，则会重建服务并调用`onStartCommand()`，但绝对不会重新传递最后一个 Intent。相反，除非有挂起 Intent 要启动服务（在这种情况下，将传递这些 Intent ），否则系统会通过空 Intent 调用`onStartCommand()`。
  * `START_REDELIVER_INTENT`:如果系统在`onStartCommand()`返回后终止服务，则会重建服务，并通过传递给服务的最后一个 Intent 调用`onStartCommand()`。任何挂起 Intent 均依次传递。

### 启动Service
可以通过将 Intent（指定要启动的服务）传递给`startService()`，从 Activity 或其他应用组件启动服务。Android 系统调用服务的`onStartCommand()`方法，并向其传递 Intent。
``` java 
Intent intent = new Intent(this, HelloService.class);
startService(intent);
```
`startService()`方法将立即返回，且 Android 系统调用服务的`onStartCommand()`方法。如果服务尚未运行，则系统会先调用`onCreate()`，然后再调用`onStartCommand()`。<br><br>

如果服务亦未提供绑定，则使用`startService()`传递的 Intent 是应用组件与服务之间唯一的通信模式。但是，如果您希望服务返回结果，则启动服务的客户端可以为广播创建一个`PendingIntent`（使用`getBroadcast()`），并通过启动服务的 Intent 传递给服务。然后，服务就可以使用广播传递结果。<br><br>

多个服务启动请求会导致多次对服务的`onStartCommand()`进行相应的调用。但是，要停止服务，只需一个服务停止请求（使用`stopSelf()`或`stopService()`）即可。

### 停止Service
除非系统必须回收内存资源，否则系统不会停止或销毁服务，而且服务在`onStartCommand()`返回后会继续运行。因此，服务必须通过调用`stopSelf()`自行停止运行，或者由另一个组件通过调用`stopService()`来停止它。
<br><br>
一旦请求使用`stopSelf()`或`stopService()`停止服务，系统就会尽快销毁服务。
<br><br>
但是，如果服务同时处理多个`onStartCommand()`请求，则您不应在处理完一个启动请求之后停止服务，因为您可能已经收到了新的启动请求（在第一个请求结束时停止服务会终止第二个请求）。为了避免这一问题，您可以调用`stopSelf(int)`时，传递与停止请求的 ID 对应的启动请求的 ID（传递给`onStartCommand()`的 startId） 。然后，如果在您能够调用`stopSelf(int)`之前服务收到了新的启动请求， ID 就不匹配，服务也就不会停止。

### 创建绑定Service

### 前台服务
要请求让服务运行于前台，请调用`startForeground()`。此方法取两个参数：唯一标识通知的整型数(整型 ID 不得为 0)和状态栏的`Notification`。
``` java
startForeground(ONGOING_NOTIFICATION_ID, notification);
```
要从前台删除服务，请调用`stopForeground()`。使用`stopForeground()`之后，通知会被撤销，当Service销毁（比如stopService()被调用）之后，通知也会被撤销。`stopForeground()`仅仅只是去掉service的foreground属性，并不会让service停止。

### Service生命周期
服务生命周期（从创建到销毁）可以遵循两条不同的路径：
* 启动服务
该服务在其他组件调用`startService()`时创建，然后无限期运行，且必须通过调用`stopSelf()`来自行停止运行。此外，其他组件也可以通过调用`stopService()`来停止服务。服务停止后，系统会将其销毁。
* 绑定服务
该服务在另一个组件（客户端）调用`bindService()`时创建。然后，客户端通过 IBinder 接口与服务进行通信。客户端可以通过调用`unbindService()`关闭连接。多个客户端可以绑定到相同服务，而且当所有绑定全部取消后，系统即会销毁该服务。（服务不必自行停止运行。）
<br>
与 Activity 生命周期回调方法不同，您不需要调用service的生命周期回调方法的超类实现。
![](https://developer.android.com/images/service_lifecycle.png)
