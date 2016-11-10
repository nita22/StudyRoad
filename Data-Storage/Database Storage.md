## 数据库存储

### 使用数据库

1. 创建 `SQLiteOpenHelper` 的子类，在`SQLiteOpenHelper`的子类中至少需要实现三个方法：

   * 构造方法，调用父类SQLiteOpenHelper的构造函数。需要四个参数：上下文环境；数据库名称；一个可选的游标工厂（通常是null）；数据库版本。
   * `onCreate()`方法，需要一个SQLiteDatabase对象作为参数，根据需要对这个对象填充表和初始化数据。
   * `onUpgrade()`方法，需要三个参数：第一个参数为SQLiteDatabase对象，第二个参数为旧的版本号，第三个参数为新的版本号。

   ``` java
   public class DictionaryOpenHelper extends SQLiteOpenHelper {

       private static final int DATABASE_VERSION = 2;
       private static final String DICTIONARY_TABLE_NAME = "dictionary";
       private static final String DICTIONARY_TABLE_CREATE =
                   "CREATE TABLE " + DICTIONARY_TABLE_NAME + " (" +
                   KEY_WORD + " TEXT, " +
                   KEY_DEFINITION + " TEXT);";

       DictionaryOpenHelper(Context context) {
           super(context, DATABASE_NAME, null, DATABASE_VERSION);
       }

       @Override
       public void onCreate(SQLiteDatabase db) {
           db.execSQL(DICTIONARY_TABLE_CREATE);
       }
   }
   ```

2. 使用已定义的构造函数获取 `SQLiteOpenHelper` 的实例。 要从数据库执行写入和读取操作，可以调用 `getWritableDatabase()` 和`getReadableDatabase()`返回一个表示数据库的 `SQLiteDatabase` 对象，并通过该 `SQLiteDatabase` 对象操作SQLite数据库。

3. 对数据库表进行操作，包括添加、删除、修改和查询。有两种方法可以对数据库表进行操作：可以使用`execSQL()`方法执行SQL语句；也可以使用`insert()`、`delete()`、`update()`和`query()`方法。

   * `insert(String table, String nullColumnHack, ContentValues values)` ：

     ``` java
     ContentValues cv = new ContentValues();
     2 cv.put("title","you are beautiful");
     8 db.insert("diary",null,cv);
     ```

   * `update(String table, Contentvalues values, String whereClause, String whereArgs)` ：

     ``` java
     ContentValues cv = new ContentValues();
     cv.put("password","iHatePopMusic");
     String whereClause = "username=?";
     String[] whereArgs = {"Jack Johnson"};
     db.update("user",cv,whereClause,whereArgs);
     ```

   * `delete(String table, String whereClause, String whereArgs)` ：

     ``` java
     String whereClause = "username=?";
     String[] whereArgs = {"Jack Johnson"};
     db.delete("user",whereClause,whereArgs);
     ```

   * `query(String table, String[] columns, String selection, String[] selectionArgs, String groupBy, String having, String orderBy)` ：

     ``` java
     String[] params =  {12345,123456};
     Cursor cursor = db.query("user",columns,"ID=?",params,null,null,null);//查询并获得游标
     if(cursor.moveToFirst()){//判断游标是否为空
         for(int i=0;i<cursor.getCount();i++){
             cursor.move(i);//移动到指定记录
             String username = cursor.getString(cursor.getColumnIndex("username");
             String password = cursor.getString(cursor.getColumnIndex("password"));
         }
     }
     ```
### Cursor的常用方法

`move(int offset)`：以当前位置为参考,移动到指定行 

`moveToFirst()`：移动到第一行   

`moveToLast()`：移动到最后一行   

`moveToPosition(int position)`：移动到指定行   

`moveToPrevious()`：移动到前一行   

`moveToNext()`：移动到下一行   

`isFirst()`：是否指向第一条   

`isLast()`：是否指向最后一条   

`isBeforeFirst()`：是否指向第一条之前  

`isAfterLast()`：是否指向最后一条之后  

`isNull(int columnIndex)`：指定列是否为空

`isClosed()`：游标是否已关闭  

`getCount()`：总数据项数  

`getPosition()`：返回当前游标所指向的行数  

`getColumnIndex(String columnName)`：返回某列名对应的列索引值  

`getString(int columnIndex)`：返回当前行指定列的值 





