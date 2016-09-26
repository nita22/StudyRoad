## Menu

### 在XML中定义Menu
* 文件位置：res/menu/filename.xml
* 引用Menu：
  * Java代码中引用：`R.menu.filename`
  * XML代码中引用：`@[package:]menu.filename`
  
``` xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@[+][package:]id/resource_name"
          android:title="string"
          android:titleCondensed="string"
          android:icon="@[package:]drawable/drawable_resource_name"
          android:onClick="method name"
          android:showAsAction=["ifRoom" | "never" | "withText" | "always" | "collapseActionView"]
          android:actionLayout="@[package:]layout/layout_resource_name"
          android:actionViewClass="class name"
          android:actionProviderClass="class name"
          android:alphabeticShortcut="string"
          android:numericShortcut="string"
          android:checkable=["true" | "false"]
          android:visible=["true" | "false"]
          android:enabled=["true" | "false"]
          android:menuCategory=["container" | "system" | "secondary" | "alternative"]
          android:orderInCategory="integer" />
    <group android:id="@[+][package:]id/resource name"
           android:checkableBehavior=["none" | "all" | "single"]
           android:visible=["true" | "false"]
           android:enabled=["true" | "false"]
           android:menuCategory=["container" | "system" | "secondary" | "alternative"]
           android:orderInCategory="integer" >
        <item />
    </group>
    <item >
        <menu>
          <item />
        </menu>
    </item>
</menu>
```

\<item\>标签中可以包含\<menu\>标签作为子菜单。<br>
`android:title`：菜单的标题<br>
`android:titleCondensed`：简要标题，在普通标题太长时来使用<br>
`android:icon`：菜单项所要使用的图标<br>
`android:onClick`：当menu的item被点击时调用方法，方法必须声明为public并且参数只能有一个MenuItem<br>
`android:showAsAction`：定义item作为操作栏中的操作项的显示时机和方式，值可以为`ifRoom`、`withText`、`never`、`always`、`collapseActionView`。<br>
`android:actionLayout`： 作为action view使用的布局。 <br>
`android:actionViewClass`：action view要使用的View对象的完整的类名<br>
`android:actionProviderClass`：操作item所使用的`ActionProvider`类的完整的类名<br>
`android:alphabeticShortcut`：定义一个字符快捷键<br>
`android:numericShortcut`：定义一个数字快捷键<br>
`android:checkable`：菜单项是否可以复选<br>
`android:checked`：菜单项是否被选中<br>
`android:visible`：是否可见<br>
`android:enabled`：菜单项是否可用<br>
`android:menuCategory`：值对应了定义菜单项优先级的常量。值可以为`container`、`system`、`secondary`、`alternative`。<br>
`android:orderInCategory`：定义菜单项在菜单组中的重要性的顺序<br>

\<group\>标签中的`android:checkableBehavior`属性定义菜单组的可复选行为的类型，其值可以为`none`、`all`、`single`。<br>

范例：
``` xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="@string/item1"
          android:icon="@drawable/group_item1_icon"
          android:showAsAction="ifRoom|withText"/>
    <group android:id="@+id/group">
        <item android:id="@+id/group_item1"
              android:onClick="onGroupItemClick"
              android:title="@string/group_item1"
              android:icon="@drawable/group_item1_icon" />
        <item android:id="@+id/group_item2"
              android:onClick="onGroupItemClick"
              android:title="@string/group_item2"
              android:icon="@drawable/group_item2_icon" />
    </group>
    <item android:id="@+id/submenu"
          android:title="@string/submenu_title"
          android:showAsAction="ifRoom|withText" >
        <menu>
            <item android:id="@+id/submenu_item1"
                  android:title="@string/submenu_item1" />
        </menu>
    </item>
</menu>
```

Java代码中加载菜单：
``` java
public boolean onCreateOptionsMenu(Menu menu) {
    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.example_menu, menu);
    return true;
}
```


