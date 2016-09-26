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
