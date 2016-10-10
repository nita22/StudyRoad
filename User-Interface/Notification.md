# 通知

## 创建通知

要创建通知，调用 `NotificationCompat.Builder.build()`，它将返回 `Notification` 对象。要发出通知，通过调用 `NotificationManager.notify()` 将 `Notification` 对象传递给系统。

`Notification` 对象必须包含：

* 小图标：通过`setSmallIcon()` 设置

* 标题：通过 `setContentTitle()` 设置

* 详细文本：通过`setContentText()` 设置

可通过调用`setContentIntent()`来添加`PendingIntent`到`Notification`中提供操作。

要设置通知的优先级，调用`NotificationCompat.Builder.setPriority()` 并传入一个 `NotificationCompat` 优先级常量。有五个优先级别，范围从 `PRIORITY_MIN` (-2) 到`PRIORITY_MAX` (2)；如果未设置，则优先级默认为 `PRIORITY_DEFAULT` (0)。

``` java
NotificationCompat.Builder mBuilder =
        new NotificationCompat.Builder(this)
        .setSmallIcon(R.drawable.notification_icon)
        .setContentTitle("My notification")
        .setContentText("Hello World!");
NotificationManager mNotificationManager =
    (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
// mId allows you to update the notification later on.
mNotificationManager.notify(mId, mBuilder.build());
```

### 通知的扩展布局

**扩展通知在 Android 4.1 之前的平台上不可用。**要使通知出现在展开视图中，先创建一个带有所需普通视图选项的 `NotificationCompat.Builder` 对象。接下来，调用以扩展布局对象作为其参数的`Builder.setStyle()`。

``` java
NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(this)
    .setSmallIcon(R.drawable.notification_icon)
    .setContentTitle("Event tracker")
    .setContentText("Events received")
NotificationCompat.InboxStyle inboxStyle =
        new NotificationCompat.InboxStyle();
String[] events = new String[6];
// Sets a title for the Inbox in expanded layout
inboxStyle.setBigContentTitle("Event tracker details:");
...
// Moves events into the expanded layout
for (int i=0; i < events.length; i++) {

    inboxStyle.addLine(events[i]);
}
// Moves the expanded layout object into the notification object.
mBuilder.setStyle(inBoxStyle);
...
// Issue the notification here.
```

## 更新通知

要将通知设置为能够更新，通过调用 `NotificationManager.notify()` 发出带有通知 ID 的通知。 要在发出之后更新此通知，更新或创建`NotificationCompat.Builder` 对象，从该对象构建 `Notification` 对象，并发出与之前所用 ID 相同的 `Notification`。

``` java
mNotificationManager =
        (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
// Sets an ID for the notification, so it can be updated
int notifyID = 1;
mNotifyBuilder = new NotificationCompat.Builder(this)
    .setContentTitle("New Message")
    .setContentText("You've received new messages.")
    .setSmallIcon(R.drawable.ic_notify_status)
numMessages = 0;
// Start of a loop that processes data and then notifies the user
...
    mNotifyBuilder.setContentText(currentText)
        .setNumber(++numMessages);
    // Because the ID remains unchanged, the existing notification is
    // updated.
    mNotificationManager.notify(
            notifyID,
            mNotifyBuilder.build());
...
```

## 删除通知

- 用户单独或通过使用“全部清除”清除了该通知（如果通知可以清除）。

- 用户点击通知，且您在创建通知时调用了 `setAutoCancel()`。

- 针对特定的通知 ID 调用了 `cancel()`。此方法会删除当前通知。

- 调用了 `cancelAll()` 方法，该方法将删除之前发出的所有通知。

## 启动 Activity 时保留导航

### 设置常规 Activity PendingIntent

常规 Activity是应用的正常工作流的一部分。

