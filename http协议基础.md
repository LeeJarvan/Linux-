### http：hyper text transfer protocol
>应用层协议， 监听于80/tcp,  文本协议

HTTP是一种无状态(stateless) 协议，HTTP协议本身不会对发送过的请求和相应的通信状态进行持久化处理。这样做的目的是为了保持HTTP协议的简单性，从而能够快速处理大量的事务，提高效率。

然而，在许多应用场景中，我们需要保持用户登录的状态或记录用户购物车中的商品。由于HTTP是无状态协议，所以必须引入一些技术来记录管理状态，例如Cookie

Cookie指某些网站为了辨别用户身份、进行 session 跟踪而储存在用户本地终端上的数据（通常经过加密）。Cookie技术是客户端的解决方案，Cookie就是由服务器发给客户端的特殊信息，而这些信息以文本文件的方式存放在客户端，然后客户端每次向服务器发送请求的时候都会带上这些特殊的信息。

**html：hyper text mark language, 编程语言，超文本标记语言**

```		
<html>
	<head>
		<title>TITLE</title>
	</head>
	<body>
		<h1></h1>
			<p> blabla... <a href="http://www.magedu.com/download.html"> bla... </a> </p>
		<h2> </h2>
	</body>
</html>
```

>如果要学习html，还需学习：
>css: Cascading Style Sheet，层叠样式表，是一种用来表现HTML或XML等文件样式的计算机语言
>js：JavaScript，客户端脚本开发语言


#### 协议版本
>http/0.9：原型版本，功能简陋
>method：GET（服务度传数据给客户端）
>
>http/1.0:引入 cache, MIME, method（请求方法）机制			
>MIME：Multipurpose Internet Mail Extesion （多用途互联网邮件扩展类型），是设定某种扩展名的文件用一种应用程序来打开的方式类型，当该扩展名文件被访问的时候，浏览器会自动使用指定应用程序来打开。
>method：GET（服务度传数据给客户端）， POST， HEAD，PUT， DELETE，TRACE， OPTIONS
>
>http/1.1：增强了缓存功能；
>SPDY协议是Google提出的基于传输控制协议(TCP)的应用层协议，通过压缩、多路复用和优先级来缩短加载时间。该协议是一种更加快速的内容传输协议。
>
>http/2.0：
>HTTP2.0大幅度的提高了web性能，在HTTP1.1完全语意兼容的基础上，进一步减少了网络的延迟。实现低延迟高吞吐量。对于前端开发者而言，减少了优化工作。 



#### 工作模式
**一次http事务：请求<-->响应（过程）**
- http请求报文：http request

**request报文**

```
<method> <request-URL> <version>
<HEADERS>

<entity-body>
```

- http响应报文: http response

**response报文**

```
<version> <status> <reason-phrase>
<HEADERS>

<entity-body>
```


**method**：请求方法，标明客户端希望服务器对资源执行的动作

|  方法   |                    作用                    |
| :-----: | :----------------------------------------: |
|   GET   |            从服务器获取一个资源            |
|  HEAD   |        只从服务器获取文档的响应首部        |
|  POST   |          向服务器发送要处理的数据          |
|   PUT   |       将请求的主体部分存储在服务器上       |
| DELETE  |         请求删除服务器上指定的文档         |
|  TRACE  |   追踪请求到达服务器中间经过的代理服务器   |
| OPTIONS | 请求服务器返回对指定资源支持使用的请求方法 |

**version**：HTTP/<major>.<minor>
**status**：三位数字，如200，301, 302, 404, 502; 标记请求处理过程中发生的情况

| status(状态码) |           作用           |
| :------------: | :----------------------: |
|  1xx：100-101  |         信息提示         |
|  2xx：200-206  |           成功           |
|  3xx：300-305  |          重定向          |
|  4xx：400-415  |  错误类信息，客户端错误  |
|  5xx：500-505  | 错误类信息，服务器端错误 |


| 常用状态码 |       原因短语        |                             作用                             |
| :--------: | :-------------------: | :----------------------------------------------------------: |
|    200     |          OK           |    成功，请求的所有数据通过响应报文的entity-body部分发送     |
|    301     |   Moved Permanently   | 请求的URL指向的资源已经被删除；但在响应报文中通过首部Location指明了资源现在所处的新位置 |
|    302     |         Found         | 与301相似，但在响应报文中通过Location指明资源现在所处临时新位置 |
|    304     |     Not Modified      | 客户端发出了条件式请求，但服务器上的资源未曾发生改变，则通过响应此响应状态码通知客户端 |
|    401     |     Unauthorized      |              需要输入账号和密码认证方能访问资源              |
|    403     |       Forbidden       |                          请求被禁止                          |
|    404     |       Not Found       |                服务器无法找到客户端请求的资源                |
|    500     | Internal Server Error |                        服务器内部错误                        |
|    502     |      Bad Gateway      |            代理服务器从后端服务器收到了一条伪响应            |

