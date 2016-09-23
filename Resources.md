## 提供资源

### 资源目录
![](https://github.com/nita22/StudyRoad/blob/master/Res/Pic/Resource%20directories.png?raw=true)

### 提供备用资源
#### 配置限定符
* 可以为单组资源指定多个限定符，并使用短划线分隔
* 限定符必须遵循配置限定符的顺序
* 不能嵌套备用资源目录
* 值不区分大小写
* 对于每种限定符类型，仅支持一个值
* 始终包含一组默认资源
<br>
![](https://github.com/nita22/StudyRoad/blob/master/Res/Pic/Configuration%20qualifier%20names.png?raw=true)

#### 创建别名资源
如果您想将某一资源用于多种设备配置（但是不想作为默认资源提供），则无需将同一资源放入多个备用资源目录中。 相反，您可以创建备用资源，充当保存在默认资源目录下的资源的别名。(xml/ 目录中的动画资源、菜单资源、原始资源以及其他未指定资源均不提供此功能。)

* Drawable
``` xml
<bitmap xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@drawable/icon_ca" />
```

* 布局
``` xml
<merge>
    <include layout="@layout/main_ltr"/>
</merge>
```

* 字符串和其他简单值
``` xml
<resources>
    <string name="hello">Hello</string>
    <string name="hi">@string/hello</string>
</resources>
```
