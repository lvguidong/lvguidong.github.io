---
layout: post
title: TCP三次握手和四次挥手-wireshark抓包分析
tags: 
- tcp
- 计算机网络
date: 2021-09-01 20:32 +0800
---



我们TCP/IP指的的TCP/IP协议族，现代网络通讯都是基于这个协议族简历起来的。
## UDP
UDP（用户数据包协议 User Datagram Protocol）,UDP是一种无连接协议，UDP客户端与服务器不必存在长期的关系，例如一个UDP客户端创建一个套接字并发送一个数据报给一个服务器，然后立即用同一个套接字发送另一个数据报给另外一个服务器。同样，一个UDP服务器可以用同一个UDP套接字从若干个不同的客户端一连串接受多个数据报。
UPD不是可靠的，不能保证最终到达他们的目的地，不保证各个数据的先后顺序跨网络保持不变，也不保证每个数据包可到达。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811143620130.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)


## TCP
TCP（传输控制协议 Transmission Control Protocol）

 1. TCP是一种面向连接的协议，提供客户与服务器的连接。
 2. TCP提供可靠性，使用TCP向另外一端发送数据时，要求对方范返回一个确认，若没有收到确认，TCP自动重传数据并等待更长时间
 3. TCP通过给所发送数据的每一个字节关联一个序列号进行排序，UDP没有
 4. TCP提供流量控制，UDP没有，TCP总是告诉对方它能接收多少字节的数据
 5. TCP的连接是双工的
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811144321240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)

## TCP与UDP的区别
TCP与UDP的**区别在于UDP不一定提供可靠的数据传输**，但UDP在很多方面非常有效，如UDP具有对系统资源要求较少，具有较好的实时性，网络开销小的优点，当某个程序的目标是尽快传输尽可能多的信息时，可用UDP。

## TCP连接的建立和终止
以下是一个基本TCP客户端与服务器的套接字接口函数
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020081115073934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)

从服务器端说起，服务器先调用socket函数（返回一个套接字），将该套接字与地址，端口绑定（bind）,调用accept阻塞，等待客户端来连接。
这是某个客户端在调用socket返回socket后调用connect()连接服务器，如果连接成功，两端的连接就建立了，双方可以发送数据。
最后关闭连接，一次交互就完成了。
下面是TCP连接过程中一些名词
|字符缩写|描述  |
|--|--|
|  SYN| 同步序列，标识此报文是一个连接请求或连接接受报文|
|  ACK|  确认位，对接收到报文的确认|
|  FIN| 终止位表示发送完成的数据发送。用来释放一个连接。|
|  RST|  复位连接。要是TCP连接中出现严重错误|
|  PSH| 推送位，尽可能快地将数据送往接收进程 |

## TCP三次握手建立连接详解
大致流程如下:

 1. 客户端向服务器发送一个SYN J;
 2. 服务器向客户端响应一个SYN K, 并对YSN J进行确认 ACK J+1；
 3. 客户端在向服务器发送一个确认ACK+1

这样就完成了三次握手，下面是三次握手发生的函数
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811152619699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)
从图中可以看出，雾气必须准备好接受外来的连接。是通过socket的band和listen函数来完成，称为被动打开。
不断通过调用connect进行主动打开。这引起的客户端向服务器发送SYN J(表示同步，它告诉服务器，客户将在连接中发送的数据的初始序列号)，这时connect进入阻塞状态。
服务器监听到连接请求，即收到了SYN J， 调用accept函数接受请求，并向客户端发送SYN K（他告诉客户端，服务器在连接中发送的数据的初始序列号），ACK+1,这是accept进入阻塞状态。
客户端收到服务器的SYN K,ACK+1之后，这时connect返回，并对SYN K进行确认；服务器收到ACK+1后，accept返回，至此三次握手完毕，完成连接
最后总结，客户端的connect在三次握手的第二次返回服务器的accept在三次握手的第三次返回。

### 使用wireshark来分析三次握手过程
[wireshark](https://wiki.wireshark.org/)是专业抓包软件，很多网络问题都可以通过wireshark来分析处理，下面抓一个tcp握手的包。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811155507864.png)
客户端发送一个TCP，标志位为SYN，序列号为0， 代表客户端请求建立连接
第一个包：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811155705949.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)
务器发回确认包, 标志位为 SYN,ACK. 将确认序号(Acknowledgement Number)设置为客户的I S N加1以.即0+1=1
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811155937854.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)
客户端再次发送确认包(ACK) SYN标志位为0,ACK标志位为1.并且把服务器发来ACK的序号字段+1,放在确定字段中发送给对方，第3个包
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811160208290.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)
## TCP四次挥手断开连接详解
建立一个连接需要三次握手，而终止一个连接要经过4次挥手。这是由于TCP的**半关闭(hald-close）**所致，那么TCP是一个全双工的(两个方向上都可同时传递)，因此两个方向必须单独关闭连接。
TCP断开连接的四次挥手过程如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811172145342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)

具体过程如下：

 1. 某个进程首先调用close,成这一端为主动关闭，这段的TCP发动一个FIN包，表示数据已经发动完毕。
 2. 另一端收到FIN包后，执行被动关闭，对这个FIN进行确认。他的接收也作为文件结束符传给接收端应用程序，因为FIN接收意味着应用进程在相应的连接上再也收不到额外数据。
 3. 一段时间之后，接收到文件结束符的应用进程，调用close关闭他的套接字，这导致它的TCP也发送一个FIN。
 4. 接收到这个FIN的源发送端TCP对它进行确认

这样每个方向上的都有一个FIN和ACK，需要四个包
注意，一般说的是要4个包，但是特殊情况下不用四个包，主要有两种情况。

 1. **开始要发动FIN包的这端，它的FIN包随数据一起发送**
 2. **被动关闭的那端，即上述的步骤2和步骤3发出的ACK和FIN可以合并成一个包发送**
 
 上述图中是客户端主动执行关闭，其实不管是客户端还是服务器都可以执行关闭，只是通常是客户端执行，但某些协议HTTP则是服务器执行主动关闭的
 
### 使用wireshark来分析四次挥手过程
wireshark四次挥手的包
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811170326987.png)

第一个包，发送一个FIN包
seq = 1467  ack = 267
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811170542572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)

第2个包
ack = 1468(1467+1)  seq=267
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811170812708.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)
第3个包：
ack=1468(1467+1) seq=267
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020081117095288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)
第4个包：
seq=1468(1467+1)  ack=268(267+1)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200811171118313.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODI2NDg=,size_16,color_FFFFFF,t_70)

 也可关注微信公众号，欢迎交流。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200813123208602.jpg#pic_center)