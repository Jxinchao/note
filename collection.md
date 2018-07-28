主流浏览器

IE						trident

Chrome					webkit/blink

Firefox					Gecko

Opera					Presto

Safari					webkit



### http 三次握手

 	三次握手协议指的是在发送数据的准备阶段，服务器端和客户端直接需要进行三次交互：

 - 第一次握手:

   客户端发送syn包（syn=j）到服务器，并进入SYN_SEND状态，等待服务器确认；

- 第二次握手：

  服务器收到syn包，必须确认客户的syn（ack=j+1），同时自己也发送一个SYN包（syn=k）,即SYN+ACK包，此时服务器进入SYN_RECV状态；

- 第三次握手：

  客户端收到服务器的SYN+ACK，向服务器发送确认包ACK（ACK=k+1）,此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。

  链接建立后，客户端和服务器就可以开始进行数据传输了。

