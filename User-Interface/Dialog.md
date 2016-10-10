# 对话框

建议将 `DialogFragment` 用作对话框的容器。使用 `DialogFragment` 管理对话框可确保它能正确处理生命周期事件，`DialogFragment` 类还允许您将对话框的 UI 作为嵌入式组件在较大 UI 中重复使用。

## 创建DialogFragment

通过扩展 `DialogFragment` 并在 `onCreateDialog()` 回调方法中创建`AlertDialog`。

``` java
public class FireMissilesDialogFragment extends DialogFragment {
    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
        // Use the Builder class for convenient dialog construction
        AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
        builder.setMessage(R.string.dialog_fire_missiles)
               .setPositiveButton(R.string.fire, new DialogInterface.OnClickListener() {
                   public void onClick(DialogInterface dialog, int id) {
                       // FIRE ZE MISSILES!
                   }
               })
               .setNegativeButton(R.string.cancel, new DialogInterface.OnClickListener() {
                   public void onClick(DialogInterface dialog, int id) {
                       // User cancelled the dialog
                   }
               });
        // Create the AlertDialog object and return it
        return builder.create();
    }
}
```

## 构建AlertDialog

``` java
AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());

builder.setMessage(R.string.dialog_message)
       .setTitle(R.string.dialog_title);

AlertDialog dialog = builder.create();
```

### 添加按钮

对于每种按钮类型，只能为 `AlertDialog` 添加一个该类型的按钮。

调用 `setPositiveButton()` 、 `setNegativeButton()` 、`setNeutralButton()`方法：

``` java
AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
// Add the buttons
builder.setPositiveButton(R.string.ok, new DialogInterface.OnClickListener() {
           public void onClick(DialogInterface dialog, int id) {
               // User clicked OK button
           }
       });
builder.setNegativeButton(R.string.cancel, new DialogInterface.OnClickListener() {
           public void onClick(DialogInterface dialog, int id) {
               // User cancelled the dialog
           }
       });
// Set other dialog properties
...

// Create the AlertDialog
AlertDialog dialog = builder.create();
```

### 添加列表

- 传统单选列表

  使用 `setItems()` 方法或者使用 `setAdapter()` 指定一个列表。

  ``` java
  @Override
  public Dialog onCreateDialog(Bundle savedInstanceState) {
      AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
      builder.setTitle(R.string.pick_color)
             .setItems(R.array.colors_array, new DialogInterface.OnClickListener() {
                 public void onClick(DialogInterface dialog, int which) {
                 // The 'which' argument contains the index position
                 // of the selected item
             }
      });
      return builder.create();
  }
  ```

- 永久性单选列表（单选按钮）

  使用`setSingleChoiceItems()` 方法。

- 永久性多选列表（复选框）

  使用 `setMultiChoiceItems()`方法。其中第一个参数是列表数组；第二个参数是默认选中的子项；第三个参数是监听选择子项事件的监听器。

  ``` java
  @Override
  public Dialog onCreateDialog(Bundle savedInstanceState) {
      mSelectedItems = new ArrayList();  // Where we track the selected items
      AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
      // Set the dialog title
      builder.setTitle(R.string.pick_toppings)
      // Specify the list array, the items to be selected by default (null for none),
      // and the listener through which to receive callbacks when items are selected
             .setMultiChoiceItems(R.array.toppings, null,
                        new DialogInterface.OnMultiChoiceClickListener() {
                 @Override
                 public void onClick(DialogInterface dialog, int which,
                         boolean isChecked) {
                     if (isChecked) {
                         // If the user checked the item, add it to the selected items
                         mSelectedItems.add(which);
                     } else if (mSelectedItems.contains(which)) {
                         // Else, if the item is already in the array, remove it
                         mSelectedItems.remove(Integer.valueOf(which));
                     }
                 }
             })
      // Set the action buttons
             .setPositiveButton(R.string.ok, new DialogInterface.OnClickListener() {
                 @Override
                 public void onClick(DialogInterface dialog, int id) {
                     // User clicked OK, so save the mSelectedItems results somewhere
                     // or return them to the component that opened the dialog
                     ...
                 }
             })
             .setNegativeButton(R.string.cancel, new DialogInterface.OnClickListener() {
                 @Override
                 public void onClick(DialogInterface dialog, int id) {
                     ...
                 }
             });

      return builder.create();
  }
  ```

