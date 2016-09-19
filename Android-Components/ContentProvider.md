## Content Provider基础知识
### Content Provider权限
Provider的应用可以指定其他应用访问Provider的数据所必需的权限。 这些权限可确保用户了解应用将尝试访问的数据。<br> 要获取访问Provider所需的权限，应用将通过其清单文件中的`<uses-permission>`元素来请求这些权限。<br><br>
如果Provider的应用未指定任何权限，则其他应用将无权访问Provider的数据。 但是，无论指定权限为何，Provider的应用中的组件始终具有完整的读取和写入访问权限。

### 内容 URI
内容 URI 是用于在Provider中标识数据的 URI。内容 URI 包括整个Provider的符号名称（其权限）和一个指向表的名称（路径）。 当您调用客户端方法来访问Provider中的表时，该表的内容 URI 将是其参数之一。<br>
ContentProvider 使用内容 URI 的路径部分来选择要访问的表。 Provider通常会为其公开的每个表显示一条路径。<br>
在检索到一组行后想要更新或删除其中某一行时通常会用到 ID 值，而ContentUris 包含一些可以将 ID 值轻松追加到 URI 后的方法，例如可以使用`withAppendedId()`将 ID 追加到用户字典内容 URI 后。`UriMatcher`类的方法`addURI()` 会将权限和路径映射到一个整型值，而方法`match()`会返回 URI 的整型值。<br><br>
内容 URI 模式使用通配符匹配内容 URI：<br>
*：匹配由任意长度的任何有效字符组成的字符串<br>
\#：匹配由任意长度的数字字符组成的字符串

### 实现`ContentProvider`类
抽象类`ContentProvider`定义了六个抽象方法，您必须将这些方法作为自己具体子类的一部分加以实现。<br>
所有这些方法（onCreate() 除外）都可由多个线程同时调用，因此它们必须是线程安全方法。<br>
* 实现`query()`方法
* 实现`insert()`方法
* 实现`delete()`方法
* 实现`update()`方法
* 实现`onCreate()`方法

### 从Content Provider检索数据
要从provider中检索数据，请按照以下基本步骤执行操作：<br>
* 1.请求对provider的读取访问权限。
* 2.定义将查询发送至provider的代码。

#### 请求读取访问权限
需要使用`<uses-permission>`元素和Content Provider定义的准确权限名称，在清单文件中指明您需要此权限。

#### 构建查询
要从Provider中获取列表，则需调用`ContentResolver.query()`。`query()`方法会调用Provider所定义的`ContentProvider.query()`方法。
``` java
mCursor = getContentResolver().query(
    UserDictionary.Words.CONTENT_URI,  // The content URI of the words table
    mProjection,                       // The columns to return for each row
    mSelectionClause                   // Either null, or the word the user entered
    mSelectionArgs,                    // Either empty, or the string the user entered
    mSortOrder);                       // The sort order for the returned rows
```

#### 显示查询结果
`ContentResolver.query()`客户端方法始终会返回符合以下条件的 Cursor：包含匹配查询选择条件的行指定的列。 Cursor 对象为其包含的行和列提供随机读取访问权限。 通过使用 Cursor 方法，您可以循环访问结果中的行、确定每个列的数据类型、从列中获取数据，并检查结果的其他属性。 某些 Cursor 实现会在Provider的数据发生更改时自动更新对象和(或)在 Cursor 更改时触发观察程序对象中的方法。
<br><br>
如果没有与选择条件匹配的行，则Provider会返回`Cursor.getCount()`为 0（空游标）的 Cursor 对象。
<br><br>
如果出现内部错误，查询结果将取决于具体的Provider。它可能会选择返回 null，或抛出 Exception。

#### 从查询结果中获取数据
Cursor 实现包含多个用于从对象中检索不同类型的数据的“获取”方法。Cursor的`getType()`方法会返回指示列的数据类型的值。
``` java
if (mCursor != null) {
   
    while (mCursor.moveToNext()) {

        newWord = mCursor.getString(index);

    }
```

### 插入数据
要将数据插入Provider，可调用`ContentResolver.insert()`方法。此方法会在Provider中插入新行并为该行返回内容 URI。<br>
`ContentValues`对象中的列不需要具有相同的数据类型，如果您不想指定值，则可以使用`ContentValues.putNull()`将列设置为 null。<br>
代码段不会添加 _ID 列，因为系统会自动维护此列。 Provider会向添加的每个行分配唯一的 _ID 值。 通常，Provider会将此值用作表的主键。
要从返回的 Uri 中获取 _ID 的值，可以调用`ContentUris.parseId()`。
``` java
Uri mNewUri;

ContentValues mNewValues = new ContentValues();

mNewValues.put(UserDictionary.Words.APP_ID, "example.user");
mNewValues.put(UserDictionary.Words.LOCALE, "en_US");
mNewValues.put(UserDictionary.Words.WORD, "insert");
mNewValues.put(UserDictionary.Words.FREQUENCY, "100");

mNewUri = getContentResolver().insert(
    UserDictionary.Word.CONTENT_URI,   // the user dictionary content URI
    mNewValues                          // the values to insert
);
```

