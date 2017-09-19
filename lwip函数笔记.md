# lwip函数笔记

tcp_new 创建一个新的pcb，没有内存返回NULL

tcp_bind 绑定pcb到本地ip和端口。本地ip，可以使用IP_ADDR_ANY，如果端口被占用，返回ERR_USE。

tcp_connect 建立pcb连接，发送SYN segment。这个函数会立即返回，不会等待连接完成。如果连接成功，通过回调函数（第四个参数）通知。如果远程主机拒绝或者没有应答会通过tcp_err返回。当内存不足的时候，SYN segment创建失败，本函数可能会返回ERR_MEM错误。

tcp_write 发送tcp数据。最后一个参数有两种标记，TCP_WRITE_FLAG_COPY代表将发送的数据创建新的拷贝，如果不使用则代表该内存会被lwip内部引用，直到远程主机回复acked前都不能改变。TCP_WRITE_FLAG_MORE 是否设置PSH标记。如果函数发送失败返回ERR_MEM代表当前发送数据的大小超过发送队列的上线，可以通过tcp_sndbuf获取发送队列剩余可用大小。


tcp_sent 设置发送成功回调。


tcp_recv 设置接收回调函数，当有新数据到来的时候，会调用该函数设置的回调。

回调函数原型：err_t (* recv)(void *arg, struct tcp_pcb *tpcb, struct pbuf *p, err_t err)

如果远程主机关闭连接，p的值将为NULL。如果没有错误，这个回调函数返回ERR_OK，在回调中必须释放pbuf，否则无需释放以便于lwip能够存储它。

tcp_recved 告知还可以接收数据的长度。


tcp_poll 设置空闲回调函数。当连接空闲的时候该函数设置的回调函数会反复调用，可以利用这个特点，用来检测长时间没有响应的连接或者等待tcp_write下次可用。


tcp_close 关闭连接。当没有内存可用的时候，该函数可能会返回ERR_MEM，当出现这样的情况需要等待一段时间后再次尝试，可以使用轮询poll或者acknowledgment后关闭。


tcp_abort 终止连接。发送RST segment，释放pcb，该函数永远不会失败。警告：当在tcp回调中调用此方式时，请确保总是返回ERR_ABRT，否则有可能导致内存泄露。

tcp_err 设置错误回调，回调中不能获取pcb，因为pcb可能已经释放。