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



#### ss账号、翻墙

（1）"server" : "114.55.255.231", "server_port" : 35537, "method" : "rc4-md5", "password" : "Xingshulin2016",

（2） "server" : "139.198.189.242","server_port" : 35531,"method" : "rc4-md5","password" : "Xingshulin2016",

（3） "server" : "167.99.97.175","server_port" : 678,"method" : "AES-256-CFB","password" : "ntdtv.com",

（4） "server" : "64.32.5.185","server_port" : 123,"method" : "AES-256-CFB","password" : "dongtaiwang.com",

（5） "server" : "64.32.5.183","server_port" : 556,"method" : "AES-256-CFB","password" : "dongtaiwang.com",

（6） "server" : "38.141.44.99","server_port" : 8080,"method" : "AES-256-CFB","password" : "dongtaiwang.com",

（7） "server" : "103.114.161.158","server_port" : 6666,"method" : "AES-256-CFB","password" : "ntdtv.com",

（8） "server" : "103.114.161.16","server_port" : 7777,"method" : "AES-256-CFB","password" : "ntdtv.com",