## 开启`lwip`调试

`debug.h`文件中定义了调试宏：

```
#define LWIP_DEBUGF(debug, message) do { \
                               if ( \
                                   ((debug) & LWIP_DBG_ON) && \
                                   ((debug) & LWIP_DBG_TYPES_ON) && \
                                   ((s16_t)((debug) & LWIP_DBG_MASK_LEVEL) >= LWIP_DBG_MIN_LEVEL)) { \
                                 LWIP_PLATFORM_DIAG(message); \
                                 if ((debug) & LWIP_DBG_HALT) { \
                                   while(1); \
                                 } \
                               } \
                             } while(0)
```

其中有三个逻辑运算：

1. `(debug) & LWIP_DBG_ON)`
2. `(debug) & LWIP_DBG_TYPES_ON)`
3. `(debug) & LWIP_DBG_MASK_LEVEL) >= LWIP_DBG_MIN_LEVEL)`

一个打印日志的语句：

`LWIP_PLATFORM_DIAG(message); `

一个while死循环条件：

`((debug) & LWIP_DBG_HALT)`



`lwip`在打印日志的时候，语句如下：

`LWIP_DEBUGF(DHCP_DEBUG | LWIP_DBG_TRACE, .....)`在该语句中debug变量的值就是`DHCP_DEBUG | LWIP_DBG_TRACE`。



`cc.h`文件定义了`LWIP_PLATFORM_DIAG`宏，用来打印日志。注意该宏的实现：

```
#define LWIP_PLATFORM_DIAG(x)	do {printf x;} while(0)
```

默认状态下，x本身是带有小括号的，类似：`(......)`，所以打印的时候无需加括号。



