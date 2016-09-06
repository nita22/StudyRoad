## Fragment基本知识
### 创建Fragment
要想创建Fragment，您必须创建 Fragment 的子类（或已有其子类，例如`DialogFragment`，`ListFragment`，`PreferenceFragment`）。至少应实现以下生命周期方法：
`onCreate()`，`onCreateView()`，`onPause()`。
### 添加用户界面
要想为片段提供布局，您必须实现 onCreateView() 回调方法，Android 系统会在片段需要绘制其布局时调用该方法。您对此方法的实现返回的 View 必须是片段布局的根视图。
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
* 在 Activity 的布局文件内声明片段（静态添加）
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
当系统创建此 Activity 布局时，会实例化在布局中指定的每个片段，并为每个片段调用`onCreateView()`方法，以检索每个片段的布局。系统会直接插入片段返回的 View 来替代\<fragment\>元素。

