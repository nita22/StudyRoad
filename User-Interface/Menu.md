# Menu

## 在XML中定义Menu
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
`android:onClick`：当menu的item被点击时调用方法，方法必须声明为public并且参数只能有一个MenuItem，该方法的优先度比`onOptionsItemSelected()`方法要高<br>
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

## **选项菜单和操作栏**
可以通过 Activity 子类或 Fragment 子类为选项菜单声明项目。如果您的 Activity 和Fragment均为选项菜单声明项目，系统将首先显示 Activity 的项目，随后按每个Fragment添加到 Activity 中的顺序显示该Fragment的项目。可以使用`add()`添加菜单项，并使用`findItem()`检索项目。
``` java
public boolean onCreateOptionsMenu(Menu menu) {
    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.game_menu, menu);
    return true;
}
```

### 处理点击事件

``` java
public boolean onOptionsItemSelected(MenuItem item) {
    // Handle item selection
    switch (item.getItemId()) {
        case R.id.new_game:
            newGame();
            return true;
        case R.id.help:
            showHelp();
            return true;
        default:
            return super.onOptionsItemSelected(item);
    }
}
```

成功处理菜单项后，系统将返回 true。如果未处理菜单项，则应调用`onOptionsItemSelected()`的超类实现（默认实现将返回 false）。

如果 Activity 包括Fragment，则系统将依次为 Activity 和每个Fragment（按照每个Fragment的添加顺序）调用 `onOptionsItemSelected()`，直到有一返回结果为 true 或所有Fragment均调用完毕为止。

### 在运行时更改菜单项

如需根据在 Activity 生命周期中发生的事件修改选项菜单，则可通过`onPrepareOptionsMenu()`方法执行此操作。此方法向您传递 Menu 对象，以便您能够对其进行修改，如添加、删除或禁用项目。

* 在 Android 2.3.x 及更低版本中，每当用户打开选项菜单时，系统均会调用 `onPrepareOptionsMenu()`。
* 在 Android 3.0 及更高版本中，当菜单项显示在操作栏中时，选项菜单被视为始终处于打开状态。发生事件时，如果您要执行菜单更新，则必须调用`invalidateOptionsMenu()` 来请求系统调用 `onPrepareOptionsMenu()`。

### 为下级 Activity 添加向上按钮

实现向上导航需要在 manifest 文件中声明父 activity ，然后通过调用`setDisplayHomeAsUpEnabled()`来把 app icon 设置成可用的向上按钮：

``` xml
<application ... >
    ...
    <!-- 主 main/home 活动 (没有上级活动) -->
    <activity
        android:name="com.example.myfirstapp.MainActivity" ...>
        ...
    </activity>
    <!-- 主活动的一个子活动-->
    <activity
        android:name="com.example.myfirstapp.DisplayMessageActivity"
        android:label="@string/title_activity_display_message"
        android:parentActivityName="com.example.myfirstapp.MainActivity" >
        <!--  meta-data 用于支持 support 4.0 以及以下来指明上级活动 -->
        <meta-data
            android:name="android.support.PARENT_ACTIVITY"
            android:value="com.example.myfirstapp.MainActivity" />
    </activity>
</application>
```

``` java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_displaymessage);

    getSupportActionBar().setDisplayHomeAsUpEnabled(true);
    // 如果你的minSdkVersion属性是11或更高, 应该这么用:
    // getActionBar().setDisplayHomeAsUpEnabled(true);
}
```

## **上下文菜单和上下文操作模式**
### 创建浮动上下文菜单（Android 3.0以下系统）

1. 通过调用 `registerForContextMenu()`，注册应与上下文菜单关联的 `View` 并将其传递给 `View`。可以通过将 `ListView` 或 `GridView` 传递给`registerForContextMenu()`，为上下文菜单注册所有项目。

2. 在 `Activity` 或 `Fragment` 中实现 `onCreateContextMenu()` 方法。

   ``` java
   public void onCreateContextMenu(ContextMenu menu, View v,
                                   ContextMenuInfo menuInfo) {
       super.onCreateContextMenu(menu, v, menuInfo);
       MenuInflater inflater = getMenuInflater();
       inflater.inflate(R.menu.context_menu, menu);
   }
   ```

