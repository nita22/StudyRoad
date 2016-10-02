## ListView
### 使用数据填充适配器视图
通过将`ListView`实例与 Adapter 绑定来填充`ListView`，此操作会从外部来源检索数据，并创建表示每个数据条目的 View。<br>

* ArrayAdapter<br>
默认情况下，`ArrayAdapter`会通过在每个项目上调用`toString()`并将内容放入 TextView 来为每个数组项创建视图。要想自定义每个项的外观，您可以重写数组中各个对象的`toString()`方法。或者，要想为 TextView 之外的每个项创建视图，请扩展`ArrayAdapter`类并重写`getView()`以返回您想要为每个项获取的视图类型。
``` java
ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
        android.R.layout.simple_list_item_1, myStringArray);
ListView listView = (ListView) findViewById(R.id.listview);
listView.setAdapter(adapter);
```

* SimpleCursorAdapter<br>
使用`SimpleCursorAdapter`时，您必须指定要为 Cursor 中的每个行使用的布局，以及应该在哪些布局视图中插入 Cursor 中的哪些列。<br>
``` java
String[] fromColumns = {ContactsContract.Data.DISPLAY_NAME,
                        ContactsContract.CommonDataKinds.Phone.NUMBER};
int[] toViews = {R.id.display_name, R.id.phone_number};
SimpleCursorAdapter adapter = new SimpleCursorAdapter(this,
        R.layout.person_name_and_number, cursor, fromColumns, toViews, 0);
ListView listView = getListView();
listView.setAdapter(adapter);
```
### 刷新数据
如果您在应用的生命周期中更改了适配器读取的底层数据，则应调用`notifyDataSetChanged()`。此操作会通知附加的视图，数据发生了变化，它应该自行刷新。

### 处理点击事件
可以通过实现`AdapterView.OnItemClickListener`界面来响应`ListView`中每一项上的点击事件。
``` java
private OnItemClickListener mMessageClickedHandler = new OnItemClickListener() {
    public void onItemClick(AdapterView parent, View v, int position, long id) {
        // Do something in response to the click
    }
};

listView.setOnItemClickListener(mMessageClickedHandler);
```

