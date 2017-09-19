#Ext常见错误

##`1.  Uncaught TypeError: Cannot read property 'buffered' of undefined`

![Uncaught TypeError](./err_1.png)

该错误主要的原因是无法找到某个文件。点击进入发现如下代码:

    if (store.buffered && !store.remoteSort) {
        for (i = 0, len = columns.length; i < len; i++) {
            columns[i].sortable = false;
        }
    }

原因很明显，store加载失败！查找grid控件关联的store属性，发现store的代码还有没写（路径不对）,也有可能是因为store没有加载，文件存在，但是没有加载该文件的代码，一般在控制器。


##`2. Uncaught Error: [Ext.Loader] Failed loading 'app/model/permManage/groupModel.js', please verify that the file exists`

![Uncaught Error](./err_2.png)

该错误很明显的提示信息，找不到某个文件，请检查文件路径是否存在该文件，或者代码中的名称填写错误等等。常见错误的位置为控制器中的views、stores、models，或者某个控件的子控件。


##`3. Uncaught Error: [Ext.createByAlias] Cannot create an instance of unrecognized alias: widget.permManageTab`

![Uncaught Error](./err_3.png)

改错误信息显示主要是无法创建某个实例。这里面是无法找到widget.permManageTab控件，解决方法尝试查看是否名称填写错误，然后调试看前台是否加载该控件，结果没有加载。然后发现该控件对应的控制器也没有加载，所以根本原因是没有加载控制器，导致与之关联的model、view和store都没有加载。

##`4. Uncaught SyntaxError: Unexpected identifier`

![Uncaught SyntaxError](./err_4.png)

该错误大多是语法问题，一般是多个逗号导致的。

##`5. [E] Ext.util.Event.addListener(): The specified callback function is undefined`

![Ext.util.Event.addListener](./err_5.png)

该错误主要是Ext内部加入监听器的时候发现函数未定义。主要是参数传递错误，如果使用的是MVC控件，大多数应该在控制器初始化函数中某个函数未定义导致的。

##6. IE6-8下 SCRIPT1028: 缺少标识符、字符串或数字

![SCRIPT1028](./err_6.png)

该错误常见于ie下，在现代浏览器下没有任何错误，在ie6/7下会出现。主要原因应该是末尾多增加一个逗号之类的错误。