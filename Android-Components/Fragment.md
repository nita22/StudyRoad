## Fragment基本知识
### 创建Fragment
要想创建Fragment，您必须创建 Fragment 的子类（或已有其子类，例如`DialogFragment`，`ListFragment`，`PreferenceFragment`）。至少应实现以下生命周期方法：
`onCreate()`，`onCreateView()`，`onPause()`。
### 添加用户界面
要想为Fragment提供布局，您必须实现`onCreateView()`回调方法，Android 系统会在Fragment需要绘制其布局时调用该方法。您对此方法的实现返回的 View 必须是Fragment布局的根视图。
```java 
public static class ExampleFragment extends Fragment {
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.example_fragment, container, false);
    }
}
```
### 添加Fragment
* 在 Activity 的布局文件内声明Fragment（静态添加）
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment android:name="com.example.news.ArticleListFragment"
            android:id="@+id/list"
            android:layout_weight="1"
            android:layout_width="0dp"
            android:layout_height="match_parent" />
    <fragment android:name="com.example.news.ArticleReaderFragment"
            android:id="@+id/viewer"
            android:layout_weight="2"
            android:layout_width="0dp"
            android:layout_height="match_parent" />
</LinearLayout>
```
\<fragment\>中的`android:name`属性指定要在布局中实例化的 Fragment 类。<br>
当系统创建此 Activity 布局时，会实例化在布局中指定的每个Fragment，并为每个Fragment调用`onCreateView()`方法，以检索每个Fragment的布局。系统会直接插入Fragment返回的 View 来替代\<fragment\>元素。
* 在代码中添加Fragment（动态添加）

可以在 Activity 运行期间随时将Fragment添加到 Activity 布局中。只需指定要将Fragment放入哪个 ViewGroup。
``` java
FragmentManager fragmentManager = getFragmentManager()
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
ExampleFragment fragment = new ExampleFragment();
fragmentTransaction.add(R.id.fragment_container, fragment);
fragmentTransaction.commit();
```
#### 添加没有 UI 的Fragment
要想添加没有 UI 的Fragment，请使用`add(Fragment, String)`从 Activity 添加Fragment（为Fragment提供一个唯一的字符串“标记”，而不是视图 ID）。这会添加Fragment，但由于它并不与 Activity 布局中的视图关联，因此不会收到对 onCreateView() 的调用。因此，不需要实现该方法。

### 管理Fragment
要想管理您的 Activity 中的Fragment，您需要使用`FragmentManager`。要想获取它，请从您的 Activity 调用`getFragmentManager()`。
* 通过`findFragmentById()`（对于在 Activity 布局中提供 UI 的Fragment）或`findFragmentByTag()`（对于提供或不提供 UI 的Fragment）获取 Activity 中存在的Fragment
* 通过`popBackStack()`（模拟用户发出的 Back 命令）将Fragment从返回栈中弹出
* 通过`addOnBackStackChangedListener()`注册一个侦听返回栈变化的侦听器

### 执行Fragment事务
``` java
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
```
* 每个事务都是您想要同时执行的一组更改。您可以使用`add()`、`remove()`和 `replace()`等方法为给定事务设置您想要执行的所有更改。然后，要想将事务应用到 Activity，您必须调用`commit()`。
* 不过，在您调用`commit()`之前，您可能想调用`addToBackStack()`，以将事务添加到Fragment事务返回栈。 该返回栈由 Activity 管理，允许用户通过按“返回” 按钮返回上一Fragment状态。如果您向事务添加了多个更改（如又一个`add()`或`remove()`），并且调用了 `addToBackStack()`，则在调用`commit()`前应用的所有更改都将作为单一事务添加到返回栈，并且“返回” 按钮会将它们一并撤消。如果您没有在执行删除Fragment的事务时调用`addToBackStack()`，则事务提交时该Fragment会被销毁，用户将无法回退到该Fragment。 不过，如果您在删除Fragment时调用了`addToBackStack()`，则系统会停止该Fragment，并在用户回退时将其恢复。
* 对于每个Fragment事务，您都可以通过在提交前调用`setTransition()`来应用过渡动画。
* 调用`commit()`不会立即执行事务，而是在 Activity 的 UI 线程可以执行该操作时再安排其在线程上运行。不过，如有必要，您也可以从 UI 线程调用`executePendingTransactions()`以立即执行`commit()`提交的事务。通常不必这样做，除非其他线程中的作业依赖该事务。
* 只能在 Activity保存其状态（用户离开 Activity）之前使`commit()`提交事务。如果您试图在该时间点后提交，则会引发异常。 这是因为如需恢复 Activity，则提交后的状态可能会丢失。 对于丢失提交无关紧要的情况，请使用`commitAllowingStateLoss()`。

### 与 Activity 通信
* Fragment可以通过`getActivity()`访问 Activity 实例
* Activity 也可以使用FragmentManager的`findFragmentById()`或`findFragmentByTag()`方法获取对 Fragment 的引用

### 向操作栏添加项目
您的Fragment可以通过实现`onCreateOptionsMenu()`向 Activity 的选项菜单（并因此向操作栏）贡献菜单项。不过，为了使此方法能够收到调用，您必须在`onCreate()`期间调用 `setHasOptionsMenu()`，以指示Fragment想要向选项菜单添加菜单项（否则，Fragment将不会收到对`onCreateOptionsMenu()`的调用）。<br>

您之后从Fragment添加到选项菜单的任何菜单项都将追加到现有菜单项之后。 选定菜单项时，Fragment还会收到对`onOptionsItemSelected()`的回调。<br>

您还可以通过调用`registerForContextMenu()`，在Fragment布局中注册一个视图来提供上下文菜单。用户打开上下文菜单时，Fragment会收到对 `onCreateContextMenu()`的调用。当用户选择某个菜单项时，Fragment会收到对`onContextItemSelected()`的调用。<br>

### Fragment生命周期
假使 Activity 的进程被终止，而您需要在重建 Activity 时恢复Fragment状态，您也可以使用 Bundle 保留Fragment的状态。您可以在Fragment的 `onSaveInstanceState()`回调期间保存状态，并可在`onCreate()`、`onCreateView()`或`onActivityCreated()`期间恢复状态。<br>
仅当您在删除Fragment的事务执行期间通过调用`addToBackStack()`显式请求保存实例时，系统才会将Fragment放入由宿主 Activity 管理的返回栈。<br>
![](https://github.com/nita22/StudyRoad/blob/master/Res/Pic/fragment_lifecycle.png?raw=true)<br><br>
![](https://github.com/nita22/StudyRoad/blob/master/Res/Pic/activity_fragment_lifecycle.png?raw=true)