3. 实现 `onContextItemSelected()`。如果 Activity 包括Fragment，则 Activity 将先收到此回调。 通过在未处理的情况下调用超类，系统将事件逐一传递给每个Fragment中相应的回调方法（按照每个Fragment的添加顺序），直到返回 true 或 false 为止。

   ``` java
   public boolean onContextItemSelected(MenuItem item) {
       AdapterContextMenuInfo info = (AdapterContextMenuInfo) item.getMenuInfo();
       switch (item.getItemId()) {
           case R.id.edit:
               editNote(info.id);
               return true;
           case R.id.delete:
               deleteNote(info.id);
               return true;
           default:
               return super.onContextItemSelected(item);
       }
   }
   ```

### 使用上下文操作模式（Android 3.0及以上系统）

#### 为单个视图启用上下文操作模式

1. 实现 `ActionMode.Callback` 接口：

   ``` java
   private ActionMode.Callback mActionModeCallback = new ActionMode.Callback() {

       // Called when the action mode is created; startActionMode() was called
       @Override
       public boolean onCreateActionMode(ActionMode mode, Menu menu) {
           // Inflate a menu resource providing context menu items
           MenuInflater inflater = mode.getMenuInflater();
           inflater.inflate(R.menu.context_menu, menu);
           return true;
       }

       // Called each time the action mode is shown. Always called after onCreateActionMode, but
       // may be called multiple times if the mode is invalidated.
       @Override
       public boolean onPrepareActionMode(ActionMode mode, Menu menu) {
           return false; // Return false if nothing is done
       }

       // Called when the user selects a contextual menu item
       @Override
       public boolean onActionItemClicked(ActionMode mode, MenuItem item) {
           switch (item.getItemId()) {
               case R.id.menu_share:
                   shareCurrentItem();
                   mode.finish(); // Action picked, so close the CAB
                   return true;
               default:
                   return false;
           }
       }

       // Called when the user exits the action mode
       @Override
       public void onDestroyActionMode(ActionMode mode) {
           mActionMode = null;
       }
   };
   ```

2. 调用 `startActionMode()` 以便适时启用上下文操作模式。

   ``` java
   someView.setOnLongClickListener(new View.OnLongClickListener() {
       // Called when the user long-clicks on someView
       public boolean onLongClick(View view) {
           if (mActionMode != null) {
               return false;
           }

           // Start the CAB using the ActionMode.Callback defined above
           mActionMode = getActivity().startActionMode(mActionModeCallback);
           view.setSelected(true);
           return true;
       }
   });
   ```
#### 在 ListView 或 GridView 中启用批处理上下文操作

1. 使用 `CHOICE_MODE_MULTIPLE_MODAL` 参数调用 `setChoiceMode()`。

2. 实现 `AbsListView.MultiChoiceModeListener` 接口，并使用 `setMultiChoiceModeListener()` 为View设置该接口。

   ``` java
   ListView listView = getListView();
   listView.setChoiceMode(ListView.CHOICE_MODE_MULTIPLE_MODAL);
   listView.setMultiChoiceModeListener(new MultiChoiceModeListener() {

       @Override
       public void onItemCheckedStateChanged(ActionMode mode, int position,
                                             long id, boolean checked) {
           // Here you can do something when items are selected/de-selected,
           // such as update the title in the CAB
       }

       @Override
       public boolean onActionItemClicked(ActionMode mode, MenuItem item) {
           // Respond to clicks on the actions in the CAB
           switch (item.getItemId()) {
               case R.id.menu_delete:
                   deleteSelectedItems();
                   mode.finish(); // Action picked, so close the CAB
                   return true;
               default:
                   return false;
           }
       }

       @Override
       public boolean onCreateActionMode(ActionMode mode, Menu menu) {
           // Inflate the menu for the CAB
           MenuInflater inflater = mode.getMenuInflater();
           inflater.inflate(R.menu.context, menu);
           return true;
       }

       @Override
       public void onDestroyActionMode(ActionMode mode) {
           // Here you can make any necessary updates to the activity when
           // the CAB is removed. By default, selected items are deselected/unchecked.
       }

       @Override
       public boolean onPrepareActionMode(ActionMode mode, Menu menu) {
           // Here you can perform updates to the CAB due to
           // an invalidate() request
           return false;
       }
   });
   ```

## **弹出菜单**

1. 实例化 `PopupMenu` 及其构造函数，该函数将提取当前应用的 `Context` 以及菜单应锚定到的 `View`。
2. 使用 `MenuInflater` 将菜单资源扩充到 `PopupMenu.getMenu()` 返回的 `Menu` 对象中。
3. 调用 `PopupMenu.show()`。

