# 设置

## 使用 XML 定义首选项

XML 文件的根节点必须是一个`<PreferenceScreen>`元素。可以在此元素内添加每个 `Preference`。在 `<PreferenceScreen>`元素内添加的每个子项均将作为单独的项目显示在设置列表中。

``` xml
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    <CheckBoxPreference
        android:key="pref_sync"
        android:title="@string/pref_sync"
        android:summary="@string/pref_sync_summ"
        android:defaultValue="true" />
    <ListPreference
        android:dependency="pref_sync"
        android:key="pref_syncConnectionType"
        android:title="@string/pref_syncConnectionType"
        android:dialogTitle="@string/pref_syncConnectionType"
        android:entries="@array/pref_syncConnectionTypes_entries"
        android:entryValues="@array/pref_syncConnectionTypes_values"
        android:defaultValue="@string/pref_syncConnectionTypes_default" />
</PreferenceScreen>
```

### 创建设置组

* #### 使用标题

  以分隔线分隔两组设置并为其提供标题，需要将每组 `Preference` 对象放入`PreferenceCategory` 内。

  ``` xml
  <PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
      <PreferenceCategory
          android:title="@string/pref_sms_storage_title"
          android:key="pref_key_storage_settings">
          <CheckBoxPreference
              android:key="pref_key_auto_delete"
              android:summary="@string/pref_summary_auto_delete"
              android:title="@string/pref_title_auto_delete"
              android:defaultValue="false"... />
          <Preference
              android:key="pref_key_sms_delete_limit"
              android:dependency="pref_key_auto_delete"
              android:summary="@string/pref_summary_delete_limit"
              android:title="@string/pref_title_sms_delete"... />
          <Preference
              android:key="pref_key_mms_delete_limit"
              android:dependency="pref_key_auto_delete"
              android:summary="@string/pref_summary_delete_limit"
              android:title="@string/pref_title_mms_delete" ... />
      </PreferenceCategory>
      ...
  </PreferenceScreen>
  ```

* #### 使用子屏幕

  将设置组放入子屏幕，需要将 `Preference` 对象组放入 `PreferenceScreen` 内。

  ``` xml
  <PreferenceScreen  xmlns:android="http://schemas.android.com/apk/res/android">
      <!-- opens a subscreen of settings -->
      <PreferenceScreen
          android:key="button_voicemail_category_key"
          android:title="@string/voicemail"
          android:persistent="false">
          <ListPreference
              android:key="button_voicemail_provider_key"
              android:title="@string/voicemail_provider" ... />
          <!-- opens another nested subscreen -->
          <PreferenceScreen
              android:key="button_voicemail_setting_key"
              android:title="@string/voicemail_settings"
              android:persistent="false">
              ...
          </PreferenceScreen>
          <RingtonePreference
              android:key="button_voicemail_ringtone_key"
              android:title="@string/voicemail_ringtone_title"
              android:ringtoneType="notification" ... />
          ...
      </PreferenceScreen>
      ...
  </PreferenceScreen>
  ```

### 使用 Intent

要在用户选择首选项时调用 `Intent`，请将`<intent>` 元素添加为相应 `<Preference>` 元素的子元素。

```  xml
<Preference android:title="@string/prefs_web_page" >
    <intent android:action="android.intent.action.VIEW"
            android:data="http://www.example.com" />
</Preference>
```

`android:action` ：要分配的操作（按照 `setAction()` 方法）。

`android:data` ：要分配的数据（按照 `setData()` 方法）。

`android:mimeType` ：要分配的 MIME 类型（按照 `setType()` 方法）。

`android:targetClass` ：组件名称的类部分（按照 `setComponent()` 方法）。

`android:targetPackage` ：组件名称的软件包部分（按照 `setComponent()` 方法）。



## 创建首选项 Activity

要在 Activity 中显示您的设置，请扩展 `PreferenceActivity` 类，该类根据 `Preference` 对象的层次结构显示设置列表。当用户进行更改时，`PreferenceActivity` 会自动保留与每个 `Preference` 相关的设置。

