# 搜索控件

## 编写searchable configuration

需要在`res/xml/`目录下创建一个XML文件作为配置文件，用于设定搜索框的功能和样子。

``` xml
<searchable xmlns:android="http://schemas.android.com/apk/res/android"
    android:label="@string/app_label"
    android:hint="@string/search_hint" >
</searchable>
```

`android:label`是必需的属性，应该使用该应用的名字作为该属性的值。使用简单的搜索功能时，该label是不可见的，只有将你的搜索功能加入到quick search box的时候，才会在quick search box的可搜索列表中出现。

`android：hint`是搜索栏的提示文本。

## 创建Searchable Activity

### 声明Searchable Activity

在AndroidManifest配置文件中声明Activity为Searchable Activity。

1. `<intent-filter>`标签中声明接收`ACTION_SEARCH`intent。
2. 在`<meta-data>`标签中`android:name`的值必须为`android.app.searchable`，指明该Activity对`android.intent.action.SEARCH`敏感，可进行搜索工作。

``` xml
<application ... >
    <activity android:name=".SearchableActivity" >
        <intent-filter>
            <action android:name="android.intent.action.SEARCH" />
        </intent-filter>
        <meta-data android:name="android.app.searchable"
                   android:resource="@xml/searchable"/>
    </activity>
    ...
</application>
```

### 进行搜索

1. 接收查询请求

   当用户提交搜索请求时，系统会启动并且会发送`ACTION_SEARCH`的intent到Searchable Activity，该intent中会包含附加信息，需要在Searchable Activity中获取该附加信息。

   ``` java
   public void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       setContentView(R.layout.search);

       // Get the intent, verify the action and get the query
       Intent intent = getIntent();
       if (Intent.ACTION_SEARCH.equals(intent.getAction())) {
         String query = intent.getStringExtra(SearchManager.QUERY);
         doMySearch(query);
       }
   }
   ```

2. 在数据源中搜索

   利用数据库或者网络相关的API在数据库或网络上查找结果。

3. 展示搜索结果

   通常通过ListView来展示搜索的结果。

## Search Dialog

1. 在AndroidManifest配置文件中声明Searchable Activity应该接受Search Dialog的搜索请求：

   ``` xml
   <application ... >
       <!-- this is the searchable activity; it performs searches -->
       <activity android:name=".SearchableActivity" >
           <intent-filter>
               <action android:name="android.intent.action.SEARCH" />
           </intent-filter>
           <meta-data android:name="android.app.searchable"
                      android:resource="@xml/searchable"/>
       </activity>

       <!-- this activity enables the search dialog to initiate searches
            in the SearchableActivity -->
       <activity android:name=".OtherActivity" ... >
           <!-- enable the search dialog to send searches to SearchableActivity -->
           <meta-data android:name="android.app.default_searchable"
                      android:value=".SearchableActivity" />
       </activity>
       ...
   </application>
   ```

2. 调用`onSearchRequested()`方法来激活Search Dialog。

3. 若有需要在激活Search Dialog时把一些数据发送到Searchable Activity：

   ``` java
   @Override
   public boolean onSearchRequested() {
        Bundle appData = new Bundle();
        appData.putBoolean(SearchableActivity.JARGON, true);
        startSearch(null, false, appData, false);
        return true;
    }
   ```

   ``` java
   Bundle appData = getIntent().getBundleExtra(SearchManager.APP_DATA);
   if (appData != null) {
       boolean jargon = appData.getBoolean(SearchableActivity.JARGON);
   }
   ```

###  Search Dialog对Activity生命周期的影响

Search Dialog不会影响Activity的正常生命周期，但会让Activity失去焦点。当用户按下返回键时，Activity会重新获得焦点。

可以利用`setOnDismissListener()`方法和`setOnCancelListener()`方法来监听Search Dialog消失以及被取消的事件。当Search Dialog每次消失的时候，`OnDismissListener`都会获得回调，而当用户主动退出Search Dialog才会调用`OnCancelListener`的方法，因此可以只注册`OnDismissListener`。

