## EditText

### 指定键盘类型

可以通过`android:inputType`属性来指定弹出虚拟键盘的类型

``` xml
<EditText
    android:id="@+id/email_address"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:hint="@string/email_hint"
    android:inputType="textEmailAddress|
                       textCapWords|
                       textNoSuggestions" />
```

若想指定键盘的类型，`android:inputType`属性的值可以设置为`text`、`textEmailAddress`、`textUri`、`number`、`phone` 。



若想指定键盘的行为，，`android:inputType`属性的值可以设置为`textCapSentences`、`textCapWords`、`textAutoCorrect`、`textPassword`、`textMultiLine` 。



### 指定键盘的动作

通过设置`android:imeOptions`的属性来指定键盘的动作，可以利用`android:imeActionLabel`属性来设置自定义的标签文本。

* `android:imeOptions="flagNoExtractUi"`     使软键盘不全屏显示，只占用一部分屏幕    

* `android:imeOptions="actionNone"`     输入框右侧不带任何提示    

* `android:imeOptions="actionGo" `      右下角按键内容为'开始'    

* `android:imeOptions="actionSearch"`     右下角按键为放大镜图片，搜索    

* `android:imeOptions="actionSend"`       右下角按键内容为'发送'    

* `android:imeOptions="actionNext" `     右下角按键内容为'下一步'    

* `android:imeOptions="actionDone"`     右下角按键内容为'完成'   

``` xml
<EditText
    android:id="@+id/search"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:hint="@string/search_hint"
    android:inputType="text"
    android:imeOptions="actionSend" />
```

#### 监听键盘动作

可以利用` TextView.OnEditorActionListener`提供的`onEditorAction() `方法来监听键盘的动作。

``` java
EditText editText = (EditText) findViewById(R.id.search);
editText.setOnEditorActionListener(new OnEditorActionListener() {
    @Override
    public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
        boolean handled = false;
        if (actionId == EditorInfo.IME_ACTION_SEND) {
            sendMessage();
            handled = true;
        }
        return handled;
    }
});
```

### 提供自动完成建议

想要提供自动完成建议，需要使用EditText的子类`AutoCompleteTextView`。同时需要指定Adapter来提供文本输入的建议。

``` xml
<AutoCompleteTextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/autocomplete_country"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
```

``` xml
<resources>
    <string-array name="countries_array">
        <item>Afghanistan</item>
        <item>Albania</item>
        <item>Algeria</item>
        <item>American Samoa</item>
        <item>Andorra</item>
        <item>Angola</item>
        <item>Anguilla</item>
        <item>Antarctica</item>
        ...
    </string-array>
</resources>
```

``` java
// Get a reference to the AutoCompleteTextView in the layout
AutoCompleteTextView textView = (AutoCompleteTextView) findViewById(R.id.autocomplete_country);
// Get the string array
String[] countries = getResources().getStringArray(R.array.countries_array);
// Create the adapter and set it to the AutoCompleteTextView
ArrayAdapter<String> adapter =
        new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, countries);
textView.setAdapter(adapter);
```



