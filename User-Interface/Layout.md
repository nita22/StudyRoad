## Layout
* 文件位置：res/layout/filename.xml
* 引用布局：
  * Java代码中引用：`R.layout.filename`
  * XML代码中引用：`@[package:]layout/filename`

``` xml
<ViewGroup
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@[+][package:]id/resource_name"
    android:layout_height=["dimension" | "match_parent" | "wrap_content"]
    android:layout_width=["dimension" | "match_parent" | "wrap_content"]
    [ViewGroup-specific attributes] >
    <View
        android:id="@[+][package:]id/resource_name"
        android:layout_height=["dimension" | "match_parent" | "wrap_content"]
        android:layout_width=["dimension" | "match_parent" | "wrap_content"]
        [View-specific attributes] >
        <requestFocus/>
    </View>
    <ViewGroup >
        <View />
    </ViewGroup>
    <include layout="@layout/layout_resource"/>
</ViewGroup>
```

### 布局优化

* \<include\>标签<br>
  \<include\>标签可以将一个指定的布局文件加载到当前的布局文件中。使用\<include\>标签必须要重写`android:layout_height`和`android:layout_width`属性。<br>
``` xml
<include layout="@layout/titlebar"/>
```

* \<merge\>标签<br>
  \<merge\>标签一般跟\<include\>标签一起使用来减少布局的层级。当LayoutInflater遇到\<merge/\>标签时,它会跳过它,并将\<merge/\>内的元素添加到\<merge/\>的父元素里。<br>

* ViewStub<br>
  ViewStub可以按需加载所需的布局文件。当ViewStub通过`setVisibility()`或`inflate()`方法加载后，ViewStub就会被它内部的布局替换掉。<br>
``` xml
<ViewStub
  android:id="@+id/stub_import"
  android:inflateId="@+id/panel_import"
  android:layout="@layout/layout_network_error"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="bottom"  />
```

需要加载ViewStub中的布局时，可以：
``` java
((ViewStub) findViewById(R.id.stub_import)).setVisibity(View.VISIBLE);
```

或
``` java
View importPanel = ((ViewStub) findViewById(R.id.stub_import)).inflate();
```
