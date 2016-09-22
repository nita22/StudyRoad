## AsyncTask
`AsyncTask`允许对用户界面执行异步操作。它会先阻塞工作线程中的操作，然后在 UI 线程中发布结果，而无需亲自处理线程和/或处理程序。<br>
* 可以使用泛型指定参数类型、进度值和任务最终值
* 方法`doInBackground()`会在工作线程上自动执行
* `onPreExecute()`、`onPostExecute()`和`onProgressUpdate()`均在 UI 线程中调用
* `doInBackground()`返回的值将发送到`onPostExecute()`
* 可以随时在`doInBackground()`中调用`publishProgress()`，以在 UI 线程中执行`onProgressUpdate()`
* 可以随时取消任何线程中的任务

### 使用AsyncTask
必须创建 AsyncTask 子类并实现`doInBackground()`回调方法，该方法将在后台线程池中运行。要更新 UI，必须实现`onPostExecute()`以传递`doInBackground()`返回的结果并在 UI 线程中运行，这样，您即可安全更新 UI。稍后，您可以通过从 UI 线程调用`execute()`来运行任务。
``` java
public void onClick(View v) {
    new DownloadImageTask().execute("http://example.com/image.png");
}

private class DownloadImageTask extends AsyncTask<String, Void, Bitmap> {
    /** The system calls this to perform work in a worker thread and
      * delivers it the parameters given to AsyncTask.execute() */
    protected Bitmap doInBackground(String... urls) {
        return loadImageFromNetwork(urls[0]);
    }

    /** The system calls this to perform work in the UI thread and delivers
      * the result from doInBackground() */
    protected void onPostExecute(Bitmap result) {
        mImageView.setImageBitmap(result);
    }
}
```
