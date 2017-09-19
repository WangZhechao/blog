#javascript规范
---

##命名规则
---
+ 项目命名尽量以小写方式，以中划线分隔。例如：`my-project`。

+ 目录名称以小写方式命名，注意单复数。例如：`until`、`images`、`data-models`。

+ 文件名称以小写方式命名，采用中划线分隔。例如：`model.js`、`no-permission-error.js`。

+ 标准变量采用驼峰标识，原则上采用名词及名词短语。如：`thisIsMyName`。

+ 常量以大写字母标识，多个单词用下划线分割。如：`MAX_COUNT`。

+ 缩写单词尽量用大写标识。如：`myID`、`reportURL`。

+ 函数名称同样用驼峰标识，原则上函数采用动词及动词短语，以描述该函数的行为。如：

        if (condition) {
            doSomething();
        }

+ 构造函数第一个字母大写，如：

        function Person(name) {
            this.name = name
        }

##注释规则
---

+ 单行注释
双斜线后，必须跟注释内容保留一个空格，可独占一行, 前边不许有空行, 缩进与下一行代码保持一致，可位于一个代码行的末尾，距离分号一个Tab（4空格）。例如：

        // 距离双斜线一个空格
        if (condition) {
        
            // if you made it here, then all security checks passed
            allowed();
        }
        
        var zhangsan = "zhangsan";    // 双斜线距离分号四个空格，双斜线后始终保留一个空格


+ 多行注释
最少三行。可标注难以理解的代码。

        /*
         * 注释内容与星标前保留一个空格
         */

+ 文档注释
文档工具自动识别的格式，安装插件（`JSDoc`），自动生成。用来标注文档或函数。

        /**
         * here boy, look here , here is girl
         * @method lookGril
         * @param {Object} balabalabala
         * @return {Object} balabalabala
         */

##缩进规则
---
+ 一律使用tab键。请设置tab键的宽度为4个空格。
+ 连续缩进请与上一段代码块对齐 (if语句的条件）。
+ 单行语句过长，换行请在操作符后换行。

        if (typeof qqfind === "undefined" ||
            typeof qqfind.cdnrejected === "undefined" ||
            qqfind.cdnrejected !== true) {
            url = "http://pub.idqqimg.com/qqfind/js/location4.js";
        } else {
            url = "http://find.qq.com/js/location4.js";
        }

##分号、逗号、引号
---

+ 声明后分号一律不可省略。
+ 对象最后的属性结尾无逗号。
+ 字符串的最外层一律用单引号。

        var obj = {
            age: 18,
            name: 'xiaohua'
        };
        
        var string = '字符串外用"单引号"';

##变量、常量
---
+ 变量声明放到函数的头部，只使用一个var，涉及到赋值请换行，请尽量注释。

        function doSomethingWithItems(items) {
        
            var value = 10,    // 注释啊，注释啊，亲
                result = value + 10,    // 注释啊，注释啊
                i, len;    // 注释啊，注释啊，亲
        
            for (i=0, len=items.length; i<len; i++) {
                doSomething(items[i]);
            }
        }

+ 函数声明，常量声明请尽量前置。

##空格、空行
---

+ 括号前后要有空格，大括号起始不另还行，结尾新起一行。

        if (condition) {
            doSomething();
        }
+ `if else`、`else`、`switch`、`while`所有条件的括号前后都要有空格，break后要换行。

        switch (condition) {
            case "first":
                // code
                break;
        
            case "third":
                // code
                break;
        
            default:
            // code
        }

+ 大括号`{}`永远要有，即使内容只有一行。

+ 普通`for`循环, 分号后留有一个空格， 判断条件等内的操作符两边不留空格， 前置条件如果有多个，逗号后留一个空格

        for (i=0, len=values.length; i<len; i++) {
            process(values[i]);
        }

+ 函数表达式声明的函数，名称前后要有空格。

        var doSomething = function (item) {
            // do something
        }
        
+ 声明式函数，名称后一个空格。

        function doSomething(item) {
            // do something
        }

+ 变量声明后加空行。
+ 函数和函数之间加空行。
+ 单行或多行注释前加空行。
+ 复杂逻辑在逻辑块内增加空行。

##禁用规则
---

+ 尽量避免使用`==`或者`!=`，用`===`和`!==`替代。
+ 禁用`eval`。除非特殊性质。
+ 禁用`with`。除非特殊性质。
+ 永远不要直接使用`undefined`进行变量判断，请使用（`typeof` )。
+ `JavaScript`资源中不使用`IE`条件注释。

##其他
---
+ 在使用`JSON.parse`或`JSON.stringify`请注意捕获异常。
+ `this`指针的别名尽量命名为：`self`、`that`。
+ 使用`for...in`循环的时候，不要忘记使用`hasOwnProperty`，除非你刻意去这也做。
+ 尽量不修改内置对象的原型。
+ 要知道`truthy`和`falsy`规则。

        下方表达式返回false：
        null
        undefined
        ''
        0
        
        以下的都返回true：
        '0' 字符串0
        [] 空数组
        {} 空对象