``` java
public class SettingsActivity extends PreferenceActivity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.preferences);
    }
}
```

## 使用首选项Fragment

使用 `PreferenceFragment` 显示 `Preference` 对象的列表。

``` java
public static class SettingsFragment extends PreferenceFragment {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Load the preferences from an XML resource
        addPreferencesFromResource(R.xml.preferences);
    }
    ...
}
```

``` java
public class SettingsActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Display the fragment as the main content.
        getFragmentManager().beginTransaction()
                .replace(android.R.id.content, new SettingsFragment())
                .commit();
    }
}
```

## 设置默认值

1. 使用 `android:defaultValue` 属性为 XML 文件中的每个 `Preference` 对象指定默认值。

   ``` xml
   <!-- default value is a boolean -->
   <CheckBoxPreference
       android:defaultValue="true"
       ... />

   <!-- default value is a string -->
   <ListPreference
       android:defaultValue="@string/pref_syncConnectionTypes_default"
       ... />
   ```

2. 通过应用的主 Activity中的 `onCreate()` 方法调用 `setDefaultValues()`：

   ``` java
   PreferenceManager.setDefaultValues(this, R.xml.advanced_preferences, false);
   ```

   `setDefaultValues()`的第一个参数是Context；第二个参数是要为其设置默认值的首选项 XML 文件的资源 ID

   ；第三个参数用于指示是否应该多次设置默认值，如果该值为 `false`，则仅当过去从未调用此方法时，系统才会设置默认值。如果将它设置为 `true`，则需要使用默认值替代之前的所有值。

## 使用首选项标头

   要使用标头构建设置，您需要：

1. 将每组设置分成单独的 `PreferenceFragment` 实例。即每组设置均需要一个单独的 XML 文件。
2. 创建 XML 标头文件，其中列出每个设置组并声明哪个Fragment包含对应的设置列表。
3. 扩展 `PreferenceActivity` 类以托管设置。
4. 实现 `onBuildHeaders()` 回调以指定标头文件。

### 创建标头文件

标头列表中的每组设置均由根 `<preference-headers>` 元素内的单个 `<header>` 元素指定。

``` xml
<preference-headers xmlns:android="http://schemas.android.com/apk/res/android">
    <header
        android:fragment="com.example.prefs.SettingsActivity$SettingsFragmentOne"
        android:title="@string/prefs_category_one"
        android:summary="@string/prefs_summ_category_one" />
    <header
        android:fragment="com.example.prefs.SettingsActivity$SettingsFragmentTwo"
        android:title="@string/prefs_category_two"
        android:summary="@string/prefs_summ_category_two" >
        <!-- key/value pairs can be included as arguments for the fragment. -->
        <extra android:name="someKey" android:value="someHeaderValue" />
    </header>
</preference-headers>
```

`<extras>` 元素允许您使用 `Bundle` 将键值对传递给Fragment。该Fragment可以通过调用 `getArguments()` 检索参数。

### 显示标头

要显示首选项标头，您必须实现 `onBuildHeaders()` 回调方法并调用 `loadHeadersFromResource()`。只有在 Androd 3.0 或更高版本中运行时，系统才会在您的`PreferenceActivity` 中调用 `onBuildHeaders()`。

``` java
public class SettingsActivity extends PreferenceActivity {
    @Override
    public void onBuildHeaders(List<Header> target) {
        loadHeadersFromResource(R.xml.preference_headers, target);
    }
}
```

### 使用首选项标头支持早于 3.0 的 Android 版本

每个` <Preference>`元素均会向 `PreferenceActivity` 发送一个 `Intent`，指定要加载哪个首选项 XML 文件。