### 创建自定义布局

创建一个布局，通过 `getLayoutInflater()` 获取一个 `LayoutInflater` 并调用 `inflate()`，其中第一个参数是布局资源 ID，第二个参数是布局的父视图。然后通过调用 `AlertDialog.Builder` 对象上的 `setView()` 将其添加到 `AlertDialog`。

``` java
@Override
public Dialog onCreateDialog(Bundle savedInstanceState) {
    AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
    // Get the layout inflater
    LayoutInflater inflater = getActivity().getLayoutInflater();

    // Inflate and set the layout for the dialog
    // Pass null as the parent view because its going in the dialog layout
    builder.setView(inflater.inflate(R.layout.dialog_signin, null))
    // Add action buttons
           .setPositiveButton(R.string.signin, new DialogInterface.OnClickListener() {
               @Override
               public void onClick(DialogInterface dialog, int id) {
                   // sign in the user ...
               }
           })
           .setNegativeButton(R.string.cancel, new DialogInterface.OnClickListener() {
               public void onClick(DialogInterface dialog, int id) {
                   LoginDialogFragment.this.getDialog().cancel();
               }
           });
    return builder.create();
}
```

## 显示对话框

创建一个 `DialogFragment` 实例并调用 `show()`，以传递对话框片段的 `FragmentManager` 和标记名称。

``` java
DialogFragment newFragment = new FireMissilesDialogFragment();
newFragment.show(getSupportFragmentManager(), "missiles");
```

## Dialog作为嵌入式 Fragment

 `DialogFragment` 可以显示为对话框或嵌入式片段。想让 `DialogFragment` 具有嵌入能力，则必须在布局中定义对话框的 UI，然后在 `onCreateView()` 回调中加载布局。

``` java
public class CustomDialogFragment extends DialogFragment {
    /** The system calls this to get the DialogFragment's layout, regardless
        of whether it's being displayed as a dialog or an embedded fragment. */
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
            Bundle savedInstanceState) {
        // Inflate the layout to use as dialog or embedded fragment
        return inflater.inflate(R.layout.purchase_items, container, false);
    }

    /** The system calls this only when creating the layout in a dialog. */
    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
        // The only reason you might override this method when using onCreateView() is
        // to modify any dialog characteristics. For example, the dialog includes a
        // title by default, but your custom layout might not need it. So here you can
        // remove the dialog title, but you must call the superclass to get the Dialog.
        Dialog dialog = super.onCreateDialog(savedInstanceState);
        dialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        return dialog;
    }
}
```

``` java
public void showDialog() {
    FragmentManager fragmentManager = getSupportFragmentManager();
    CustomDialogFragment newFragment = new CustomDialogFragment();

    if (mIsLargeLayout) {
        // The device is using a large layout, so show the fragment as a dialog
        newFragment.show(fragmentManager, "dialog");
    } else {
        // The device is smaller, so show the fragment fullscreen
        FragmentTransaction transaction = fragmentManager.beginTransaction();
        // For a little polish, specify a transition animation
        transaction.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_OPEN);
        // To make it fullscreen, use the 'content' root view as the container
        // for the fragment, which is always the root view for the activity
        transaction.add(android.R.id.content, newFragment)
                   .addToBackStack(null).commit();
    }
}
```

## 将 Activity 显示为对话框

``` java
<activity android:theme="@android:style/Theme.Holo.Dialog" >
```

要想仅在大屏幕上将 Activity 显示为对话框：

```xml
<activity android:theme="@android:style/Theme.Holo.DialogWhenLarge" >
```

 ## 清除对话框

* 清除对话框

  默认情况下，当用户触摸使用 `AlertDialog.Builder` 创建的任何操作按钮时，系统会清除对话框。

  可以通过在 `DialogFragment` 上调用 `dismiss()` 来手动清除对话框。

  如需在对话框消失时执行特定操作，则可以在 `DialogFragment` 中实现 `onDismiss()` 方法。

* 取消对话框

  用户按“返回”按钮，触摸对话框区域外部的屏幕，或者在`Dialog` 上显式调用 `cancel()`。

  可以通过在您的 `DialogFragment` 中实现 `onCancel()` 来响应取消事件。

系统会在每个调用`onCancel()`回调的事件发生时立即调用`onDismiss()`。

  

