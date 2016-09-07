Loader具有以下特征：
* 可用于每个 Activity 和 Fragment
* 支持异步加载数据
* 监控其数据源并在内容变化时传递新结果
* 在某一配置更改后重建Loader时，会自动重新连接上一个Loader的 Cursor。 因此，它们无需重新查询其数据

### 启动Loader
`LoaderManager`可在 Activity 或 Fragment 内管理一个或多个`Loader`实例。每个 Activity 或Fragment只有一个`LoaderManager`。
<br>
通常，您会使用 Activity 的`onCreate()`方法或Fragment的`onActivityCreated()`方法利用`initLoader()`方法初始化 Loader。
``` java
getLoaderManager().initLoader(0, null, this);
```
`initLoader()`方法采用以下参数：
* 用于标识Loader的唯一 ID
* 在构建时提供给Loader的可选参数
* `LoaderManager.LoaderCallbacks`实现， LoaderManager 将调用此实现来报告Loader事件

#### 注意
* 如果 ID 指定的Loader已存在，则将重复使用上次创建的Loader。
* 如果 ID 指定的Loader不存在，则`initLoader()`将触发`LoaderManager.LoaderCallbacks`方法`onCreateLoader()`。在此方法中，您可以实现代码以实例化并返回新Loader。
* 如果在调用时，调用程序处于启动状态，且请求的Loader已存在并生成了数据，则系统将立即调用`LoaderManager.LoaderCallbacks`的`onLoadFinished()`方法 

### 重启Loader
当您使用`initLoader()` 时，它将使用含有指定 ID 的现有Loader（如有）。如果没有，则它会创建一个。但有时，您想放弃这些旧数据并重新开始，此时可以使用`restartLoader()`方法实现。

### 使用 LoaderManager 回调
LoaderManager将根据需要启动和停止加载，并维护Loader的状态及其相关内容。这意味着您很少直接与Loader进行交互。当特殊事件发生时，您通常会使用`LoaderManager.LoaderCallbacks`接口的方法干预加载进程。
`LoaderManager.LoaderCallbacks`是一个支持客户端与`LoaderManager`交互的回调接口。<br>
Loader（特别是 CursorLoader）在停止运行后，仍需保留其数据。可使用`LoaderManager.LoaderCallbacks`方法了解何时创建新Loader，并告知应用何时停止使用Loader的数据。<br>
* `onCreateLoader()`:当您尝试访问Loader时（例如，通过`initLoader()`），该方法将检查是否已存在由该 ID 指定的Loader。如果没有，它将触发`LoaderManager.LoaderCallbacks`方法`onCreateLoader()`。在此方法中，您可以创建新Loader。 通常，这将是`CursorLoader`，但您也可以实现自己的 Loader 子类。
* `onLoadFinished()`:当先前创建的Loader完成加载时，将调用此方法。该方法必须在为此Loader提供的最后一个数据释放之前调用。 此时，您应移除所有使用的旧数据（因为它们很快会被释放），但不要自行释放这些数据，因为这些数据归其Loader所有，其Loader会处理它们。
当Loader发现应用不再使用这些数据时，即会释放它们。
* `onLoaderReset()`:此方法将在先前创建的Loader重置且其数据因此不可用时调用。 通过此回调，您可以了解何时将释放数据，因而能够及时移除其引用。