``` xml
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    <Preference
        android:title="@string/prefs_category_one"
        android:summary="@string/prefs_summ_category_one"  >
        <intent
            android:targetPackage="com.example.prefs"
            android:targetClass="com.example.prefs.SettingsActivity"
            android:action="com.example.prefs.PREFS_ONE" />
    </Preference>
    <Preference
        android:title="@string/prefs_category_two"
        android:summary="@string/prefs_summ_category_two" >
        <intent
            android:targetPackage="com.example.prefs"
            android:targetClass="com.example.prefs.SettingsActivity"
            android:action="com.example.prefs.PREFS_TWO" />
    </Preference>
</PreferenceScreen>
```

连续调用 `addPreferencesFromResource()` 会将所有首选项堆叠在一个列表中，因此请将条件与 else-if 语句链接在一起，确保它只调用一次。

``` java
final static String ACTION_PREFS_ONE = "com.example.prefs.PREFS_ONE";
...

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    String action = getIntent().getAction();
    if (action != null && action.equals(ACTION_PREFS_ONE)) {
        addPreferencesFromResource(R.xml.preferences);
    }
    ...

    else if (Build.VERSION.SDK_INT < Build.VERSION_CODES.HONEYCOMB) {
        // Load the legacy preferences headers
        addPreferencesFromResource(R.xml.preference_headers_legacy);
    }
}
```

## 读取首选项

应用的所有首选项均保存到一个可通过调用静态方法 `PreferenceManager.getDefaultSharedPreferences()` 从应用内的任何位置访问的文件中。这将返回 `SharedPreferences` 对象，其中包含与 `PreferenceActivity` 中所用 `Preference` 对象相关的所有键值对。

``` java
SharedPreferences sharedPref = PreferenceManager.getDefaultSharedPreferences(this);
String syncConnPref = sharedPref.getString(SettingsActivity.KEY_PREF_SYNC_CONN, "");
```

### 监听首选项变化

要在任一首选项发生更改时收到回调，请实现`SharedPreference.OnSharedPreferenceChangeListener` 接口，并通过调用 `registerOnSharedPreferenceChangeListener()` 为 `SharedPreferences` 对象注册监听器。

``` java
public class SettingsActivity extends PreferenceActivity
                              implements OnSharedPreferenceChangeListener {
    public static final String KEY_PREF_SYNC_CONN = "pref_syncConnectionType";
    ...

    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences,
        String key) {
        if (key.equals(KEY_PREF_SYNC_CONN)) {
            Preference connectionPref = findPreference(key);
            // Set summary to be the user-description for the selected value
            connectionPref.setSummary(sharedPreferences.getString(key, ""));
        }
    }
}
```

需要在 `onResume()` 和 `onPause()` 回调期间分别注册和注销`SharedPreferences.OnSharedPreferenceChangeListener`。

``` java
@Override
protected void onResume() {
    super.onResume();
    getPreferenceScreen().getSharedPreferences()
            .registerOnSharedPreferenceChangeListener(this);
}

@Override
protected void onPause() {
    super.onPause();
    getPreferenceScreen().getSharedPreferences()
            .unregisterOnSharedPreferenceChangeListener(this);
}
```

建议对监听器的引用存储在只要需要监听器就会存在的对象的实例数据字段中：

``` java
SharedPreferences.OnSharedPreferenceChangeListener listener =
    new SharedPreferences.OnSharedPreferenceChangeListener() {
  public void onSharedPreferenceChanged(SharedPreferences prefs, String key) {
    // listener implementation
  }
};
prefs.registerOnSharedPreferenceChangeListener(listener);
```

## 自定义首选项

### 指定用户界面

扩展 `DialogPreference` 时，必须在类构造函数中调用 `setDialogLayoutResourcs()` 来指定对话框的布局。

``` java
public class NumberPickerPreference extends DialogPreference {
    public NumberPickerPreference(Context context, AttributeSet attrs) {
        super(context, attrs);

        setDialogLayoutResource(R.layout.numberpicker_dialog);
        setPositiveButtonText(android.R.string.ok);
        setNegativeButtonText(android.R.string.cancel);

        setDialogIcon(null);
    }
    ...
}
```

### 保存设置的值

