## 存储访问框架
Android 4.4（API 19 级）引入了存储访问框架 (SAF)。SAF 让用户能够在其所有首选文档存储提供程序中方便地浏览并打开文档、图像以及其他文件。 

### 编写客户端应用
对于 Android 4.3 及更低版本，如果您想让应用从其他应用中检索文件，它必须调用`ACTION_PICK`或`ACTION_GET_CONTENT`等 Intent。然后，用户必须选择一个要从中选取文件的应用，并且所选应用必须提供一个用户界面，以便用户浏览和选取可用文件。<br>
对于 Android 4.4 及更高版本，您还可以选择使用`ACTION_OPEN_DOCUMENT`Intent，后者会显示一个由系统控制的选取器 UI，用户可以通过它浏览其他应用提供的所有文件。用户只需通过这一个 UI 便可从任何受支持的应用中选取文件。

#### 搜索文档
使用`ACTION_OPEN_DOCUMENT` Intent
``` java
private static final int READ_REQUEST_CODE = 42;

public void performFileSearch() {

    Intent intent = new Intent(Intent.ACTION_OPEN_DOCUMENT);

    intent.addCategory(Intent.CATEGORY_OPENABLE);

    intent.setType("image/*");

    startActivityForResult(intent, READ_REQUEST_CODE);
}
```

#### 处理结果
用户在选取器中选择文档后，系统就会调用`onActivityResult()`。指向所选文档的 URI 包含在 resultData 参数中。使用`getData()`提取 URI。获得 URI 后，即可使用它来检索用户想要的文档。
``` java
@Override
public void onActivityResult(int requestCode, int resultCode,
        Intent resultData) {

    if (requestCode == READ_REQUEST_CODE && resultCode == Activity.RESULT_OK) {
      
        Uri uri = null;
        if (resultData != null) {
            uri = resultData.getData();
            Log.i(TAG, "Uri: " + uri.toString());
            showImage(uri);
        }
    }
}
```

#### 检查文档元数据
获得文档的 URI 后，即可获得对其元数据的访问权限。
``` java
public void dumpImageMetaData(Uri uri) {

    Cursor cursor = getActivity().getContentResolver()
            .query(uri, null, null, null, null, null);

    try {
 
        if (cursor != null && cursor.moveToFirst()) {

            String displayName = cursor.getString(
                    cursor.getColumnIndex(OpenableColumns.DISPLAY_NAME));
            Log.i(TAG, "Display Name: " + displayName);

            int sizeIndex = cursor.getColumnIndex(OpenableColumns.SIZE);
           
            String size = null;
            if (!cursor.isNull(sizeIndex)) {
               
                size = cursor.getString(sizeIndex);
            } else {
                size = "Unknown";
            }
            Log.i(TAG, "Size: " + size);
        }
    } finally {
        cursor.close();
    }
}
```

#### 打开文档
获得文档的 URI 后，即可打开文档或对其执行任何其他您想要执行的操作。
* 打开图片
``` java
private Bitmap getBitmapFromUri(Uri uri) throws IOException {
    ParcelFileDescriptor parcelFileDescriptor =
            getContentResolver().openFileDescriptor(uri, "r");
    FileDescriptor fileDescriptor = parcelFileDescriptor.getFileDescriptor();
    Bitmap image = BitmapFactory.decodeFileDescriptor(fileDescriptor);
    parcelFileDescriptor.close();
    return image;
}
```

* 获取 InputStream
``` java
private String readTextFromUri(Uri uri) throws IOException {
    InputStream inputStream = getContentResolver().openInputStream(uri);
    BufferedReader reader = new BufferedReader(new InputStreamReader(
            inputStream));
    StringBuilder stringBuilder = new StringBuilder();
    String line;
    while ((line = reader.readLine()) != null) {
        stringBuilder.append(line);
    }
    fileInputStream.close();
    parcelFileDescriptor.close();
    return stringBuilder.toString();
}
```