最好设置Searchable Activity的启动模式为`singleTop` ，这样可以避免实例化多个Searchable Activity的实例。

``` java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.search);
    handleIntent(getIntent());
}

@Override
protected void onNewIntent(Intent intent) {
    setIntent(intent);
    handleIntent(intent);
}

private void handleIntent(Intent intent) {
    if (Intent.ACTION_SEARCH.equals(intent.getAction())) {
      String query = intent.getStringExtra(SearchManager.QUERY);
      doMySearch(query);
    }
}
```

## Search Widget

* 如果搜索作为一个menu item在候选菜单当中，则不会显示`SearchView` 。

1. 通过调用`SearchManager`的`getSearchableInfo()`方法来获得对`SearchableInfo`的引用。
2. 将`SearchableInfo`的实例传入到`setSearchableInfo()`方法中。

``` java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    // Inflate the options menu from XML
    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.options_menu, menu);

    // Get the SearchView and set the searchable configuration
    SearchManager searchManager = (SearchManager) getSystemService(Context.SEARCH_SERVICE);
    SearchView searchView = (SearchView) menu.findItem(R.id.menu_search).getActionView();
    // Assumes current activity is the searchable activity
    searchView.setSearchableInfo(searchManager.getSearchableInfo(getComponentName()));
    searchView.setIconifiedByDefault(false); // Do not iconify the widget; expand it by default

    return true;
}
```

## 添加语音搜索

``` xml
<searchable xmlns:android="http://schemas.android.com/apk/res/android"
    android:label="@string/search_label"
    android:hint="@string/search_hint"
    android:voiceSearchMode="showVoiceSearchButton|launchRecognizer" >
</searchable>
```

## 添加最近搜索建议

### 创建`SearchRecentSuggestionsProvider`

创建`SearchRecentSuggestionsProvider`的子类并且需要实现该构造方法。

`setupSuggestions(AUTHORITY, MODE)`方法的第一个参数是搜索的权限，通常使用唯一的字符串；第二个参数是数据库模式，必须包括`DATABASE_MODE_QUERIES`，还可以添加`DATABASE_MODE_2LINES`。

``` java
public class MySuggestionProvider extends SearchRecentSuggestionsProvider {
    public final static String AUTHORITY = "com.example.MySuggestionProvider";
    public final static int MODE = DATABASE_MODE_QUERIES;

    public MySuggestionProvider() {
        setupSuggestions(AUTHORITY, MODE);
    }
}
```

在AndroidManifest文件中声明：

``` xml
<application>
    <provider android:name=".MySuggestionProvider"
              android:authorities="com.example.MySuggestionProvider" />
    ...
</application>
```

### 修改searchable configuration

``` xml
<searchable xmlns:android="http://schemas.android.com/apk/res/android"
    android:label="@string/app_label"
    android:hint="@string/search_hint"
    android:searchSuggestAuthority="com.example.MySuggestionProvider"
    android:searchSuggestSelection=" ?" >
</searchable>
```

### 保存查询

在Searchable Activity中接收到`Intent.ACTION_SEARCH` intent 时，可以实例化`SearchRecentSuggestions`并且调用`saveRecentQuery()`方法。其第一个参数是搜索的字符串，第二个参数作为搜索的第二行（当使用`DATABASE_MODE_2LINES`标志时）。

``` java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);

    Intent intent  = getIntent();

    if (Intent.ACTION_SEARCH.equals(intent.getAction())) {
        String query = intent.getStringExtra(SearchManager.QUERY);
        SearchRecentSuggestions suggestions = new SearchRecentSuggestions(this,
                MySuggestionProvider.AUTHORITY, MySuggestionProvider.MODE);
        suggestions.saveRecentQuery(query, null);
    }
}
```

### 删除历史搜索

``` java
SearchRecentSuggestions suggestions = new SearchRecentSuggestions(this,
        HelloSuggestionProvider.AUTHORITY, HelloSuggestionProvider.MODE);
suggestions.clearHistory();
```



