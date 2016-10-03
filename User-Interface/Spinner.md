## Spinner

### 添加Spinner到布局中

``` xml
<Spinner
    android:id="@+id/planets_spinner"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
```

### 设置Spinner的可选选项

如果想要指定Spinner的可选选项，可以在Java代码中指定`SpinnerAdapter`，`SpinnerAdapter`可以是`ArrayAdapter`，也可以是`CursorAdapter` 。

``` xml
<resources>
    <string-array name="planets_array">
        <item>Mercury</item>
        <item>Venus</item>
        <item>Earth</item>
        <item>Mars</item>
        <item>Jupiter</item>
        <item>Saturn</item>
        <item>Uranus</item>
        <item>Neptune</item>
    </string-array>
</resources>
```

``` java
Spinner spinner = (Spinner) findViewById(R.id.spinner);
// Create an ArrayAdapter using the string array and a default spinner layout
ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(this,
        R.array.planets_array, android.R.layout.simple_spinner_item);
// Specify the layout to use when the list of choices appears
adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
// Apply the adapter to the spinner
spinner.setAdapter(adapter);
```

### 处理选择子项事件

要想处理选择子项的事件，需要创建一个`AdapterView.OnItemSelectedListener`接口监听选择子项，并且通过`setOnItemSelectedListener()`将Spinner绑定到该监听器上。

实现` AdapterView.OnItemSelectedListener`接口，需要实现`onItemSelected()`以及`onNothingSelected()`方法。

``` java
public class SpinnerActivity extends Activity implements OnItemSelectedListener {
    ...

    public void onItemSelected(AdapterView<?> parent, View view,
            int pos, long id) {
        // An item was selected. You can retrieve the selected item using
        // parent.getItemAtPosition(pos)
    }

    public void onNothingSelected(AdapterView<?> parent) {
        // Another interface callback
    }
}
```

``` java
Spinner spinner = (Spinner) findViewById(R.id.spinner);
spinner.setOnItemSelectedListener(this);
```

