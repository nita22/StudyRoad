## CheckBox

`isChecked()`方法可以返回CheckBox是否被选中的Boolean值，可以使用`setChecked(boolean)`属性来设置CheckBox是否被选中。

### 处理点击事件

* 1.在\<CheckBox\>标签中使用`android:onClick`属性设置点击事件。

  ``` xm
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      android:orientation="vertical"
      android:layout_width="fill_parent"
      android:layout_height="fill_parent">
      <CheckBox android:id="@+id/checkbox_meat"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:text="@string/meat"
          android:onClick="onCheckboxClicked"/>
      <CheckBox android:id="@+id/checkbox_cheese"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:text="@string/cheese"
          android:onClick="onCheckboxClicked"/>
  </LinearLayout>
  ```

* 2.在Java代码中实现点击事件的方法。方法必须为public、返回值必须为void、接收View作为唯一参数。

  ``` java
  public void onCheckboxClicked(View view) {
      // Is the view now checked?
      boolean checked = ((CheckBox) view).isChecked();

      // Check which checkbox was clicked
      switch(view.getId()) {
          case R.id.checkbox_meat:
              if (checked)
                  // Put some meat on the sandwich
              else
                  // Remove the meat
              break;
          case R.id.checkbox_cheese:
              if (checked)
                  // Cheese me
              else
                  // I'm lactose intolerant
              break;
          // TODO: Veggie sandwich
      }
  }
  ```

  ​