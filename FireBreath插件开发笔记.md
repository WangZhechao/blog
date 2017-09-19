# 插件

```javascript
function pluginLoaded() {
    alert("Plugin loaded!");
}

<object id="plugin" type="application/x-plusvideoplayer" width="300" height="300">
    <param name="onload" value="pluginLoaded" />
</object>
```

插件加载完毕后触发事件`onload`。

## 1. 插件添加方法

对于插件C++代码中，需要在`FB::JSAPIAuto`的子类中实现该方法。

### 声明

```C++
// Method echo
FB::variant echo(const FB::variant& msg);
```

### 实现

```C++
FB::variant PlusVideoPlayerAPI::echo(const FB::variant& msg)
{
    return msg;
}
```

### 注册

```C++
registerMethod("echo", make_method(this, &PlusVideoPlayerAPI::echo));
```



对于浏览器端js代码中，可以直接调用该方法。

```javascript
document.getElementById('plugin0').echo("This plugin seems to be working!"));
```



## 2. 插件添加事件

对于插件C++代码中，需要在`FB::JSAPIAuto`的子类中声明该事件。

### 声明

使用宏`FB_JSAPI_EVENT`进行事件声明。

```c++
FB_JSAPI_EVENT(test, 0, ());
FB_JSAPI_EVENT(echo, 2, (const FB::variant&, const int));
```

宏有三个参数，第一个参数代表事件的名称，第二参数代表参数的个数，第三个参数代表参数的列表。

### 触发

在C++代码中触发事件，可以使用`fire_xxxxx`。

```c++
fire_test();
fire_echo("So far, you clicked this many times: ", n++);
```



对于浏览器端js代码中，需要监听对应的事件。因为浏览器的差异，所以监听事件的方法对于不同浏览器略有区别：

```javascript
function addEvent(obj, name, func)
{
    if (obj.attachEvent) {
        obj.attachEvent("on"+name, func);
    } else {
        obj.addEventListener(name, func, false); 
    }
}
```

监听事件

```javascript
addEvent(plugin(), 'test', function(){
    alert("Received a test event from the plugin.")
});

addEvent(plugin(), 'echo', function(txt,count){
    alert(txt+count);
});
```



## 3. 插件添加属性

插件属性分为两种，一种是只读属性，一种是读写属性。

### 注册

注册属性可以使用函数`registerProperty`进行注册。

```c++
// Read-write property
registerProperty("testString",
                 make_property(this,
                               &PlusVideoPlayerAPI::get_testString,
                               &PlusVideoPlayerAPI::set_testString));

// Read-only property
registerProperty("version",
                 make_property(this,
                               &PlusVideoPlayerAPI::get_version));
```

函数第一个参数代表属性名称，第二个参数是一个结构体，其中有两个重要的函数，分别对应参数的读和写。如果不传入写函数，该属性就位只读。

### 声明

```c++
// Read/Write property ${PROPERTY.ident}
std::string get_testString();
void set_testString(const std::string& val);

// Read-only property ${PROPERTY.ident}
std::string get_version();
```

### 实现

```c++
// Read/Write property testString
std::string PlusVideoPlayerAPI::get_testString()
{
    return m_testString;
}

void PlusVideoPlayerAPI::set_testString(const std::string& val)
{
    m_testString = val;
}

// Read-only property version
std::string PlusVideoPlayerAPI::get_version()
{
    return FBSTRING_PLUGIN_VERSION;
}
```



对于浏览器js代码，可以直接操作。

```
plugin().testString = 'abc';
```



## 4. js初始化参数传入

对于浏览器js代码，可以在添加object标签的内部，定义param标签，该标签的内容可以传入到插件中。

```javascript
<param name="init" value="4" />
<param name="color" value="16776960" />
```



对于插件来说可以通过函数`getParamVariant`获取参数的值。

```C++
FB::variant& init = getParamVariant("init");
FB::variant& color = getParamVariant("color");
```