* API 级别 14 以下版本：

  ``` java
  PopupMenu popup = new PopupMenu(this, v);
  MenuInflater inflater = popup.getMenuInflater();
  inflater.inflate(R.menu.actions, popup.getMenu());
  popup.show();
  ```

* API 级别 14 及更高版本：

  ``` java
  PopupMenu popup = new PopupMenu(this, v);
  popup.inflate(R.menu.actions);
  popup.show();
  ```

当用户选择项目或触摸菜单以外的区域时，系统即会清除此菜单。 可使用 `PopupMenu.OnDismissListener`监听清除事件。

### 处理点击事件

要在用户选择菜单项时执行操作，您必须实现 `PopupMenu.OnMenuItemClickListener` 接口，并通过调用 `setOnMenuItemclickListener()` 将其注册到`PopupMenu`。用户选择项目时，系统会在接口中调用 `onMenuItemClick()` 回调。

``` java
public void showMenu(View v) {
    PopupMenu popup = new PopupMenu(this, v);

    // This activity implements OnMenuItemClickListener
    popup.setOnMenuItemClickListener(this);
    popup.inflate(R.menu.actions);
    popup.show();
}

@Override
public boolean onMenuItemClick(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.archive:
            archive(item);
            return true;
        case R.id.delete:
            delete(item);
            return true;
        default:
            return false;
    }
}
```

## 菜单组

- 使用 `setGroupVisible()` 显示或隐藏所有项目
- 使用 `setGroupEnabled()` 启用或禁用所有项目
- 使用 `setGroupCheckable()` 指定所有项目是否可选中

可以使用 `<item>` 元素中的 `android:checkable` 属性为各个菜单项定义可选中的行为，或者使用`<group>` 元素中的 `android:checkableBehavior` 属性为整个组定义可选中的行为，`android:checkableBehavior` 属性的取值可以为single、all、none。

可以使用 `<item>` 元素中的 `android:checked` 属性设置默认的选中状态，并可使用 `setChecked()` 方法在代码中更改此默认状态，并且可以使用 `isChecked()` 查询项目的当前状态。

``` xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <group android:checkableBehavior="single">
        <item android:id="@+id/red"
              android:title="@string/red" />
        <item android:id="@+id/blue"
              android:title="@string/blue" />
    </group>
</menu>
```

## 添加基于 Intent 的菜单项

1. 使用类别 `CATEGORY_ALTERNATIVE` 和（或） `CATEGORY_SELECTED_ALTERNATIVE` 以及任何其他要求定义 Intent。

2. 调用 `Menu.addIntentOptions()`。如果发现 Activity 提供的 Intent 过滤器与定义的 Intent 匹配，则会添加菜单项，并使用 Intent 过滤器 `android:label` 中的值作为菜单项标题，使用应用图标作为菜单项图标。`addIntentOptions()` 方法将返回已添加的菜单项数量。

   ``` java
   public boolean onCreateOptionsMenu(Menu menu){
       super.onCreateOptionsMenu(menu);

       // Create an Intent that describes the requirements to fulfill, to be included
       // in our menu. The offering app must include a category value of Intent.CATEGORY_ALTERNATIVE.
       Intent intent = new Intent(null, dataUri);
       intent.addCategory(Intent.CATEGORY_ALTERNATIVE);

       // Search and populate the menu with acceptable offering applications.
       menu.addIntentOptions(
            R.id.intent_group,  // Menu group to which new items will be added
            0,      // Unique item ID (none)
            0,      // Order for the items (none)
            this.getComponentName(),   // The current activity name
            null,   // Specific items to place first (none)
            intent, // Intent created above that describes our requirements
            0,      // Additional flags to control items (none)
            null);  // Array of MenuItems that correlate to specific items (none)

       return true;
   }
   ```

### 允许将 Activity 添加到其他菜单中

要包含在其他应用的菜单中，需要按常规方式定义 Intent 过滤器，但请确保为 Intent 过滤器类别添加 `CATEGORY_ALTERNATIVE` 和/或`CATEGORY_SELECTED_ALTERNATIVE` 值。

``` xml
<intent-filter label="@string/resize_image">
    ...
    <category android:name="android.intent.category.ALTERNATIVE" />
    <category android:name="android.intent.category.SELECTED_ALTERNATIVE" />
    ...
</intent-filter>
```

