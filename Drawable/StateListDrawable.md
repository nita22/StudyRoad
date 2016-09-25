## StateListDrawable
StateListDrawable从上而下检索\<item\>标签，选择第一个能够匹配状态的\<item\>标签，因此默认的\<item\>应该放在最后一个。<br>

* 文件位置：res/drawable/filename.xml
* 引用资源：
  * Java代码中引用：`R.drawable.filename`
  * XML代码中引用：`@[package:]drawable/filename`

``` xml
<selector xmlns:android="http://schemas.android.com/apk/res/android"
    android:constantSize=["true" | "false"]
    android:dither=["true" | "false"]
    android:variablePadding=["true" | "false"] >
    <item
        android:drawable="@[package:]drawable/drawable_resource"
        android:state_pressed=["true" | "false"]
        android:state_focused=["true" | "false"]
        android:state_hovered=["true" | "false"]
        android:state_selected=["true" | "false"]
        android:state_checkable=["true" | "false"]
        android:state_checked=["true" | "false"]
        android:state_enabled=["true" | "false"]
        android:state_activated=["true" | "false"]
        android:state_window_focused=["true" | "false"] />
</selector>
```

`android:constantSize`：false表示StateListDrawable的大小随着状态变化而变化，true代表StateListDrawable的固有大小保持不变（以最大的为准）。默认为false <br>
`android:dither`：true表示，如果一个屏幕中位图有这不同的像素配置，启用位图的抖动。false表示不启用位图的抖动<br>
`android:variablePadding`：选择true时，drawable的内边距会根据状态的变化而变化。选择false时，内边距保持一致，为所有状态中最大的内边距。默认为false<br>

|状态         | 含义           |
| ------------- |:-------------:|
|android:state_pressed|用户点击或者触摸该控件的状态|
|android:state_focused|当前控件获得焦点时的状态|
|android:state_hovered|光标移动到当前控件上的状态|
|android:state_selected|被选择的状态|
|android:state_checkable|可以被勾选的状态|
|android:state_checked|当前控件处于被勾选|
|android:state_enabled|当前控件处于可用的状态|
|android:state_activated|当前控件被激活的状态|
|android:state_window_focused|当前控件处于最前端时，应用窗口获得焦点的状态|


范例：
``` xml
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true"
          android:drawable="@drawable/button_pressed" /> <!-- pressed -->
    <item android:state_focused="true"
          android:drawable="@drawable/button_focused" /> <!-- focused -->
    <item android:state_hovered="true"
          android:drawable="@drawable/button_focused" /> <!-- hovered -->
    <item android:drawable="@drawable/button_normal" /> <!-- default -->
</selector>
```