#### 创建新文档
可以使用`ACTION_CREATE_DOCUMENT`Intent 在文档提供程序中创建新文档。要想创建文件，请为您的 Intent 提供一个 MIME 类型和文件名，然后通过唯一的请求代码启动它。创建新文档后，即可在`onActivityResult()`中获取其 URI，以便继续向其写入内容。
``` java
private static final int WRITE_REQUEST_CODE = 43;
...
private void createFile(String mimeType, String fileName) {
    Intent intent = new Intent(Intent.ACTION_CREATE_DOCUMENT);

    intent.addCategory(Intent.CATEGORY_OPENABLE);

    // Create a file with the requested MIME type.
    intent.setType(mimeType);
    intent.putExtra(Intent.EXTRA_TITLE, fileName);
    startActivityForResult(intent, WRITE_REQUEST_CODE);
}
```

#### 删除文档
如果您获得了文档的 URI，并且文档的`Document.COLUMN_FLAGS`包含`SUPPORTS_DELETE`，便可以删除该文档。
``` java
DocumentsContract.deleteDocument(getContentResolver(), uri);
```

#### 编辑文档
可以从`onActivityResult()`调用代码以执行编辑。
``` java
private static final int EDIT_REQUEST_CODE = 44;

 private void editDocument() {
    
    Intent intent = new Intent(Intent.ACTION_OPEN_DOCUMENT);

    intent.addCategory(Intent.CATEGORY_OPENABLE);

    intent.setType("text/plain");

    startActivityForResult(intent, EDIT_REQUEST_CODE);
}

private void alterDocument(Uri uri) {
    try {
        ParcelFileDescriptor pfd = getActivity().getContentResolver().
                openFileDescriptor(uri, "w");
        FileOutputStream fileOutputStream =
                new FileOutputStream(pfd.getFileDescriptor());
        fileOutputStream.write(("Overwritten by MyCloud at " +
                System.currentTimeMillis() + "\n").getBytes());

        fileOutputStream.close();
        pfd.close();
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

#### 保留权限
可以保留系统为您的应用授予的权限。应该始终调用`getContentResolver().takePersistableUriPermission()`以检查有无最新数据。
``` java
final int takeFlags = intent.getFlags()
            & (Intent.FLAG_GRANT_READ_URI_PERMISSION
            | Intent.FLAG_GRANT_WRITE_URI_PERMISSION);

getContentResolver().takePersistableUriPermission(uri, takeFlags);
```

### 编写自定义文档提供程序
#### 清单文件
* 属性`android:grantUriPermissions`设置为 "true"。此设置允许系统向其他应用授予对提供程序中内容的访问权限。
* `MANAGE_DOCUMENTS`权限。默认情况下，提供程序对所有人可用。 添加此权限将限定您的提供程序只能供系统使用。
* `android:enabled`属性设置为在资源文件中定义的一个布尔值。此属性的用途是，在运行 Android 4.3 或更低版本的设备上禁用提供程序。
``` xml
<manifest... >
    ...
    <uses-sdk
        android:minSdkVersion="19"
        android:targetSdkVersion="19" />
        ....
        <provider
            android:name="com.example.android.storageprovider.MyCloudProvider"
            android:authorities="com.example.android.storageprovider.documents"
            android:grantUriPermissions="true"
            android:exported="true"
            android:permission="android.permission.MANAGE_DOCUMENTS"
            android:enabled="@bool/atLeastKitKat">
            <intent-filter>
                <action android:name="android.content.action.DOCUMENTS_PROVIDER" />
            </intent-filter>
        </provider>
    </application>

</manifest>
```

#### 支持运行 Android 4.3 及更低版本的设备
让应用支持`ACTION_GET_CONTENT`以适应运行 Android 4.3 及更低版本的设备，应在清单文件中为运行 Android 4.4 或更高版本的设备禁用 `ACTION_GET_CONTENT Intent`过滤器。
``` xml
<activity-alias android:name="com.android.example.app.MyPicker"
        android:targetActivity="com.android.example.app.MyActivity"
        ...
        android:enabled="@bool/atMostJellyBeanMR2">
    <intent-filter>
        <action android:name="android.intent.action.GET_CONTENT" />
        <category android:name="android.intent.category.OPENABLE" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:mimeType="image/*" />
        <data android:mimeType="video/*" />
    </intent-filter>
