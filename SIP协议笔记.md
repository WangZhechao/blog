## 1. SIP协议介绍

Internet的许多应用都需要建立和管理一个会话（session），会话在这里的含义是在参与者之间的数据交换。考虑到现实情况的复杂性，这些参与者之间可能基于不同媒体（视频、音频、文本），不同的地理位置建立会话。而SIP（Session Initiation Protocol）协议主要用来创建、修改、终止这样的会话，它独立于其它通讯协议之外，并不依赖建立会话的具体类型。SIP协议不是一个垂直集成的通讯协议，它更多的作为其他协议的一部分，主要是管理会话，并不参与具体的服务内容。例如：SIP协议可以和RTP协议结合用来传输实时的数据并提供Qos反馈，可以和RSTP结合用于控制流媒体的传输，可以和MEGACO用来控制公共电话交换网（PSTN）的网关等等。



## 2. SIP协议结构

SIP协议是一个分层协议，层与层之间是松散的关系。SIP协议定义的要素是逻辑上的要素，并不是物理上的要素，并且不是每个要素都一定包含在每一层中。

SIP协议最底层是它的语法和编码，编码方式是采用扩展的Backus-Naur Form grammar (BNF范式)。

SIP协议第二层是传输层（transport layer）。它定义了一个客户端如何发送请求和应答请求，以及一个服务器如何接受请求和应答请求。所有的SIP要素（elements）都包括一个传输层。

SIP协议第三层是事务层（transaction layer）。事务是SIP的基本组成部分。一个事务的具体含义是指客户发送一个请求（通过传输层）到服务器以及服务器触发对客户的一系列应答。

在事务层之上是事务用户（transaction user）。每一个SIP实体（entities），除了无状态的代理，都是一个事务用户。当一个事务用户想发送一个请求的时候，它首先创建一个事务实例并连带着一些具体的参数（目标IP地址、端口号、发送请求的设备等等）一起发送出去。事务用户除了可以创建一个事务外也可以进行取消一个事务。



## 3. 消息格式

SIP协议是一个基于文本的协议，使用UTF-8字符集。

一个SIP消息可以使从客户端到服务端的请求（request），也可以是一个服务端到客户端的响应（response）。

> 一般消息（generic-message） = 起始行（start-line）
>
> ​		  *消息头（message-header）
>
> ​		  CRLF（回车换行）
>
> ​		  [消息体（message-body）]
>
> 起始行（start-line） = 请求行（Request-Line）/状态行（Status-Line）



### 3.1 请求

SIP请求根据请求行（Request-Line）进行区分，每个请求行包括：一个方法名、一个请求标识符（Request-URI）和一个协议版本号，每个元素之间用单个空格（single space）分隔。

请求行用CRLF结束。除了用作行结束，不允许CR或者LF出现在协议的任何地方，任何元素中不可以出现线型空白（linear whitespace）。



> Request-Line = Method SP Request-URI SP SIP-VERSION CRLF



**Method**：规范中定义了六种方法。

+ REGISTER 用于登记联系信息。
+ INVITE、ACK、CANCEL用于建立会话。
+ BYE用于结束会话。
+ OPTIONS用于查询服务器能力。

**Request-URI**：指定了请求用户或者服务地址。Request-URI使用一个SIP或者SIPS URI进行表述，不可以包含任何空格或者控制字符，并且禁止使用”<>“。

**SIP-VERSION**：指定请求应答的SIP协议版本。例如：SIP/2.0。



### 3.2 响应

响应和请求类似，区别在于起始行是请求行还是状态行。每个状态行包括：协议版本号、状态码和与之相关的文本描述信息，每个元素使用单个空格分隔。

除了结束标志外，其他任何地方不可以出现CR或者LF。



> Status-Line = SIP-Version SP Status-Code SP Reason-Phrase CRLF



**SIP-VERSION**：指定请求应答的SIP协议版本。例如：SIP/2.0。

**Status-Code**：状态码是由三位整形数构成，代表处理请求的结果。主要面向程序，方便计算。

+ 1XX：临时应答 —— 请求已经接收，正在处理。
+ 2XX：成功处理 —— 请求已经成功接收并正确处理。
+ 3XX：重定向 —— 请求需要进一步处理，用于转发到其他服务器。
+ 4XX：客户端错误 —— 请求包含错误语法或者该服务器无法完成处理。
+ 5XX：服务器错误 —— 服务器内部处理错误。
+ 6XX：全局错误 —— 请求不能被任何服务器处理。

**Reason-Phrase**：状态码的简短描述，主要面向用户，方便理解。该元素并不是必须的，但是推荐使用，可以使用合适的语言进行填充。



### 3.3 消息头

消息头由若干行组成，每种请求或者相应的消息头各有不同。消息头每行使用CRLF分隔，每行代表一个头部。具体的语法如下：



> header = "header-name" HCOLON header-value *(COMMA header-value)



具有相同头部的字段可以使用COMMA（逗号）进行组合，头部名称和头部内容使用HCOLON（冒号）分隔。例如：

```
Subject:     lunch
Subject   :  lunch
Subject     :lunch
Subject: lunch
```

上面的描述是合法等价的，在头部字段中允许冒号两侧有任意个空白，但是推荐最后一种写法。

```
Subject: I know you're there, pick up the phone and talk to me!
Subject: I know you're there, 
	pick up the phone 
 and talk to me!
```

上面的描述是合法等价的，在头部字段中允许多行描述，只需要在每个附加行前至少使用一个空格或者TAB打头。

消息头中各个头部字段的顺序并没有什么意义，但是推荐将路由相关的头部放到前面，便于提高处理解析速度。

```
Route: <sip:alice@atlanta.com>
Subject: Lunch
Route: <sip:bob@biloxi.com>
Route: <sip:carol@chicago.com>


Route: <sip:alice@atlanta.com>,<sip:bob@biloxi.com>
Route: <sip:carol@chicago.com>
Subject: Lunch


Subject: Lunch
Route: <sip:alice@atlanta.com>,<sip:bob@biloxi.com>,<sip:carol@chicago.com>
```

上面三组头部的描述是等价的。

```
Route: <sip:alice@atlanta.com>
Route: <sip:bob@biloxi.com>
Route: <sip:carol@chicago.com>

Route: <sip:bob@biloxi.com>
Route: <sip:alice@atlanta.com>
Route: <sip:carol@chicago.com>

Route: <sip:alice@atlanta.com>,<sip:carol@chicago.com>,<sip:bob@biloxi.com>
```

上面三组头部描述是合法的，但是并不等价。

在消息头中，头部内的字段名必须唯一，除非特别的字段名之外，所有的字段和值都是大小写不敏感的。引号指定的字符串是大小写敏感的。

```
Contact: <sip:alice@atlanta.com>;expires=3600
CONTACT: <sip:alice@atlanta.com>;ExPiReS=3600
```

```
Content-Disposition: session;handling=optional
content-disposition: Session;HANDLING=OPTIONAL
```

上面两组描述是等价的。

```
Warning: 370 devnull "Choose a bigger pipe"
Warning: 370 devnull "CHOOSE A BIGGER PIPE"
```

上面的描述是**不等价**的。

由于网络传输的限制（MTU），消息过大会导致通讯层无法传输，SIP协议定制了缩写格式的消息头，单个消息中，缩写格式和完全格式可能共存。



### 3.4 消息体

所有的请求和响应都可以包含一个消息体。消息体可以使用Content-Type头字段描述消息体的内容类型。如果消息正文使用某种编码，需要在Content-Encoding中指明，否则该字段将被忽略。消息体的长度放在Content-Length头部。
