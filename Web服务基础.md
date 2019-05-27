### Web服务
Web服务是一种服务导向架构的技术，通过标准的Web协议提供服务，目的是保证不同平台的应用服务可以互操作。工作在应用层，主要实现http, https协议。

#### 端口划分
>IANA(The Internet Assigned Numbers Authority，互联网数字分配机构)是负责协调一些使Internet正常运作的机构，由他们来划分端口。

- 0-1023：众所周知的端口，永久地分配给固定的应用使用，属于特权端口（只有管理员可以启用并让进程监听）；如：53/tcp

- 1024-41951：注册端口，但要求不是特别严格，分配给程序注册为某应用使用；
  如：mysql:3306/tcp, 11211/tcp；

- 41952+：客户端程序随机使用的端口，称为动态端口或私有端口；其范围定义在/proc/sys/net/ipv4/ip_local_port_range；
  		

**应用程序与应用程序之间通信是基于套接字进行的**
#### BSD Socket
>IPC（进程间通信）的一种实现，允许位于不同主机（也可以是同一主机）上的进程之间进行通信；

##### Socket API 
(封装了内核中的socket通信相关的系统调用)
- SOCK_STREAM: tcp套接字
- SOCK_DGRAM: UDP套接字
- SOCK_RAW：裸套按字
  			

**根据套按字所使用的地址格式，Socket Domain（套接字域）：**
- AF_INET：Address Family，IPv4

- AF_INET6：ipv6

- AF_UNIX：同一主机上的不同进程间基于socket套接字通信使用的一种地址；Unix_SOCK

  

![基于TCP客户/服务器程序的套接字函数](https://upload-images.jianshu.io/upload_images/16547068-c860ecf092a03702.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### TCP三次握手



![TCP三次握手](https://upload-images.jianshu.io/upload_images/16547068-78654889b8c6d7ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### TCP四次分手



![TCP四次分手](https://upload-images.jianshu.io/upload_images/16547068-a215cfef7b47bf4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### TCP协议的特性：

>建立连接：三次握手；
>将数据打包成段：校验和(CRC32)循环冗余校验码
>引用确认、重传及超时机制；
>排序：基于逻辑序号；
>流量控制：基于滑动窗口算法；
>拥塞控制：慢启动和拥塞避免算法；


​		

​			