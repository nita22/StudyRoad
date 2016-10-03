## Toggle Button

如果想要改变Toggle Button的开关状态，可以利用`CompoundButton.setChecked()`方法来设置。

### 处理点击事件

若想处理Toggle Button的点击事件，需要创建一个`CompoundButton.OnCheckedChangeListener`，并且通过`setOnCheckedChangeListener()`将Toggle Button绑定到该监听器上。

``` java
ToggleButton toggle = (ToggleButton) findViewById(R.id.togglebutton);
toggle.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
    public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
        if (isChecked) {
            // The toggle is enabled
        } else {
            // The toggle is disabled
        }
    }
});
```