### 更新数据
要更新行，需要使用`ContentResolver.update()`。您只需将值添加至您要更新的列的`ContentValues`对象。 如果您要清除列的内容，请将值设置为 null。
``` java
ContentValues mUpdateValues = new ContentValues();

String mSelectionClause = UserDictionary.Words.LOCALE +  "LIKE ?";
String[] mSelectionArgs = {"en_%"};

int mRowsUpdated = 0;

mUpdateValues.putNull(UserDictionary.Words.LOCALE);

mRowsUpdated = getContentResolver().update(
    UserDictionary.Words.CONTENT_URI,   // the user dictionary content URI
    mUpdateValues                       // the columns to update
    mSelectionClause                    // the column to select on
    mSelectionArgs                      // the value to compare to
);
```

### 删除数据
为要删除的行指定选择条件，`ContentResolver.delete()`方法会返回已删除的行数。
``` java
String mSelectionClause = UserDictionary.Words.APP_ID + " LIKE ?";
String[] mSelectionArgs = {"user"};

int mRowsDeleted = 0;

mRowsDeleted = getContentResolver().delete(
    UserDictionary.Words.CONTENT_URI,   // the user dictionary content URI
    mSelectionClause                    // the column to select on
    mSelectionArgs                      // the value to compare to
);
```

### Provider访问的替代形式
* 批量访问：<br>
批量访问Provider适用于插入大量行，或通过同一方法调用在多个表中插入行，或者通常用于跨进程界限将一组操作作为事务处理（原子操作）执行。
<br>
要在“批量模式”下访问Provider， 您可以创建`ContentProviderOperation`对象数组，然后使用`ContentResolver.applyBatch()` 将其分派给Content Provider。 您需将Content Provider的授权传递给此方法，而不是特定内容 URI。这样可使数组中的每个`ContentProviderOperation` 对象都能适用于其他表。 调用`ContentResolver.applyBatch()`会返回结果数组。
<br>

* 异步查询：<br>
应该在单独线程中执行查询。执行此操作的方式之一是使用`CursorLoader`对象。
<br>

* 通过 Intent 访问数据：<br>
Intent 可以提供对Content Provider的间接访问。即使您的应用不具备访问权限，您也可以通过以下方式允许用户访问Provider中的数据：从具有权限的应用中获取回结果 Intent，或者通过激活具有权限的应用，然后让用户在其中工作。<br>
    * 通过临时权限获取访问权限 <br>
    将 Intent 发送至具有权限的应用，然后接收回包含“URI”权限的结果 Intent。 这些是特定内容 URI 的权限，将持续至接收该权限的 Activity 结束。<br>
    具有永久权限的应用将通过在结果 Intent 中设置标志来授予临时权限：<br>
    读取权限：`FLAG_GRANT_READ_URI_PERMISSION` <br>
    写入权限：`FLAG_GRANT_WRITE_URI_PERMISSION`<br>
    * 使用其他应用 <br>
    允许用户修改您无权访问的数据的简单方法是激活具有权限的应用，让用户在其中执行工作。

### Provider数据类型
* 文本
* int
* long
* float
* double
* BLOB
* MIME

#### MIME类型引用
可以使用 MIME 类型信息查明应用是否可以处理Provider提供的数据，或根据 MIME 类型选择处理类型。 在使用包含复杂数据结构或文件的Provider时，通常需要 MIME 类型。 <br>
要获取与内容 URI 对应的 MIME 类型，请调用`ContentResolver.getType()`。<br><br>

* 表的 MIME 类型
对于指向一个或多个表数据行的内容 URI，`getType()`应该以 Android 供应商特有 MIME 格式返回 MIME 类型：<br>
类型部分：vnd<br>
子类型部分：<br>
如果 URI 模式用于单个行：android.cursor.item/<br>
如果 URI 模式用于多个行：android.cursor.dir/<br>
提供程序特有部分：vnd.\<name\>.\<type\><br>
您提供\<name\> 和\<type\>。\<name\> 值应具有全局唯一性，\<type\> 值应在对应的 URI 模式中具有唯一性。<br>

* 文件的 MIME 类型
如果您的提供程序提供文件，请实现`getStreamTypes()`。该方法会为您的提供程序可以为给定内容 URI 返回的文件返回一个 MIME 类型 String 数组。应该通过 MIME 类型过滤器参数过滤您提供的 MIME 类型，以便只返回客户端想处理的那些 MIME 类型。

### 防止恶意输入
可使用一个用于将`?`作为可替换参数的选择子句以及一个单独的选择参数数组。 执行此操作时，用户输入直接受查询约束，而不解释为 SQL 语句的一部分。 由于用户输入未作为 SQL 处理，因此无法注入恶意 SQL。
