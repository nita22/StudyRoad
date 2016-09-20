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