1. 在清单文件中定义应用的 `Activity` 层次结构

    Android 4.0.3 及更低版本：通过添加 `<meta-data>` 元素作为`<activity>`的子项来指定正在启动的 `Activity` 的父项。设置 `android:name="android.support.PARENT_ACTIVITY"`以及设置 `android:value="<parent_activity_name>"`。

    Android 4.1 及更高版本：将 `android:parentActivityName` 属性添加到正在启动的 `Activity` 的`<activity>`元素中。

   ``` xml
   <activity
       android:name=".MainActivity"
       android:label="@string/app_name" >
       <intent-filter>
           <action android:name="android.intent.action.MAIN" />
           <category android:name="android.intent.category.LAUNCHER" />
       </intent-filter>
   </activity>
   <activity
       android:name=".ResultActivity"
       android:parentActivityName=".MainActivity">
       <meta-data
           android:name="android.support.PARENT_ACTIVITY"
           android:value=".MainActivity"/>
   </activity>
   ```

2. 根据可启动 `Activity` 的 `Intent` 创建返回栈：

   1. 创建 `Intent` 以启动 `Activity`。
   2. 通过调用 `TaskStackBuilder.create()` 创建堆栈生成器。
   3. 通过调用 `addParentStack()` 将返回栈添加到堆栈生成器。 对于在清单文件中所定义层次结构内的每个 `Activity`，返回栈均包含可启动 `Activity`的 `Intent` 对象。此方法还会添加一些可在全新任务中启动堆栈的标志。
   4. 通过调用 `addNextIntent()`，添加可从通知中启动 `Activity` 的 `Intent`。 将在第一步中创建的 `Intent` 作为 `addNextIntent()` 的参数传递。
   5. 如需，请通过调用 `TaskStackBuilder.editIntentAt()` 向堆栈中的 `Intent` 对象添加参数。
   6. 通过调用 `getPendingIntent()` 获得此返回栈的 `PendingIntent`。 然后可以使用此 `PendingIntent` 作为 `setContentIntent()` 的参数。

   ``` java
   Intent resultIntent = new Intent(this, ResultActivity.class);
   TaskStackBuilder stackBuilder = TaskStackBuilder.create(this);
   // Adds the back stack
   stackBuilder.addParentStack(ResultActivity.class);
   // Adds the Intent to the top of the stack
   stackBuilder.addNextIntent(resultIntent);
   // Gets a PendingIntent containing the entire back stack
   PendingIntent resultPendingIntent =
           stackBuilder.getPendingIntent(0, PendingIntent.FLAG_UPDATE_CURRENT);
   ...
   NotificationCompat.Builder builder = new NotificationCompat.Builder(this);
   builder.setContentIntent(resultPendingIntent);
   NotificationManager mNotificationManager =
       (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
   mNotificationManager.notify(id, builder.build());
   ```

### 设置特殊 Activity PendingIntent

仅当从通知中启动时，用户才会看到特殊Activity。

1.  在清单文件中，将以下属性添加到 `Activity` 的`<activity>`元素

    ``` xml
    <activity
        android:name=".ResultActivity"
    ...
        android:launchMode="singleTask"
        android:taskAffinity=""
        android:excludeFromRecents="true">
    </activity>
    ```

    `android:name="activityclass"`：Activity 的完全限定类名。

    `android:taskAffinity=""`：与 `FLAG_ACTIVITY_NEW_TASK` 标志相结合，这可确保此 `Activity` 不会进入应用的默认任务。任何具有应用默认关联的现有任务均不受影响。

    `android:excludeFromRecents="true"`：将新任务从“最新动态”中排除。

2.  构建并发出通知：

    1. 创建可启动 `Activity`的 `Intent`。
    2. 通过使用 `FLAG_ACTIVITY_NEW_TASK` 和 `FLAG_ACTIVITY_CLEAR_TASK` 标志调用 `setFlags()`，将 `Activity` 设置为在新的空任务中启动。
    3. 为 `Intent` 设置所需的任何其他选项。
    4. 通过调用 `getActivity()` 从 `Intent` 中创建 `PendingIntent`。 然后，您可以使用此 `PendingIntent` 作为 `setContentIntent()` 的参数。

    ``` java
    NotificationCompat.Builder builder = new NotificationCompat.Builder(this);
    // Creates an Intent for the Activity
    Intent notifyIntent =
            new Intent(this, ResultActivity.class);
    // Sets the Activity to start in a new, empty task
    notifyIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK
                            | Intent.FLAG_ACTIVITY_CLEAR_TASK);
    // Creates the PendingIntent
    PendingIntent notifyPendingIntent =
            PendingIntent.getActivity(
            this,
            0,
            notifyIntent,
            PendingIntent.FLAG_UPDATE_CURRENT
    );

    // Puts the PendingIntent into the notification builder
    builder.setContentIntent(notifyPendingIntent);
    // Notifications are issued by sending them to the
    // NotificationManager system service.
    NotificationManager mNotificationManager =
        (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
    // Builds an anonymous Notification object from the builder, and
    // passes it to the NotificationManager
    mNotificationManager.notify(id, builder.build());
    ```

