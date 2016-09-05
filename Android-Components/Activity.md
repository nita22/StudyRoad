## Activity基本知识
### 创建Activity
要创建 Activity，您必须创建 Activity 的子类（或使用其现有子类）。您需要在子类中实现 Activity 在其生命周期的各种状态之间转变时（例如创建 Activity、停止 Activity、恢复 Activity 或销毁 Activity 时）系统调用的回调方法。 <br>
### 启动Activity
* 显式Intent
<br>
通过使用类名创建一个显式定义您想启动的 Activity 的 Intent 对象
```Java
Intent intent = new Intent(this, SignInActivity.class);
startActivity(intent);
```
* 隐式Intent
<br>
```java
Intent intent = new Intent(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_EMAIL, recipientArray);
startActivity(intent);
```
### 启动 Activity 以获得结果
调用`startActivityForResult()`来启动 Activity。 要想在随后收到后续 Activity 的结果，请实现`onActivityResult()`回调方法。 当后续 Activity 完成时，它会使用 Intent 向您的`onActivityResult()`方法返回结果。
<br>
### 结束 Activity
可以通过调用 Activity 的`finish()`方法来结束该 Activity。您还可以通过调用`finishActivity()`结束您之前启动的另一个 Activity。
<br>
###  Activity 生命周期
* Activity 的整个生命周期发生在`onCreate()`调用与`onDestroy()`调用之间。您的 Activity 应在`onCreate()` 中执行“全局”状态设置（例如定义布局），并释放`onDestroy()`中的所有其余资源。
* Activity 的可见生命周期发生在`onStart()`调用与`onStop()`调用之间。在这段时间，用户可以在屏幕上看到 Activity 并与其交互。 例如，当一个新 Activity 启动，并且此 Activity 不再可见时，系统会调用`onStop()`。您可以在调用这两个方法之间保留向用户显示 Activity 所需的资源。在 Activity 的整个生命周期，当 Activity 在对用户可见和隐藏两种状态中交替变化时，系统可能会多次调用`onStart()`和`onStop()`。
* Activity 的前台生命周期发生在`onResume()`调用与`onPause()`调用之间。在这段时间，Activity 位于屏幕上的所有其他 Activity 之前，并具有用户输入焦点。由于此状态可能经常发生转变，因此这两个方法中应采用适度轻量级的代码，以避免因转变速度慢而让用户等待。
<br>
![](https://developer.android.com/images/activity_lifecycle.png)

### 保存 Activity 状态
* 系统会先调用`onSaveInstanceState()`，然后再使 Activity 变得易于销毁。系统会向该方法传递一个 Bundle，您可以在其中使用`putString()`和 `putInt()`等方法以名称-值对形式保存有关 Activity 状态的信息。然后，如果系统终止您的应用进程，并且用户返回您的 Activity，则系统会重建该 Activity，并将 Bundle 同时传递给`onCreate()`和`onRestoreInstanceState()`。您可以使用上述任一方法从 Bundle 提取您保存的状态并恢复该 Activity 状态。如果没有状态信息需要恢复，则传递给您的 Bundle 是空值（如果是首次创建该 Activity，就会出现这种情况）。<br>
* 系统只在Activity异常终止的时候才会调用`onSaveInstanceState()`跟`onRestoreInstanceState()`来存储和回复数据，其它情况不会触发这个过程。
如果系统调用`onSaveInstanceState()`，它会在调用`onStop()`之前，并且可能会在调用`onPause()`之前进行调用。如果系统调用 `onRestoreInstanceState()`，它会在调用`onStart()`之前进行调用。<br>
* Activity 类的`onSaveInstanceState()`默认实现也会恢复部分 Activity 状态。具体地讲，默认实现会为布局中的每个 View 调用相应的 `onSaveInstanceState()`方法，让每个视图都能提供有关自身的应保存信息。

### 处理配置变更
有些设备配置可能会在运行时发生变化（例如屏幕方向、键盘可用性及语言）。 发生此类变化时，Android 会重建运行中的 Activity（系统调用 `onDestroy()`，然后立即调用`onCreate()`）。
处理此类重启的最佳方法 是利用`onSaveInstanceState()`和`onRestoreInstanceState()`（或`onCreate()`）保存并恢复 Activity 的状态。
不想系统重新创建Activity，可以给Activity制定`configChanges`属性。

### 协调 Activity
1.Activity A 的`onPause()`方法执行。<br>
2.Activity B 的`onCreate()`、`onStart()`和`onResume()`方法依次执行。（Activity B 现在具有用户焦点。）<br>
3.然后，如果 Activity A 在屏幕上不再可见，则其`onStop()`方法执行。
