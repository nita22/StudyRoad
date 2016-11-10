## SharedPreferences

`SharedPreferences` 类能够保存和检索原始数据类型的永久性键值对。可以使用 `SharedPreferences` 来保存任何原始数据：布尔值、浮点值、整型值、长整型和字符串。

获取应用的 `SharedPreferences` 对象：

* `getSharedPreferences(String name, int mode)`：第一个参数是首选项文件名，第二个参数是操作模式，取值可以为0或`MODE_PRIVATE` 。

* `getPreferences(int mode)`：由于这将是用于 Activity 的唯一首选项文件，因此无需提供名称，只需要传入操作模式作为参数。

  ​

写入值：

1. 调用 `edit()` 以获取 `SharedPreferences.Editor` ；
2. 使用 `putBoolean()` 和 `putString()` 等方法添加值；
3. 使用 `commit()` 提交新值；



读取值：

使用 `getBoolean()` 和 `getString()` 等方法读取值。



``` java
public class Calc extends Activity {
    public static final String PREFS_NAME = "MyPrefsFile";

    @Override
    protected void onCreate(Bundle state){
       super.onCreate(state);
       . . .

       // Restore preferences
       SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
       boolean silent = settings.getBoolean("silentMode", false);
       setSilent(silent);
    }

    @Override
    protected void onStop(){
       super.onStop();

      // We need an Editor object to make preference changes.
      // All objects are from android.context.Context
      SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
      SharedPreferences.Editor editor = settings.edit();
      editor.putBoolean("silentMode", mSilentMode);

      // Commit the edits!
      editor.commit();
    }
}
```










