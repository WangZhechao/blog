# C语言中的可变参数

## 1. 简介

在C语言中可以使用`printf`进行格式化输出，函数声明如下：

```
int __cdecl printf(const char * _Format, ...);
```

其中第一个参数`format`代表需要格式化的字符串，第二个参数`...`代表任意个参数的集合，在C语言中叫做可变参数，使用它声明的函数可以在调用该函数的时候传入任意多的参数。

> 具体是如何实现的？

## 2. 实现原理

假如现在要实现`printf`函数，首先要定义该函数的实现。

```
int __cdecl custom_printf(const char * _format, ...)
{
	//具体代码逻辑
}
```

这里我们不关心该函数逻辑是如何实现的，只关心在函数内部是如何获取通过`...`传入的参数。

函数代码本质上就是一段机器指令，为了了解本质，可以使用vs2008调试界面的汇编功能进行分析：

```
int main()
{
	custom_printf("test", 1, 3, 4, 5);
	return 0;
}
```

跳转到反汇编进行查看：

![main](./iamges/main.png)

在执行函数调用语句的时候，会先将函数的参数进行压栈`(push)`，接着执行函数内部的逻辑代码`(call)`。

> 注意压栈顺序，参数从右向左依次入栈，这是由`__cdecl`决定的。

接着查看一下函数的汇编代码：

![custom_printf](./iamges/custom_printf.png)

目前`custom_printf`内部没有任何代码，生成的汇编代码和`main`函数的前面完全一致。当然其实在这里我们无需知道代码本身的含义，只需要知道如何拿到函数调用参数的值即可。

想要拿到函数参数的值是非常简单的，因为在执行`call`之前，参数已经被保存到栈，现在只需要到相应的位置拿即可，而`_format`参数的地址正是我们要找的参数位置的最后面。

> 这**可能**就是为什么可变参数之前要有固定参数的原因吧……

当前栈中的参数布局如下：

```
+-------------------+
|    5   address    |  高
+-------------------+
|    4   address    |
+-------------------+
|    3   address    |
+-------------------+
|    1   address    |  低
+-------------------+
|   test address    | <----- 已知条件
+-------------------+
```

> 因为x86机器的栈是由高到低增长，所以`test`在最下面。

如果想要获取`format`以外的其他参数，结果很简单，只要将指针按照参数类型增加。案例代码实现如下：

```
int __cdecl custom_printf(const char * _format, ...)
{
	int i = 0;
	int param[4] = {0};
	int *p = &_format;

	p += 1; //跳过字符串指针

	for(i = 0; i<4; i++) {
		param[i] = *(p + i);
	}
}
```

`param`数组内部就是传入的参数。

## 3. 验证

查看C语言内部提供的操作可变参数的函数，发现其实原理就是如此。

```
#define _INTSIZEOF(n)   ( (sizeof(n) + sizeof(int) - 1) & ~(sizeof(int) - 1) )

#define _crt_va_start(ap,v)  ( ap = (va_list)_ADDRESSOF(v) + _INTSIZEOF(v) )
#define _crt_va_arg(ap,t)    ( *(t *)((ap += _INTSIZEOF(t)) - _INTSIZEOF(t)) )
#define _crt_va_end(ap)      ( ap = (va_list)0 )
```