## 在通知中显示进度

### 显示时间固定的进度指示器

要显示限定形式的进度栏，通过调用 `setProgress(max, progress, false)` 将进度栏添加到通知，然后发出通知。 要删除进度栏，请调用`setProgress(0, 0, false)`。

``` java
mNotifyManager =
        (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
mBuilder = new NotificationCompat.Builder(this);
mBuilder.setContentTitle("Picture Download")
    .setContentText("Download in progress")
    .setSmallIcon(R.drawable.ic_notification);
// Start a lengthy operation in a background thread
new Thread(
    new Runnable() {
        @Override
        public void run() {
            int incr;
            // Do the "lengthy" operation 20 times
            for (incr = 0; incr <= 100; incr+=5) {
                    // Sets the progress indicator to a max value, the
                    // current completion percentage, and "determinate"
                    // state
                    mBuilder.setProgress(100, incr, false);
                    // Displays the progress bar for the first time.
                    mNotifyManager.notify(0, mBuilder.build());
                        // Sleeps the thread, simulating an operation
                        // that takes time
                        try {
                            // Sleep for 5 seconds
                            Thread.sleep(5*1000);
                        } catch (InterruptedException e) {
                            Log.d(TAG, "sleep failure");
                        }
            }
            // When the loop is finished, updates the notification
            mBuilder.setContentText("Download complete")
            // Removes the progress bar
                    .setProgress(0,0,false);
            mNotifyManager.notify(ID, mBuilder.build());
        }
    }
// Starts the thread by calling the run() method in its Runnable
).start();
```

### 显示持续活动的进度指示器

使用 `setProgress(0, 0, true)` 将其添加到通知，然后发出通知。除非修改通知，否则动画将一直运行。 操作完成后，调用 `setProgress(0, 0, false)`，然后更新通知以删除 Activity 指示器。 

## 浮动通知（Android5.0及以上版本）

可能触发浮动通知的条件：

- 用户的 Activity 处于全屏模式中
- 通知具有较高的优先级并使用铃声或振动

## 锁定屏幕通知（Android5.0及以上版本）

可以控制在安全锁定屏幕上显示的通知中可见的详细级别。 调用 `setVisibility()` 并指定以下值之一：

- `VISIBILITY_PUBLIC` 显示通知的完整内容。

- `VISIBILITY_SECRET` 不会在锁定屏幕上显示此通知的任何部分。

- `VISIBILITY_PRIVATE` 显示通知图标和内容标题等基本信息，但是隐藏通知的完整内容。

  设置 `VISIBILITY_PRIVATE` 后，还可以提供替换版本通知内容。先使用 `NotificationCompat.Builder` 创建替换通知。创建专用通知对象时，请通过`setPublicVersion()` 方法为其附加替换通知。

### 在锁定屏幕上控制媒体播放

  将 `Notification.MediaStyle`模板与 `addAction()` 方法结合使用，后者可将操作转换为可点击的图标。**只能在 Android 5.0 及更高版本的系统上运行。**

``` java
Notification notification = new Notification.Builder(context)
    // Show controls on lock screen even when user hides sensitive content.
    .setVisibility(Notification.VISIBILITY_PUBLIC)
    .setSmallIcon(R.drawable.ic_stat_player)
    // Add media control buttons that invoke intents in your media service
    .addAction(R.drawable.ic_prev, "Previous", prevPendingIntent) // #0
    .addAction(R.drawable.ic_pause, "Pause", pausePendingIntent)  // #1
    .addAction(R.drawable.ic_next, "Next", nextPendingIntent)     // #2
    // Apply the media style template
    .setStyle(new Notification.MediaStyle()
    .setShowActionsInCompactView(1 /* #1: pause button */)
    .setMediaSession(mMediaSession.getSessionToken())
    .setContentTitle("Wonderful music")
    .setContentText("My Awesome Band")
    .setLargeIcon(albumArtBitmap)
    .build();
```

