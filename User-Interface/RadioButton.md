## RadioButton

必须要将一组RadioButton放置在一个RadioGroup当中，以实现单选的效果。RadioGroup是LinearLayout的子类，其子元素排列的默认方向为竖直方向。

如果要设置RadioButton的选中状态，可以通过`setChecked(boolean) `方法设置。可以通过`isChecked()`方法来获取RadioButton的选中状态。

### 处理点击事件

* 1.利用`android:onClick`属性设置点击事件。

``` xml
<RadioGroup xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">
    <RadioButton android:id="@+id/radio_pirates"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/pirates"
        android:onClick="onRadioButtonClicked"/>
    <RadioButton android:id="@+id/radio_ninjas"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/ninjas"
        android:onClick="onRadioButtonClicked"/>
</RadioGroup>
```

* 2.在Java代码中实现点击方法。该方法必须为public、返回值必须为void、接收View为唯一参数。

  ``` java
  public void onRadioButtonClicked(View view) {
      // Is the button now checked?
      boolean checked = ((RadioButton) view).isChecked();

      // Check which radio button was clicked
      switch(view.getId()) {
          case R.id.radio_pirates:
              if (checked)
                  // Pirates are the best
              break;
          case R.id.radio_ninjas:
              if (checked)
                  // Ninjas rule
              break;
      }
  }
  ```

  ​