**reason-phrase**：状态码所标记的状态的简要描述
**headers**：每个请求或响应报文可包含任意个首部；每个首部都有首部名称，后面跟一个冒号，而后跟上一个可选空格，接着是一个值；

格式：`Name: Value`

```
Cache-Control:public, max-age=600
Connection:keep-alive
Content-Type:image/png
Date:Tue, 28 Apr 2015 01:43:54 GMT
ETag:"5af34e-ce6-504ea605b2e40"
Last-Modified:Wed, 08 Oct 2014 14:46:09 GMT


Accept:image/webp,*/*;q=0.8
Accept-Encoding:gzip, deflate, sdch
Accept-Language:zh-CN,zh;q=0.8
Cache-Control:max-age=0
Connection:keep-alive
Host:access.redhat.com
If-Modified-Since:Wed, 08 Oct 2014 14:46:09 GMT
If-None-Match:"5af34e-ce6-504ea605b2e40"
Referer:https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html-single/Installation_Guide/index.html
User-Agent:Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.101 Safari/537.36
```

**首部的分类：**
>通用首部：
>Date: 报文的创建时间
>Connection：连接状态，如keep-alive, close
>Via：显示报文经过的中间节点
>Cache-Control：控制缓存
>Pragma：HTTP/1.1之前版本的历史遗留字段

>请求首部：
>Accept：通过服务器自己可接受的媒体类型
>Accept-Charset：客户端可接受的字符集
>Accept-Encoding：接受编码格式，如gzip,deflate,scdh
>Accept-Language：接受的语言
>
>Client-IP: 请求的客户端IP
>Host: 请求的服务器名称和端口号
>Referer：包含当前正在请求的资源的上一级资源
>User-Agent：客户端代理
>
>条件式请求首部：
>Expect：允许客户端列出某请求所要求的服务器行为
>If-Modified-Since：自从指定的时间之后，请求的资源是否发生过修改
>If-Unmodified-Since：与上述相反
>If-None-Match：本地缓存中存储的文档的ETag标签是否与服务器文档的Etag不匹配
>If-Match：与上述相反
>
>安全请求首部：
>Authorization：向服务器发送认证信息，如账号和密码
>Cookie： 客户端向服务器发送cookie
>Cookie2：用于说明请求端支持的cookie版本
>
>代理请求首部：
>Proxy-Authorization: 向代理服务器认证

>响应首部：
>信息性首部：
>Age：响应持续时长
>Server：服务器程序软件名称和版本
>
>协商首部：某资源有多种表示方法时使用
>Accept-Ranges：服务器可接受的请求范围类型
>Vary：服务器查看的其它首部列表
>
>安全响应首部：
>Set-Cookie：向客户端设置cookie
>Set-Cookie2：与上述相似
>WWW-Authenticate：来自服务器的对客户端的质询认证表单

>实体首部：
>Allow： 列出对此实体可使用的请求方法
>Location：告诉客户端真正的实体位于何处
>
>Content-Encoding：对主体执行的编码
>Content-Language：理解主体时最适合的语言
>Content-Length：主体的长度
>Content-Location：实体真正所处位置
>Content-Type：主体的对象类型
>
>缓存相关：
>ETag：实体的扩展标签
>Expires：实体的过期时间
>Last-Modified：最后一次修改的时间

**entity-body**：请求时附加的数据或响应时附加的数据

##### web资源：web resource
- 静态资源（无须服务端做出额外处理）： .jpg, .png, .gif, .html, txt, .js, .css, .mp3, .avi					
- 动态资源（服务端需要通过执行程序做出处理，发送给客户端的是程序的运行结果）： .php, .jsp
  			

注意：一个页面中展示的资源可能有多个，每个资源都需要单独请求；
				
**资源的标识机制：URL**
Uniform Resource Locator：用于描述服务器某特定资源的位置
URL方案：scheme
服务器地址：ip:port
资源路径：/images/banner.jpg
http://www.magedu.com:80/bbs/index.php
https://www.magedu.com:80/bbs/index.php

基本语法：
<scheme>://[<user>:[<password>]@]<host>:<port>/<path>;<params>?<query>#<frag>

>params: 参数
>http://www.magedu.com/bbs/hello;gender=f
>query：查询类参数
>http://www.magedu.com/bbs/item.php?username=tom&title=abc
>frag：标记
>https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html-single/Installation_Guide/index.html#ch-Boot-x86

#### 一次完整的http请求处理过程
>(1) 建立或处理连接：接收请求或拒绝请求（三次握手的过程）；
>(2) 接收请求：接收来自于网络上的主机请求报文中对某特定资源的一次请求的过程；
>(3) 处理请求：对请求报文进行解析，获取客户端请求的资源及请求方法等相关信息；
>(4) 访问资源：获取请求报文中请求的资源；
>(5) 构建响应报文：响应客户端请求；
>(6) 发送响应报文
>(7) 记录日志



