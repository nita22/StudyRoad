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

## 访问资源
### 在代码中访问资源
[<package_name>.]R.<resource_type>.<resource_name>
\<package_name\> 是资源所在包的名称（如果引用的资源来自您自己的资源包，则不需要）。
\<resource_type\> 是资源类型的 R 子类。
\<resource_name\> 是不带扩展名的资源文件名，或 XML 元素中的`android:name`属性值（如果资源是简单值）。

### 在 XML 中访问资源
@[<package_name>:]<resource_type>/<resource_name>
\<package_name\>是资源所在包的名称（如果引用的资源来自同一包，则不需要）
\<resource_type\>是资源类型的 R 子类
\<resource_name\>是不带扩展名的资源文件名，或 XML 元素中的`android:name`属性值（如果资源是简单值）。
<br>
要引用系统资源，您需要加入包名称。

``` xml
<EditText xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:textColor="@android:color/secondary_text_dark"
    android:text="@string/hello" />
 ```

#### 引用样式属性
?[<package_name>:][<resource_type>/]<resource_name>
``` xml
<EditText id="text"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:textColor="?android:textColorSecondary"
    android:text="@string/hello_world" />
    ```
    
