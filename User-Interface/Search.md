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

## 添加自定义搜索建议

### 修改searchable configuration

``` xml
<searchable xmlns:android="http://schemas.android.com/apk/res/android"
    android:label="@string/app_label"
    android:hint="@string/search_hint"
    android:searchSuggestAuthority="com.example.MyCustomSuggestionProvider">
</searchable>
```

### 创建ContentProvider

在用户每次输入一个字符，需要调用`ContentProvider`的`query()`方法去进行搜索，并显示自定义的搜索建议。需要重写`query()`方法，在`query()`方法中进行搜索并且返回一个指向指定内容的Cursor。

* 系统的默认行为是传递URI并后跟搜索请求字符串：

  ``` xml
  content://your.authority/optional.suggest.path/SUGGEST_URI_PATH_QUERY/strings
  ```

  仅当搜索配置文件中用`android:searchSuggestPath`属性设置了路径时，URI中才要包含`optional.suggest.path`部分。

* 在配置文件中的查询字符串

  ``` xml
  <searchable xmlns:android="http://schemas.android.com/apk/res/android"
      android:label="@string/app_label"
      android:hint="@string/search_hint"
      android:searchSuggestAuthority="com.example.MyCustomSuggestionProvider"
      android:searchSuggestIntentAction="android.intent.action.VIEW"
      android:searchSuggestSelection="word MATCH ?">
  </searchable>
  ```

  `android:searchSuggestIntentAction` ：用于定义用户每次选中建议项时所发送intent的action

  `android:searchSuggestSelection` ：定义查询语句

  若想从URI中获得搜索的字符串：

  ``` java
  String query = uri.getLastPathSegment().toLowerCase();
  ```

### 建立建议项数据表

用Cursor向系统返回建议项时，每一行数据的列格式都是系统规定的。

有两列是必需的：

* `ID` ：整数类型的行ID
* `SUGGEST_COLUMN_TEXT_1` :建议项的字符串

还有其它可选列：

* `SUGGEST_COLUMN_TEXT_2` ：如果Cursor包含该列，那么所有的建议项都提供两行模式。本列中的字符串将会显示为第二行

* `SUGGEST_COLUMN_ICON_1` ：建议项的图标

* `SUGGEST_COLUMN_ICON_2` ：建议项的第二行图标

* `SUGGEST_COLUMN_INTENT_ACTION` ：intent action字符串，会覆盖`android:searchSuggestIntentAction`中的属性

* `SUGGEST_COLUMN_INTENT_DATA` ：此值在格式化建议项时将作为intent的data部分来使用，会覆盖`android:searchSuggestIntentData`中的属性

* `SUGGEST_COLUMN_INTENT_DATA_ID` ：如果给定行中存在本列并且有值，那么“/”加上本值将会添加到intent的data之后

* `SUGGEST_COLUMN_INTENT_EXTRA_DATA` ：本列允许建议项附带额外的数据，包含于intent的`EXTRA_DATA_KEY`键内

* `SUGGEST_COLUMN_QUERY` ：如果给定行中存在本列并且有值，则为格式化建议项请求时需要用到的数据，包含于intent的QUERY键内


### 声明建议项所需的Intent

当用户在搜索对话框或widget下方的列表内选中某个建议项时，系统会向搜索activity发送一个自定义的Intent。

#### 声明intent的action

有两种方法：

* 利用搜索配置文件的`android:searchSuggestIntentAction`属性来定义所有建议项的action

  ```
  <searchable xmlns:android="http://schemas.android.com/apk/res/android"
      android:label="@string/app_label"
      android:hint="@string/search_hint"
      android:searchSuggestAuthority="com.example.MyCustomSuggestionProvider"
      android:searchSuggestIntentAction="android.Intent.action.VIEW" >
  </searchable>
  ```

* 利用`SUGGEST_COLUMN_INTENT_ACTION`列来定义单个建议项的action

  建议项表中添加`SUGGEST_COLUMN_INTENT_ACTION`列，并把每个建议项所用到的action放入其中
#### 声明intent的data

有两种方法：

*  在建议项表的`SUGGEST_COLUMN_INTENT_DATA`列中为每个建议项都定义一个data

*  把data的URI分解为两部分：所有建议项公共的部分和每个建议项唯一的部分。把这两部分分别放入搜索配置文件的`android:searchSuggestintentData`属性和建议项表的`SUGGEST_COLUMN_INTENT_DATA_ID`列中。

   用户选中建议项后，系统会把`android:searchSuggestIntentData`中指定的字符串加上斜杠 ("/")，再加入各自`SUGGEST_COLUMN_INTENT_DATA_ID`列中的值，组成一个完整的content URI。


#### 附加更多数据

可以添加另一列`SUGGEST_COLUMN_INTENT_EXTRA_DATA`来存放与建议项相关的附加数据。在此列中存放的数据将被置于intent附带Bundle的`EXTRA_DATA_KEY`部分中。

### 处理Intent

``` java
Intent intent = getIntent();
if (Intent.ACTION_SEARCH.equals(intent.getAction())) {
    // Handle the normal search query case
    String query = intent.getStringExtra(SearchManager.QUERY);
    doSearch(query);
} else if (Intent.ACTION_VIEW.equals(intent.getAction())) {
    // Handle a suggestions click (because the suggestions all use ACTION_VIEW)
    Uri data = intent.getData();
    showResult(data);
}
```