如果设置的值为整型数或是用于保存布尔值的 `persistBoolean()`，则可通过调用 `Preference` 类的一个 `persist*()` 方法（如 `persistInt()`）随时保存该值。

### 初始化当前值

系统将 `Preference` 添加到屏幕时，会调用 `onSetInitialValue()` 方法。`onSetInitialValue()` 方法传递一个布尔值 (`restorePersistedValue`)，以指示是否已为该设置保留值。 如果值为 `true`，则应通过调用 `Preference` 类的一个 `getPersisted*()` 方法来检索保留值。如果 `restorePersistedValue` 为 `false`，则应使用在第二个参数中传递的默认值。

 `getPersisted*()` 方法中的参数用于指定在实际上没有保留值或该键不存在时所要使用的默认值。

``` java
@Override
protected void onSetInitialValue(boolean restorePersistedValue, Object defaultValue) {
    if (restorePersistedValue) {
        // Restore existing state
        mCurrentValue = this.getPersistedInt(DEFAULT_VALUE);
    } else {
        // Set default state from the XML attribute
        mCurrentValue = (Integer) defaultValue;
        persistInt(mCurrentValue);
    }
}
```

不能使用 `defaultValue` 作为 `getPersisted*()` 方法中的默认值，因为当 `restorePersistedValue` 为 `true` 时，其值始终为 null。

### 提供默认值

如果 `Preference` 类的实例指定一个默认值（使用 `android:defaultValue` 属性），则在实例化对象以检索该值时，系统会调用 `onGetDefaultValue()`。必须实现此方法，系统才能将默认值保存在 `SharedPreferences` 中。

``` java
@Override
protected Object onGetDefaultValue(TypedArray a, int index) {
    return a.getInteger(index, DEFAULT_VALUE);
}
```

### 保存和恢复首选项的状态

要正确保存并恢复 `Preference`类的状态，您必须实现生命周期回调方法 `onSaveInstanceState()` 和 `onRestoreInstanceState()`。

要定义 `Preference` 类保存其状态的方式，您应该扩展 `Preference.BaseSavedState` 类，该类实现 `Parcelable` 接口。

``` java
private static class SavedState extends BaseSavedState {
    // Member that holds the setting's value
    // Change this data type to match the type saved by your Preference
    int value;

    public SavedState(Parcelable superState) {
        super(superState);
    }

    public SavedState(Parcel source) {
        super(source);
        // Get the current preference's value
        value = source.readInt();  // Change this to read the appropriate data type
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        super.writeToParcel(dest, flags);
        // Write the preference's value
        dest.writeInt(value);  // Change this to write the appropriate data type
    }

    // Standard creator object using an instance of this class
    public static final Parcelable.Creator<SavedState> CREATOR =
            new Parcelable.Creator<SavedState>() {

        public SavedState createFromParcel(Parcel in) {
            return new SavedState(in);
        }

        public SavedState[] newArray(int size) {
            return new SavedState[size];
        }
    };
}
```

``` java
@Override
protected Parcelable onSaveInstanceState() {
    final Parcelable superState = super.onSaveInstanceState();
    // Check whether this Preference is persistent (continually saved)
    if (isPersistent()) {
        // No need to save instance state since it's persistent,
        // use superclass state
        return superState;
    }

    // Create instance of custom BaseSavedState
    final SavedState myState = new SavedState(superState);
    // Set the state's value with the class member that holds current
    // setting value
    myState.value = mNewValue;
    return myState;
}

@Override
protected void onRestoreInstanceState(Parcelable state) {
    // Check whether we saved the state in onSaveInstanceState
    if (state == null || !state.getClass().equals(SavedState.class)) {
        // Didn't save the state, so call superclass
        super.onRestoreInstanceState(state);
        return;
    }

    // Cast state to custom BaseSavedState and pass to superclass
    SavedState myState = (SavedState) state;
    super.onRestoreInstanceState(myState.getSuperState());

    // Set this Preference's widget to reflect the restored state
    mNumberPicker.setValue(myState.value);
}
```











