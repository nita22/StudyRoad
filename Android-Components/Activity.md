## Activity基本知识
### 创建Activity
要创建 Activity，您必须创建 Activity 的子类（或使用其现有子类）。您需要在子类中实现 Activity 在其生命周期的各种状态之间转变时（例如创建 Activity、停止 Activity、恢复 Activity 或销毁 Activity 时）系统调用的回调方法。 <br>
### 启动Activity
* 显式Intent
```Java
Intent intent = new Intent(this, SignInActivity.class);
startActivity(intent);
```
* 隐式Intent
<br>
```java
Intent intent = new Intent(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_EMAIL, recipientArray);
startActivity(intent);