![](https://upload-images.jianshu.io/upload_images/16547068-7e3a7a566cc6641e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
						

##### 建立或处理连接连接
提高HTTP 连接性能：
- 并行连接：通过多条TCP 连接发起并发的HTTP 请求
- 持久连接：keep-alive, 长连接，重用TCP 连接，以消除连接和关闭的时延, 以事务个数和时间来决定是否关闭连接
- 管道化连接：通过共享TCP 连接发起并发的HTTP 请求
- 复用的连接：交替传送请求和响应报文（实验阶段，还未实现）


##### 接收请求的模型
**并发访问响应模型：**
- 单进程I/O模型：启动一个进程处理用户请求；这意味着，一次只能处理一个请求，多个请求被串行响应；
- 多进程I/O结构：并行启动多个进程，每个进程响应一个请求；
- 复用的I/O结构：一个进程响应n个请求；
  多线程模式：一个进程生成n个线程，一个线程处理一个请求；
  事件驱动(event-driven)：一个进程直接n个请求；
- 复用的多进程I/O结构：启动多个（m）个进程，每个进程生成（n）个线程；
  响应的请求的数量：m*n



![](https://upload-images.jianshu.io/upload_images/16547068-eb31b3960156c48c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
						

##### 处理请求
服务器对请求报文进行解析，并获取请求的资源及请求方法等相关信息 ，根据方法，资源，首部和可选的主体部分对请求进行处理

**http协议：**
- http请求报文首部 
- http响应报文首部
  				

**请求报文首部的格式：**

```
<method> <URL> <VERSION>
HEADERS: (name: value)
<request body>
```



![](https://upload-images.jianshu.io/upload_images/16547068-0f888977cf7aeb9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
					

##### 访问资源：获取请求报文中请求的资源
>web服务器，即存放了web资源的主机，负责向请求者提供对方请求的静态资源，或动态资源运行生成的结果；这些资源通常应该放置于本地文件系统某路径下；此路径称为DocRoot；



![](https://upload-images.jianshu.io/upload_images/16547068-af73d8f9f1461d67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
				
资源放置于本地文件系统特定的路径：DocRoot 服务的根
DocRoot ---> /var/www/html

例：/var/www/html/images/logo.jpg
http://wwwbaidu.com/images/logo.jpg

**web服务器的资源路径映射方式：**
(a) docroot 
(b) alias
(c) 虚拟主机的docroot
(d) 用户家目录的docroot 
						
**http请求处理中的连接模式：**
>保持连接（长连接）：keep-alive
>时间
>数量
>非保持连接（短连接）

##### 构建响应报文
>一旦Web 服务器识别出了资源，就执行请求方法中描述中的动作，并返回响应报文。响应报文中 ，包含有响应状态码、响应首部，如果生成了响应主体的话，还包括响应主体。

1.响应实体：如果事务处理产生了响应主体，就将内容放在响应报文中回送过去。响应报文中通常包括：
- 描述了响应主体MIME类型的Content-Type首部
- 描述了响应主体长度大小的Content-Length
- 实际报文的主体内容

2.URL 重定向：web 服务构建的响应并非客户端请求的资源，而是资源另外一个访问路径

- 永久重定向：http://www.mageedu.com ---> http://www.xxxx.com
- 临时重定向：http://www.mageedu.com  ---> https://www.mageedu.com

3.MIME 类型：多媒体的邮件扩展

Web 服务器要负责确定响应主体的MIME 类型。有很多配置服务器的方法可以将MIME 类型与资源管理起来

- 魔法分类（扫描首部信息）：Apache web 服务器可以扫描每个资源的内容，并将其与一个已知模式表，首部( 被称为魔法文件) 进行匹配，以决定每个文件的MIME 类型。这样做可能比较慢，但很方便，尤其是文件没有标准扩展名的时候

- 显式分类：可以对Web 服务器进行配置，使其不考虑文件的扩展名或内容，强制特定文件或目录内容拥有某个MIME 类型，例如：php，Apache不识别，强制识别

- 类型协商： 有些Web 服务器经过配置，可以以多种文档格式来存储资源。在这种情况下，可以配置Web 服务器，使其可以通过与用户的协商来决定使用哪种格式( 及相关的MIME 类型)" 最好"

##### 发送响应报文
>Web 服务器通过连接发送数据时也会面临与接收数据一样的问题。服务器可能有很多条到各个客户端的连接， 有些是空闲的，有些在向服务器发送数据，还有一些在向客户端回送响应数据 。服务器要记录连接的状态，还要特别注意对持久连接的处理。对非持久连接而言，服务器应该在发送了整条报文之后，关闭自己这一端的连接 。对持久连接来说，连接可能仍保持打开状态，在这种情况下， 服务器要 正确地计算Content-Length 首部，不然客户端就无法知道响应什么时候结束了。



![](https://upload-images.jianshu.io/upload_images/16547068-2fd3f8a08c6b8178.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




##### 记录日志
>当事务结束时，Web 服务器会在日志文件中添加一个条目，来描述已执行的事务

**日志类型**
- 访问日志：现在愈发重要，大数据的时代
- 错误日志：排错使用




​			