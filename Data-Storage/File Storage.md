# 文件存储

## 使用内部存储

默认情况下，保存到内部存储的文件是应用的私有文件，其他应用（和用户）不能访问这些文件。 当用户卸载应用时，这些文件也会被移除。

### 写入到内部存储：

1. 使用文件名称和操作模式调用 `openFileOutput()`，该方法将返回一个 `FileOutputStream`。
2. 使用 `write()` 写入到文件。
3. 使用 `close()` 关闭流式传输。

``` java
String FILENAME = "hello_file";
String string = "hello world!";

FileOutputStream fos = openFileOutput(FILENAME, Context.MODE_PRIVATE);
fos.write(string.getBytes());
fos.close();
```

操作模式的取值可以为`MODE_PRIVATE`和`MODE_APPEND` 。其中`MODE_APPEND`的功能是判断是否有该文件，如果有在后面添加，而不是擦除其中的数据。

### 从内部存储读取文件：

1. 调用 `openFileInput()` 并向其传递要读取的文件名称，该方法将返回一个 `FileInputStream`。
2. 使用 `read()` 读取文件字节。
3. 然后使用 `close()` 关闭流式传输。

###  AndroidN及更高版本中共享私有文件

在AndroidN以及更高版本，如果应用需要与其他应用共享私有文件，则可以将`FileProvider`与 `FLAG_GRANT_READ_URI_PERMISSION `配合使用。

### 方法

`getCacheDir()`方法用于获取表示应用应该将临时缓存文件保存到的内部目录，即为 /data/data/\<application package>/cache目录。

`getFilesDir()`方法获取在其中存储内部文件的文件系统目录的绝对路径，即为 /data/data/\<application package>/files目录。

`getDir(String name, int mode)`在内部存储空间内创建（或打开现有的）目录。

`deleteFile(String name)`方法会删除保存在内部存储的文件。

`fileList()`方法会返回应用当前保存的一系列文件名组成的字符数组。

## 使用外部存储

保存到外部存储的文件是全局可读取文件。 所有应用都能读取和写入放置在外部存储上的文件，并且用户可以移除这些文件。

要读取或写入外部存储上的文件，应用必须获取 `READ_EXTERNAL_STORAGE` 或 `WRITE_EXTERNAL_STORAGE` 系统权限，其中 `WRITE_EXTERNAL_STORAGE` 权限隐含了读取权限要求。

**从 Android 4.4 开始，如果仅仅读取或写入应用的私有文件，则不需要声明这些权限。**

``` xml
<manifest ...>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    ...
</manifest>
```

在使用外部存储执行任何工作之前，应始终调用 `getExternalStorageState()` 以检查介质是否可用：

``` java
/* Checks if external storage is available for read and write */
public boolean isExternalStorageWritable() {
    String state = Environment.getExternalStorageState();
    if (Environment.MEDIA_MOUNTED.equals(state)) {
        return true;
    }
    return false;
}

/* Checks if external storage is available to at least read */
public boolean isExternalStorageReadable() {
    String state = Environment.getExternalStorageState();
    if (Environment.MEDIA_MOUNTED.equals(state) ||
        Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
        return true;
    }
    return false;
}
```

### 保存应用公开文件

可通过应用获取的新文件保存到设备上的“公共”位置，以便其他应用能够在其中访问这些文件，并且用户也能轻松地从该设备复制这些文件。

要获取表示相应的公共目录的 `File`，调用 `getExternalStoragePublicDirectory()`并向其传递需要的目录类型，例如 `DIRECTORY_MUSIC`、`DIRECTORY_PICTURES`、 `DIRECTORY_RINGTONES` 或其他类型。通过将您的文件保存到相应的媒体类型目录，系统的媒体扫描程序可以在系统中正确地归类您的文件。

``` java
public File getAlbumStorageDir(String albumName) {
    // Get the directory for the user's public pictures directory.
    File file = new File(Environment.getExternalStoragePublicDirectory(
            Environment.DIRECTORY_PICTURES), albumName);
    if (!file.mkdirs()) {
        Log.e(LOG_TAG, "Directory not created");
    }
    return file;
}
```

### 保存应用私有文件

应该通过调用 `getExternalFilesDir(String type)` 来使用外部存储上的私有存储目录，该方法需要传入一个参数指定子目录的类型，取值可以为`DIRECTORY_MUSIC`, `DIRECTORY_PODCASTS`, `DIRECTORY_RINGTONES`, `DIRECTORY_ALARMS`, `DIRECTORY_NOTIFICATIONS`,`DIRECTORY_PICTURES`, `DIRECTORY_MOVIES` ，也可以传入null来使用应用私有目录的根目录。

`getExternalFilesDir()`方法可以获取到的目录路径为 SDCard/Android/data/你的应用的包名/files/ 目录。

当用户卸载您的应用时，此目录及其内容将被删除。系统媒体扫描程序不会读取这些目录中的文件。

在 Android 4.3 和更低版本，`getExternalFilesDir()`方法将仅提供内部分区的访问权限，而无法读取或写入 SD 卡。从 Android 4.4 开始，可通过调用`getExternalFilesDirs()`来同时访问两个位置，该方法将会返回包含各个位置条目的 File 数组。 数组中的第一个条目被视为外部主存储。如果要在Android 4.3和更低版本中同时访问两个可能的位置，使用支持库中的静态方法`ContextCompat.getExternalFilesDirs()`。

### 保存缓存文件

调用 `getExternalCacheDir()`打开表示应该将缓存文件保存到的外部存储目录。`getExternalCacheDir()`方法 可获取到的目录路径为 SDCard/Android/data/你的应用包名/cache/目录。

可以通过调用 `ContextCompat.getExternalCacheDirs()` 来访问辅助外部存储上的缓存目录。

如果用户卸载您的应用，这些文件也会被自动删除。

### 使用作用域目录访问

在 Android 7.0 或更高版本中，如果需要访问外部存储上的特定目录，使用作用域目录访问。