</activity-alias>
```

#### 为`DocumentsProvider`创建子类
编写自定义文档提供程序的下一步是为抽象类`DocumentsProvider`创建子类。您至少需要实现以下方法：
* `queryRoots()`<br>
  实现的`queryRoots()`必须使用在`DocumentsContract.Root`中定义的列返回一个指向文档提供程序所有根目录的 Cursor。
  ``` java
  @Override
public Cursor queryRoots(String[] projection) throws FileNotFoundException {

    final MatrixCursor result =
            new MatrixCursor(resolveRootProjection(projection));

    if (!isUserLoggedIn()) {
        return result;
    }

    final MatrixCursor.RowBuilder row = result.newRow();
    row.add(Root.COLUMN_ROOT_ID, ROOT);
    row.add(Root.COLUMN_SUMMARY, getContext().getString(R.string.root_summary));

    row.add(Root.COLUMN_FLAGS, Root.FLAG_SUPPORTS_CREATE |
            Root.FLAG_SUPPORTS_RECENTS |
            Root.FLAG_SUPPORTS_SEARCH);

    row.add(Root.COLUMN_TITLE, getContext().getString(R.string.title));

    row.add(Root.COLUMN_DOCUMENT_ID, getDocIdForFile(mBaseDir));

    row.add(Root.COLUMN_MIME_TYPES, getChildMimeTypes(mBaseDir));
    row.add(Root.COLUMN_AVAILABLE_BYTES, mBaseDir.getFreeSpace());
    row.add(Root.COLUMN_ICON, R.drawable.ic_launcher);

    return result;
}
```

* `queryChildDocuments()`<br>
  实现的`queryChildDocuments()`必须使用在`DocumentsContract.Document`中定义的列返回一个指向指定目录中所有文件的 Cursor。
``` java
@Override
public Cursor queryChildDocuments(String parentDocumentId, String[] projection,
                              String sortOrder) throws FileNotFoundException {

    final MatrixCursor result = new
            MatrixCursor(resolveDocumentProjection(projection));
    final File parent = getFileForDocId(parentDocumentId);
    for (File file : parent.listFiles()) {
     
        includeFile(result, null, file);
    }
    return result;
}
```

* `queryDocument()`<br>
  实现的`queryDocument()`必须使用在`DocumentsContract.Document`中定义的列返回一个指向指定文件的 Cursor。
``` java
@Override
public Cursor queryDocument(String documentId, String[] projection) throws
        FileNotFoundException {

    final MatrixCursor result = new
            MatrixCursor(resolveDocumentProjection(projection));
    includeFile(result, documentId, null);
    return result;
}
```

* `openDocument()`<br>
  必须实现`openDocument()`以返回表示指定文件的`ParcelFileDescriptor`。其他应用可以使用返回的`ParcelFileDescriptor`来流式传输数据。
``` java
@Override
public ParcelFileDescriptor openDocument(final String documentId,
                                         final String mode,
                                         CancellationSignal signal) throws
        FileNotFoundException {
    Log.v(TAG, "openDocument, mode: " + mode);
  
    final File file = getFileForDocId(documentId);

    final boolean isWrite = (mode.indexOf('w') != -1);
    if(isWrite) {
       
        try {
            Handler handler = new Handler(getContext().getMainLooper());
            return ParcelFileDescriptor.open(file, accessMode, handler,
                        new ParcelFileDescriptor.OnCloseListener() {
                @Override
                public void onClose(IOException e) {

                    Log.i(TAG, "A file with id " +
                    documentId + " has been closed!
                    Time to " +
                    "update the server.");
                }

            });
        } catch (IOException e) {
            throw new FileNotFoundException("Failed to open document with id "
            + documentId + " and mode " + mode);
        }
    } else {
        return ParcelFileDescriptor.open(file, accessMode);
    }
}
```
