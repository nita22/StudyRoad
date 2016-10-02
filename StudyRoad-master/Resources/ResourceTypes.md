## 资源类型
### String
#### String
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.string.string_name`
  * XML代码中引用：`@string/string_name`

``` xml
<resources>
    <string
        name="string_name"
        >text_string</string>
</resources>
```

#### String Array
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.array.string_array_name`
  
``` xml
<resources>
    <string-array
        name="string_array_name">
        <item
            >text_string</item>
    </string-array>
</resources>
```

#### Plurals
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.plurals.plural_name`
  
``` xml
<resources>
    <plurals
        name="plural_name">
        <item
            quantity=["zero" | "one" | "two" | "few" | "many" | "other"]
            >text_string</item>
    </plurals>
</resources>
```

### Style
* 文件位置：res/values/filename.xml
* 引用资源：
  * XML代码中引用：`@[package:]style/style_name`
  
``` xml
<resources>
    <style
        name="style_name"
        parent="@[package:]style/style_to_inherit">
        <item
            name="[package:]style_property_name"
            >style_value</item>
    </style>
</resources>
```

### Color
* 文件位置：res/values/colors.xml
* 引用资源：
  * Java代码中引用：`R.color.color_name`
  * XML代码中引用：`@[package:]color/color_name`
  
  ``` xml
  <resources>
    <color
        name="color_name"
        >hex_color</color>
</resources>
```

Java代码中调用资源：
``` java
Resources res = getResources();
int color = res.getColor(R.color.opaque_red);
```

### Color State List
* 文件位置：res/color/filename.xml
* 引用资源：
  * Java代码中引用：`R.color.filename`
  * XML代码中引用：`@[package:]color/filename`
  
``` xml
<selector xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:color="hex_color"
        android:state_pressed=["true" | "false"]
        android:state_focused=["true" | "false"]
        android:state_selected=["true" | "false"]
        android:state_checkable=["true" | "false"]
        android:state_checked=["true" | "false"]
        android:state_enabled=["true" | "false"]
        android:state_window_focused=["true" | "false"] />
</selector>
```

### Bool
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.bool.bool_name`
  * XML代码中引用：`@[package:]bool/bool_name`
  
``` xml
<resources>
    <bool
        name="bool_name"
        >[true | false]</bool>
</resources>
```

Java代码中调用资源：
  ``` java
  Resources res = getResources();
boolean screenIsSmall = res.getBoolean(R.bool.screen_small);
```

### Dimension
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.dimen.dimension_name`
  * XML代码中引用：`@[package:]dimen/dimension_name`
  
``` xml
  <resources>
    <dimen
        name="dimension_name"
        >dimension</dimen>
</resources>
```

Java代码中调用资源：
``` java
Resources res = getResources();
float fontSize = res.getDimension(R.dimen.font_size);
```

### ID
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.id.name`
  * XML代码中引用：`@[package:]id/name`
  
``` xml
<resources>
    <item
        type="id"
        name="id_name" />
</resources>
```

### Integer
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.integer.integer_name`
  * XML代码中引用：`@[package:]integer/integer_name`
  
``` xml
<resources>
    <integer
        name="integer_name"
        >integer</integer>
</resources>
```

Java代码中引用资源：
``` java
Resources res = getResources();
int maxSpeed = res.getInteger(R.integer.max_speed);
```

### Integer Array
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.array.integer_array_name`
  * XML代码中引用：`@[package:]array.integer_array_name`
  
``` xml
<resources>
    <integer-array
        name="integer_array_name">
        <item
            >integer</item>
    </integer-array>
</resources>
```

Java代码中引用资源：
``` java
Resources res = getResources();
int[] bits = res.getIntArray(R.array.bits);
```

### Typed Array
* 文件位置：res/values/filename.xml
* 引用资源：
  * Java代码中引用：`R.array.array_name`
  * XML代码中引用：`@[package:]array.array_name`
  
``` xml
<resources>
    <array
        name="integer_array_name">
        <item>resource</item>
    </array>
</resources>
```

Java代码中引用资源：
``` java
Resources res = getResources();
TypedArray icons = res.obtainTypedArray(R.array.icons);
Drawable drawable = icons.getDrawablea(0);

TypedArray colors = res.obtainTypedArray(R.array.colors);
int color = colors.getColor(0,0);
```
