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
* Activity 的整个生命周期发生在`onCreate()`调用与`onDestroy()`调用之间。您的 Activity 应在`onCreate()` 中执行“全局”状态设置（例如定义布局），并释放`onDestroy()`中的所有其余资源。<br><br>
* Activity 的可见生命周期发生在`onStart()`调用与`onStop()`调用之间。在这段时间，用户可以在屏幕上看到 Activity 并与其交互。 例如，当一个新 Activity 启动，并且此 Activity 不再可见时，系统会调用`onStop()`。您可以在调用这两个方法之间保留向用户显示 Activity 所需的资源。在 Activity 的整个生命周期，当 Activity 在对用户可见和隐藏两种状态中交替变化时，系统可能会多次调用`onStart()`和`onStop()`。<br><br>
* Activity 的前台生命周期发生在`onResume()`调用与`onPause()`调用之间。在这段时间，Activity 位于屏幕上的所有其他 Activity 之前，并具有用户输入焦点。由于此状态可能经常发生转变，因此这两个方法中应采用适度轻量级的代码，以避免因转变速度慢而让用户等待。
<br><br>
![Activity生命周期](https://github.com/nita22/StudyRoad/blob/master/Res/Pic/activity_lifecycle.png?raw=true)

### 保存 Activity 状态
* 系统会先调用`onSaveInstanceState()`，然后再使 Activity 变得易于销毁。系统会向该方法传递一个 Bundle，您可以在其中使用`putString()`和 `putInt()`等方法以名称-值对形式保存有关 Activity 状态的信息。然后，如果系统终止您的应用进程，并且用户返回您的 Activity，则系统会重建该 Activity，并将 Bundle 同时传递给 `onCreate()`和`onRestoreInstanceState()`。您可以使用上述任一方法从 Bundle 提取您保存的状态并恢复该 Activity 状态。如果没有状态信息需要恢复，则传递给您的 Bundle 是空值（如果是首次创建该 Activity，就会出现这种情况）。<br><br>
* 系统只在Activity异常终止的时候才会调用`onSaveInstanceState()`跟`onRestoreInstanceState()`来存储和回复数据，其它情况不会触发这个过程。如果系统调用`onSaveInstanceState()`，它会在调用`onStop()`之前，并且可能会在调用`onPause()`之前进行调用。如果系统调用 `onRestoreInstanceState()`，它会在调用`onStart()`之前进行调用。<br><br>
* Activity 类的`onSaveInstanceState()`默认实现也会恢复部分 Activity 状态。具体地讲，默认实现会为布局中的每个 View 调用相应的 `onSaveInstanceState()`方法，让每个视图都能提供有关自身的应保存信息。

### 处理配置变更
* 有些设备配置可能会在运行时发生变化（例如屏幕方向、键盘可用性及语言）。 发生此类变化时，Android 会重建运行中的 Activity（系统调用 `onDestroy()`，然后立即调用`onCreate()`）。<br><br>
* 处理此类重启的最佳方法 是利用`onSaveInstanceState()`和`onRestoreInstanceState()`（或`onCreate()`）保存并恢复 Activity 的状态。<br><br>
* 不想系统重新创建Activity，可以给Activity制定`configChanges`属性。

### 协调 Activity
1.Activity A 的`onPause()`方法执行。<br>
2.Activity B 的`onCreate()`、`onStart()`和`onResume()`方法依次执行。（Activity B 现在具有用户焦点。）<br>
3.然后，如果 Activity A 在屏幕上不再可见，则其`onStop()`方法执行。

### Activity启动模式
####使用清单文件
在清单文件中声明 Activity 时，您可以使用\<activity\>元素的`launchMode`属性指定 Activity 应该如何与任务关联。
* "standard"
系统的默认模式。每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否已经存在。<br><br>
* "singleTop"
如果当前任务的顶部已存在 Activity 的一个实例，则系统会通过调用该实例的`onNewIntent()`方法向其传送 Intent，而不是创建 Activity 的新实例。<br><br>
* "singleTask"
系统先寻找Activity想要的任务栈是否存在，如果任务栈存在，且该 Activity 的一个实例已存在于想要的任务栈中，则系统会通过调用现有实例的 `onNewIntent()`方法向其传送Intent，而不是创建新实例。如果想要的任务栈不存在，系统会创建新任务，然后实例化Activity并放到该任务栈中。
<br><br>
* "singleInstance"<br>
与`"singleTask"`相同，只是系统不会将任何其他 Activity 启动到包含实例的任务中。该 Activity 始终是其任务唯一仅有的成员；由此 Activity 启动的任何 Activity 均在单独的任务中打开。

####使用 Intent 标志
启动 Activity 时，您可以通过在传递给`startActivity()`的 Intent 中加入相应的标志，修改 Activity 与其任务的默认关联方式。
* FLAG_ACTIVITY_NEW_TASK<br>
在新任务中启动 Activity。如果已为正在启动的 Activity 运行任务，则该任务会转到前台并恢复其最后状态，同时 Activity 会在`onNewIntent()` 中收到新 Intent。与`"singleTask"`launchMode 值相同<br><br>
* FLAG_ACTIVITY_SINGLE_TOP<br>
如果正在启动的 Activity 是当前 Activity（位于返回栈的顶部），则现有实例会接收对`onNewIntent()`的调用，而不是创建 Activity 的新实例。
与`"singleTop"`launchMode 值相同<br><br>
* FLAG_ACTIVITY_CLEAR_TOP<br>
如果正在启动的 Activity 已在当前任务中运行，则会销毁当前任务顶部的所有 Activity，并通过`onNewIntent()`将此 Intent 传递给 Activity 已恢复的实例（现在位于顶部），而不是启动该 Activity 的新实例。

#### 注意
* 使用 Intent 标志的优先级要高于使用清单文件
* 某些适用于清单文件的启动模式不可用作 Intent 标志，同样，某些可用作 Intent 标志的启动模式无法在清单文件中定义
* 无论 Activity 是在新任务中启动，还是在与启动 Activity 相同的任务中启动，用户按“返回”按钮始终会转到前一个 Activity。 但是，如果启动指定`singleTask`启动模式的 Activity，则当某后台任务中存在该 Activity 的实例时，整个任务都会转移到前台。


### 任务栈
默认情况下，同一应用中的所有 Activity 彼此关联。 因此，默认情况下，同一应用中的所有 Activity 优先位于相同任务中。
可以使用\<activity\>元素的`taskAffinity`属性修改任何给定 Activity 的关联，系统默认使用软件包名称作为默认任务栈的名字。
* 启动 Activity 的 Intent 包含`FLAG_ACTIVITY_NEW_TASK`标志<br>
待启动的Activity会运行在名字和TaskAffinity相同的任务栈中
* Activity 将其`allowTaskReparenting`属性设置为 "true"<br>
Activity 可以从其启动的任务移动到与其具有关联的任务（如果该任务出现在前台）

### 清理返回栈
如果用户长时间离开任务，则系统会清除所有 Activity 的任务，根任务除外。 当用户再次返回到任务时，仅恢复根 Activity。<br>
可以使用下列几个 Activity 属性修改此行为：<br><br>
`alwaysRetainTaskState`:<br>
如果在任务的根 Activity 中将此属性设置为 "true"，则不会发生刚才所述的默认行为。即使在很长一段时间后，任务仍将所有 Activity 保留在其堆栈中。<br><br>
`clearTaskOnLaunch`:<br>
如果在任务的根 Activity 中将此属性设置为 "true"，则每当用户离开任务然后返回时，系统都会将堆栈清除到只剩下根 Activity。 换而言之，它与 `alwaysRetainTaskState`正好相反。 即使只离开任务片刻时间，用户也始终会返回到任务的初始状态。<br><br>
`finishOnTaskLaunch`:<br>
此属性类似于`clearTaskOnLaunch`，但它对单个 Activity 起作用，而非整个任务。 此外，它还有可能会导致任何 Activity 停止，包括根 Activity。 设置为 "true" 时，Activity 仍是任务的一部分，但是仅限于当前会话。如果用户离开然后返回任务，则任务将不复存在。

### 入口Activity
``` java
<activity ... >
    <intent-filter ... >
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    ...
</activity>
```
### 最近任务列表
#### 添加任务到最近任务列表
* 使用 Intent 标志添加任务<br>
可在启动 Activity 的 Intent 的`addFlags()`方法中传递`FLAG_ACTIVITY_NEW_DOCUMENT`标志。如果在创建新文档时设置 `FLAG_ACTIVITY_MULTIPLE_TASK`标志，则系统始终会以目标 Activity 作为根创建新任务。此设置允许同一文档在多个任务中打开。
``` java
Intent newDocumentIntent = new Intent(this, NewDocumentActivity.class);
newDocumentIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_DOCUMENT);
newDocumentIntent.addFlags(Intent.FLAG_ACTIVITY_MULTIPLE_TASK);
startActivity(newDocumentIntent);
```
当主 Activity 启动新 Activity 时，系统会搜遍现有任务，看看是否有任务的 Intent 与 Activity 的 Intent 组件名称和 Intent 数据相匹配。 如果未找到任务或者 Intent 包含`FLAG_ACTIVITY_MULTIPLE_TASK`标志，则会以该 Activity 作为其根创建新任务。如果找到的话，则会将该任务转到前台并将新 Intent 传递给`onNewIntent()`。<br><br>
使用`FLAG_ACTIVITY_NEW_DOCUMENT`标志启动的 Activity 必须具有在清单文件中设置的`android:launchMode="standard"`属性值（默认）
* 使用 Activity 属性添加任务<br>
Activity 还可以在其清单文件中指定始终通过使用\<activity\>属性`android:documentLaunchMode`进入新任务。
    * `“intoExisting”`:这与不设置`FLAG_ACTIVITY_MULTIPLE_TASK`标志、但设置`FLAG_ACTIVITY_NEW_DOCUMENT`标志所产生的效果相同<br>
    * `“always”`:使用此值与同时设置`FLAG_ACTIVITY_NEW_DOCUMENT`和`FLAG_ACTIVITY_MULTIPLE_TASK`标志所产生的效果相同<br>
    * `“none”`:为默认方式。该 Activity 不会为文档创建新任务，为应用显示单个任务，该任务将从用户上次调用的任意 Activity 开始继续执行<br>
    * `“never”`:该 Activity 不会为文档创建新任务。设置此值会替代`FLAG_ACTIVITY_NEW_DOCUMENT`和`FLAG_ACTIVITY_MULTIPLE_TASK` 标志的行为（如果在 Intent 中设置了其中一个标志），并且最近任务列表将为应用显示单个任务，该任务将从用户上次调用的任意 Activity 开始继续执行<br><br>

对于除 none 和 never 以外的值，必须使用`launchMode="standard"`定义 Activity。如果未指定此属性，则使用`documentLaunchMode="none"`

#### 删除任务
默认情况下，在 Activity 结束后，文档任务会从最近任务列表中自动删除。<br>
通过将\<activity\>属性`android:excludeFromRecents`设置为 true，您可以始终将任务从最近任务列表中完全排除。<br>

您可以通过将\<activity\>属性`android:maxRecents`设置为整型值，设置应用能够包括在最近任务列表中的最大任务数。默认值为 16。达到最大任务数后，最近最少使用的任务将从最近任务列表中删除。`android:maxRecents`的最大值为 50（内存不足的设备上为 25。<br>

可以通过调用`finishAndRemoveTask()`方法指定何时删除该任务以及结束所有与之相关的 Activity

##### 保留已完成的任务
* 若要将任务保留在最近任务列表中（即使其 Activity 已完成），可在启动 Activity 的 Intent 的`addFlags()`方法中传递 `FLAG_ACTIVITY_RETAIN_IN_RECENTS`标志。
``` java 
newDocumentIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_DOCUMENT | android.content.Intent.FLAG_ACTIVITY_RETAIN_IN_RECENTS);
```
* 也可以把\<activity\>属性`android:autoRemoveFromRecents`设置为 false。文档 Activity的默认值为 true，常规 Activity 的默认值为 false