## 自定义通知布局

1. 在单独的文件中为通知创建 XML 布局。
2. 实例化 `RemoteViews` 对象来加载 XML 布局文件。
3. 使用 `RemoteViews` 的一系列方法定义通知的图标和文本（如：`setTextViewText()`、`setImageViewResource`、`setOnClickPendingIntent`等）。
4. 通过调用 `setContent(RemoteViews views)`或者`setCustomContentView(RemoteViews contentView)`将此 `RemoteViews` 对象放入 `NotificationCompat.Builder`中。
   * 可以调用`setCustomBigContentView(RemoteViews contentView)`来自定义拓展的通知布局。
   * 可以调用`setCustomHeadsUpContentView(RemoteViews contentView)`来自定义浮动通知布局。


``` java
PendingIntent openActivityPendingIntent = PendingIntent.getActivity(context, 0, 
                                          new Intent(context,DemoActivity.class),                                                         PendingIntent.FLAG_UPDATE_CURRENT)；

RemoteViews remoteViews = new RemoteViews(getPackageName(), R.layout.layout_notification);
remoteViews.setTextViewText(R.id.msg, "abc");
remoteViews.setImageViewResource(R.id.icon, R.drawable.icon);
remoteViews.setOnClickPendingIntent(R.id.open_activity, openActivityPendingIntent);

Notification notification = new NotificationCompat.Builder(context)
  								.setCustomContentView(remoteViews)
  								.build();
```

## Notification分组显示（Android7.0引入）

可以在创建`Notification`实例时使用 `setGroup()` 来设置一个 `string`类型的组 ID 用来组织`Notification`。

必须通过`setGroupSummary()`方法设置一个`Notification`作为小组标题，不然`Notification`将不会以组的形式展现。

``` java
NotificationCompat.Builder builderOne = new NotificationCompat.Builder(context)
// Other properties
.setGroupSummary(true)
.setGroup(KEY_NOTIFICATION_GROUP);

NotificationCompat.Builder builderTwo = new NotificationCompat.Builder(context)
// Other properties
.setGroupSummary(true)
.setGroup(KEY_NOTIFICATION_GROUP);

NotificationCompat.Builder builderThree = new NotificationCompat.Builder(context)
// Other properties
.setGroupSummary(true)
.setGroup(KEY_NOTIFICATION_GROUP);
```

## 直接在通知栏中回复（Android7.0引入）

* 添加内联回复行为

  ``` java
  RemoteInput remoteInput = new RemoteInput.Builder(KEY_TEXT_REPLY)
      .setLabel(LABEL_REPLY)
      .build();
  ```


  在创建`RemoteInput`实例时需要传入一个认证身份的 KEY , 在应用中可以使用这个 KEY 来取回用户在 `Notification`中输入的文本。

* 创建回复动作

  ``` java
  NotificationCompat.Action replyAction =
          new NotificationCompat.Action.Builder(R.drawable.ic_reply,
                  LABEL_REPLY, replyIntent)
                  .addRemoteInput(remoteInput)
                  .build();

  NotificationCompat.Action archiveAction =
          new NotificationCompat.Action.Builder(R.drawable.ic_archive,
                  LABEL_ARCHIVE, archiveIntent)
                  .build();
  ```

* 添加动作到`Notification.Builder`

  ``` java
  NotificationCompat.Builder builder = new NotificationCompat.Builder(context)
          .setSmallIcon(R.drawable.ic_phonelink_ring_primary_24dp)
          .setContentTitle(title)
          .setContentText(message)
          .setLargeIcon(largeIcon)
          .addAction(replyAction);
          .addAction(archiveAction);
          .setAutoCancel(true);
  ```

* 从内联动作中获取数据

  ``` java
  Bundle bundle = RemoteInput.getResultsFromIntent(intent);
  bundle.getCharSequence(NotificationUtil.KEY_TEXT_REPLY);
  ```

  ​
  ​

  ​

  ​













