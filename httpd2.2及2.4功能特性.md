#### http开源服务器程序：
- httpd (apache)
- nginx
- lighttpd
  	

**应用程序服务器：**
- IIS： .Net 
- tomcat： .jsp
  	

##### httpd的安装和使用：
>ASF（开源组织）： apache software foundation
>httpd文档：httpd.apache.org
>主程序包：httpd
>工具包：httd-tools

##### httpd的特性：
1.高度模块化： core + modules
2.DSO：dynamic shared object
3.MPM：Multipath processing Modules (多路处理模块)
- prefork：多进程模型，每个进程响应一个请求；
  一个主进程：负责生成子进程及回收子进程；负责创建套接字；负责接收请求，并将其派发给某子进程进行处理；
  n个子进程：每个子进程处理一个请求；
  工作模型：会预先生成几个空闲进程，随时等待用于响应用户请求；最大空闲和最小空闲；
- worker：多进程多线程模型，每线程处理一个用户请求；
  一个主进程：负责生成子进程；负责创建套接字；负责接收请求，并将其派发给某子进程进行处理；
  多个子进程：每个子进程负责生成多个线程；
  每个线程：负责响应用户请求；
  并发响应数量：m*n
  m：子进程数量
  n：每个子进程所能创建的最大线程数量；
- event：事件驱动模型，多进程模型，每个进程响应多个请求；
  一个主进程 ：负责生成子进程；负责创建套接字；负责接收请求，并将其派发给某子进程进行处理；
  子进程：基于事件驱动机制直接响应多个请求；
  				

##### httpd的功能特性：
>CGI：Common Gateway Interface
>虚拟主机：IP，PORT， FQDN
>反向代理
>负载均衡
>路径别名
>丰富的用户认证机制
>basic 
>digest
>支持第三方模块
>......


##### CentOS 6：httpd-2.2
>（1）配置文件：
>主配置文件：**/etc/httpd/conf/httpd.conf**
>自配置文件：**/etc/httpd/conf.d/\*.conf**
>
>（2）服务脚本：**/etc/rc.d/init.d/httpd**
>脚本配置文件：**/etc/sysconfig/httpd**
>
>（3）主程序文件
>**/usr/sbin/httpd
>/usr/sbin/httpd.event
>/usr/sbin/httpd.worker**
>
>（4）日志文件：**/var/log/httpd**
>access_log：访问日志
>error_log：错误日志
>
>（5）站点文档：**/var/www/html**
>
>（6）模块文件路径：**/usr/lib64/httpd/modules**
>
>（7）服务控制和启动
>`chkconfig  httpd  on|off`
>`service  {start|stop|restart|status|configtest|reload}  httpd`
>
>（8）检查配置语法：　　
>`httpd -t`  通用　　
>`service httpd configtest`  centos7不支持				


##### CentOS 7：httpd-2.4
>（1）配置文件：
>主配置文件：**/etc/httpd/conf/httpd.conf**
>自配置文件：**/etc/httpd/conf.d/\*.conf**
>
>（2）模块相关的配置文件：**/etc/httpd/conf.modules.d/\*.conf**
>systemd unit file：**/usr/lib/systemd/system/httpd.service**
>
>（3）主程序文件：**/usr/sbin/httpd**
>httpd-2.4支持MPM的动态切换；
>
>（4）日志文件：**/var/log/httpd**
>access_log：访问日志
>error_log：错误日志
>
>（5）站点文档：**/var/www/html**
>
>（6）模块文件路径：**/usr/lib64/httpd/modules**
>
>（7）服务控制：
>`systemctl  enable|disable  httpd.service`
>`systemctl  {start|stop|restart|status}  httpd.service`
>
>（8）检查配置语法：　　
>`httpd -t`  通用　　				

### httpd-2.4的常用配置
**主配置文件：/etc/httpd/conf/httpd.conf**
>\### Section 1: Global Environment 全局环境配置
>\### Section 2: 'Main' server configuration 主服务器
>\### Section 3: Virtual Hosts 虚拟主机，全是注释，默认没有

**配置格式：**
>directive（指令） value（值）
>directive：不区分字符大小写；
>value：为路径时，是否区分字符大小写，取决于文件系统； 


#### 常用配置：
**vim /etc/httpd/conf/httpd.conf 总配置文件**
			
##### 1、修改监听的IP和PORT
`Listen  [IP-address:]PORTnumber [protocol]`
(1) 省略IP表示为0.0.0.0； 端口绑定所有ip
(2) Listen 指令至少一个，可重复出现多次，写多个，不能为空或注释掉，注释掉服务起不来
- Listen  80
- Listen  8080 写多个就可开启多个端口，但是访问的还是同一个网站

注意：改了端口，要在访问时加上自己改的端口
(3) 修改监听socket，重启服务进程方可生效
(4)限制其必须通过ssl通信时，protocol需要定义为https


##### 2、持久连续（长连接）
Persistent Connection：tPersistent Connection ：连接持久建立，每个资源获取完成后不会断开连接，而是继续等待其它的请求完成，默认关闭持久连接 KeepAlive Off

**断开条件：**
- 数量限制：100
- 时间限制：默认以秒为单位， httpd-2.4 KeepAliveTimeout 支持毫秒级，加单位ms

副作用：对并发访问量较大的服务器，长连接机制会使得后续某些请求无法得到正常响应
折衷：使用较短的持久连接时长，以及较少的请求数量
注意：如果无法搜索到KeepAlive，可自行创建 `vim ./conf.d/keepalive.conf`
```						
KeepAlive  On|Off  
KeepAliveTimeout  15  连接时间
MaxKeepAliveRequests  100  获得最大连接数量
```

**测试：**

```
telnet  WEB_SERVER_IP  PORT
GET  /URL  HTTP/1.1  模仿报文首部
Host: WEB_SERVER_IP
```

```
[root@promote conf]# telnet 192.168.0.105 80
Trying 192.168.0.105...
Connected to 192.168.0.105.
Escape character is '^]'.
GET /index.html HTTP/1.1
Host:192.168.0.105
HTTP/1.1 408 Request Timeout
Date: Fri, 17 May 2019 11:16:17 GMT
Server: Apache/2.4.6 (CentOS)
Content-Length: 221
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>408 Request Timeout</title>
</head><body>
<h1>Request Timeout</h1>
<p>Server timeout waiting for the HTTP request from the client.</p>
</body></html>
Connection closed by foreign host.
```

##### 3、MPM（ Multi-Processing Module ）多路处理模块
**（1）MPM 工作模式介绍：prefork、worker、 event（试验阶段）**
>①prefork：多进程I/O 模型，每个进程响应一个请求，默认模型
>一个主进程 ：生成和回收n个子进程 ， 创建套接字，不响应请求
>多个子进程：工作work 进程，每个子进程处理一个请求；系统初始时，预先生成多个空闲进程，等待请求，最大不超过1024个


![prefork MPM](https://upload-images.jianshu.io/upload_images/16547068-60f80a00f5768083.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>②worker：复用的多进程I/O 模型, 多进程多线程，IIS 使用此模型
>一个主进程： 生成m 个子进程，每个子进程负责生n个线程，每个线程响应一个请求 ，并发响应请求：m\*n

![worker MPM](https://upload-images.jianshu.io/upload_images/16547068-969b76d9a4636586.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>③event：事件驱动模型（worker 模型的变种）
>一个主进程：生成m个子进程，每个进程直接响应n个请求，并发响应请求：m\*n ，有专门的线程来管理这些keep-alive类型的监控线程，当有真实请求时， 将请求传递给服务线程，执行完毕后，又允许释放 。这样增强了高并发场景下的请求处理力能力

![event MPM](https://upload-images.jianshu.io/upload_images/16547068-fd4baaffdb1674bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


httpd-2.2:event 测试版 ，centos6 默认
httpd-2.4:event 稳定版，centos7 默认

>注意：httpd-2.2不支持同时编译多个MPM模块，所以只能编译选定要使用的那个；CentOS 6的rpm包为此专门提供了三个应用程序文件，httpd(prefork), httpd.worker, httpd.event，分别用于实现对不同的MPM机制的支持

**（2）确认方法：**
`ps aux | grep httpd`
默认使用的为/usr/sbin/httpd，其为prefork的MPM模块 

```
[root@promote ~]# ps aux |grep httpd
root       8187  0.0  0.2 228280  5132 ?        Ss   16:24   0:00 /usr/sbin/httpd -DFOREGROUND
apache     8188  0.0  0.1 228280  2996 ?        S    16:24   0:00 /usr/sbin/httpd -DFOREGROUND
apache     8189  0.0  0.1 228280  2996 ?        S    16:24   0:00 /usr/sbin/httpd -DFOREGROUND
apache     8190  0.0  0.1 228280  2996 ?        S    16:24   0:00 /usr/sbin/httpd -DFOREGROUND
apache     8191  0.0  0.1 228280  2996 ?        S    16:24   0:00 /usr/sbin/httpd -DFOREGROUND
apache     8192  0.0  0.1 228280  2996 ?        S    16:24   0:00 /usr/sbin/httpd -DFOREGROUND
root       8225  0.0  0.0 112708   980 pts/0    S+   16:52   0:00 grep --color=auto httpd
```

**查看httpd程序的模块列表：**
查看静态编译的模块：`httpd  -l`

```
[root@promote ~]# httpd -l
Compiled in modules:
  core.c
  prefork.c
  http_core.c
  mod_so.c
```

查看静态编译及动态编译的模块：`httpd  -M`

```
[root@promote ~]# httpd -M
Loaded Modules:
 core_module (static)
 mpm_prefork_module (static)
 http_module (static)
 so_module (static)
 auth_basic_module (shared)
 auth_digest_module (shared)
 authn_file_module (shared)
 authn_alias_module (shared)
 authn_anon_module (shared)
 authn_dbm_module (shared)
 authn_default_module (shared)
······
```

- 动态模块加载：不需重启即生效
- 动态模块路径：/usr/lib64/httpd/modules
  						

**（3）更换使用httpd程序，以支持其它MPM机制**
**/etc/sysconfig/httpd** 在这个文件中改

① HTTPD=/usr/sbin/httpd.worker  默认是被注释的，去掉注释就切换到worker程序了
`pa aux | grep httpd` 查看进程
`service httpd restart` 重启服务
`httpd.worker -l` 模块，命令全部更换

```
[root@promote ~]# ps aux | grep httpd
root       6523  0.0  0.4 186180  4136 ?        Ss   10:27   0:00 /usr/sbin/httpd.worker
apache     6526  0.1  0.5 530440  5372 ?        Sl   10:27   0:00 /usr/sbin/httpd.worker
apache     6527  0.2  0.5 530440  5380 ?        Sl   10:27   0:00 /usr/sbin/httpd.worker
apache     6528  0.1  0.5 530440  5400 ?        Sl   10:27   0:00 /usr/sbin/httpd.worker
root       6645  0.0  0.0 103308   856 pts/3    S+   10:28   0:00 grep httpd
```

② Httpd 2.4 与之不同，以动态模块方式提供
配置文件：/etc/httpd/conf.modules.d/00-mpm.conf
`httpd -M |grep mpm`
`systemctl restart httpd`
`ps aux | grep httpd` 查看进程和线程

注意：重启服务进程方可生效				

**（4）prefork 的默认配置：主配置文件中，搜索/prefork**

```
<IfModule prefork.c>       根据工作环境设置
StartServers       8       一开启服务就准备8个进程
MinSpareServers    5       最小的空闲进程，先预留，不够就生成
MaxSpareServers   20       最大空闲进程
ServerLimit      256       最多进程数, 最大256
MaxClients       256       最大并发数
MaxRequestsPerChild  4000  子进程最多能处理的请求数量
</IfModule>		
```

>MaxRequestsPerChild  4000：在处理MaxRequestsPerChild 个请求之后, 子进程将会被父进程终止，这时候子进程占用的内存就会释放( 为0时永远不释放）

**（5）worker 的默认配置：主配置文件中，搜索/worker**

```
<IfModule worker.c>
StartServers      4     一开启服务就准备4个进程，4x25=100线程
MaxClients      300     最多300个线程
MinSpareThreads  25     最小空闲25线程
MaxSpareThreads  75     最大空闲75线程，和上边冲突，开服务先开启4个进程，再杀死1个进程
ThreadsPerChild  25     每个子进程最大25个线程
MaxRequestsPerChild 0   无限制
</IfModule>
```

**（6） 测试性能：ab命令 `yum -y install httpd-tools`**
`ab -c 100 -n 1000 http://192.168.0.105/`
-c：并发连接数
-n：总的连接数

```
[root@promote ~]# ab -c 100 -n 1000 http://192.168.0.105/
This is ApacheBench, Version 2.3 <$Revision: 1430300 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.0.105 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        Apache/2.4.6
Server Hostname:        192.168.0.105
Server Port:            80

Document Path:          /
Document Length:        105 bytes

Concurrency Level:      100
Time taken for tests:   0.242 seconds
Complete requests:      1000
Failed requests:        0
Write errors:           0
Total transferred:      366000 bytes
HTML transferred:       105000 bytes
Requests per second:    4135.48 [#/sec] (mean)
Time per request:       24.181 [ms] (mean)
Time per request:       0.242 [ms] (mean, across all concurrent requests)
Transfer rate:          1478.11 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    2   4.8      0      24
Processing:     7   18   7.2     16      46
Waiting:        7   18   7.1     16      43
Total:         11   20   9.2     16      50

Percentage of the requests served within a certain time (ms)
  50%     16
  66%     18
  75%     22
  80%     22
  90%     38
  95%     42
  98%     46
  99%     48
 100%     50 (longest request)
```

结果：发现prefork和worker性能没有什么较大的差异
				

##### 4、DSO：Dynamic Shared Object
编辑动态模块配置：/etc/httpd/conf.modules/00-proxy.conf

配置指定实现模块加载格式：将需要禁用的模块用#注释掉即可
格式：`LoadModule  <mod_name>  <mod_path>`

```
[root@promote ~]# cat /etc/httpd/conf.modules.d/00-proxy.conf
# This file configures all the proxy modules:
LoadModule proxy_module modules/mod_proxy.so
LoadModule lbmethod_bybusyness_module modules/mod_lbmethod_bybusyness.so
LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so
LoadModule lbmethod_bytraffic_module modules/mod_lbmethod_bytraffic.so
LoadModule lbmethod_heartbeat_module modules/mod_lbmethod_heartbeat.so
LoadModule proxy_ajp_module modules/mod_proxy_ajp.so
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
LoadModule proxy_express_module modules/mod_proxy_express.so
LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so
LoadModule proxy_fdpass_module modules/mod_proxy_fdpass.so
LoadModule proxy_ftp_module modules/mod_proxy_ftp.so
LoadModule proxy_http_module modules/mod_proxy_http.so
LoadModule proxy_scgi_module modules/mod_proxy_scgi.so
LoadModule proxy_wstunnel_module modules/mod_proxy_wstunnel.so
```

模块文件路径可使用相对路径：
相对于ServerRoot（默认/etc/httpd）
						
##### 5、定义'Main' server的文档页面路径
ServerName：ServerName [scheme://]fully-qualified-domain-name[:port]

文档路径映射格式：`DocumentRoot "/path"`
DocumentRoot 指向的路径为URL路径的起始位置，其相当于站点URL的根路径；

```
DocumentRoot "//app/site1"
DocumentRoot "/var/www/html"   下面会覆盖上面的
```

**注意：**
① 可以写多行，但是下边的会覆盖上边的，最后还是使用下边的，写到自配置文件一样，因为子配置文件Include conf.d/\*.conf在这行设置的上边，会被这行设置覆盖。
② 若设置的主站点不存在，那么服务会启动失败！
						
**URL PATH与FileSystem PATH不是等同的，而是存在一种映射关系**
>URL / --> FileSystem /var/www/html
>/images/logo.jpg --> /var/www/html/images/logo.jpg

注意：SELinux 和iptables 的状态，要关掉
						
##### 6、站点访问控制常见机制
可基于两种机制指明对哪些资源进行何种访问控制访问控制机制有两种：**客户端来源地址，用户账号**
				
①文件系统路径：

```
<Directory  "">
...
</Directory>
						
<File  "">
...
</File>
						
<FileMatch  "PATTERN">
...
</FileMatch>
```

②URL路径：支持正则表达式，通配符

```
<Location  "">
...
</Location>
						
<LocationMatch "">
...
</LocationMatch>
```

###### <Directory>中“基于源地址”实现访问控制
(1) Options：后跟1 个或多个以空白字符分隔的选项列表，可在总配置文件中修改，也可从创建一个自配置文件中修改设置

在选项前的+ ，- 表示增加或删除指定选项
常见选项：
- Indexes：指明的URL 路径下不存在与定义的主页面资源相符的资源文件时，上设置7，返回索引列表给用户，默认是不允许，加上不安全；有需要的时候，例如做yum源的时候
- FollowSymLinks：允许跟踪符号链接文件所指向的源文件，如：链接文件，默认允许
- None ：全部禁用
- All：全部允许


(2) AllowOverride，和上边实现的效果一样，就是把设置放在目录的隐藏文件下.htaccess
与访问控制相关的哪些指令可以放在指定目录下的.htaccess（由AccessFileName 指定）文件中，覆盖之前的，.htaccess是主配置文件中设置指定的

用法：vim /etc/httpd/conf.d/test.conf
只对<directory> 语句有效
```
AllowOverride All:                  所有指令都有效
AllowOverride None ：.htaccess      文件无效
AllowOverride AuthConfig Indexes    除了AuthConfig和Indexes的其它指令都无法覆盖
```

然后在.htaccess文件中设置，.htaccess放在所需要控制的目录下，例bbs目录

vim /app/site1/.htaccess
options +indexes -followsymlinks

(3) order 和allow 、deny
order ：**定义生效次序**；写在**后面**的表示默认法则，覆盖，优先级高

**控制页面资源允许所有来源的主机可访问**
>编辑/etc/httpd/conf/httpd.conf 主配置文件

**http-2.2**

```
<directory"">
    Order allow,deny
    Allow from all
</directory>
```

**http-2.4**

```
<directory"">
     ···
     Require all granted
</directory>
```

**控制页面资源拒绝所有来源的主机可访问**
>编辑/etc/httpd/conf/httpd.conf 主配置文件

**http-2.2**

```
<directory"">
    Order allow,deny
    Deny from all
</directory>
```

**http-2.4**

```
<directory"">
     ···
     Require all denied
</directory>
```

**允许部分主机访问**
**http-2.2**
>Allow from,Deny from

来源地址的表达方法：IP、网络:

```
IP
NetAddr
    172.16
    172.16.0.0
    172.16.0.0/16
    172.16.0.0/255.255.0.0
```

允许192.168的网段访问，禁止地址为192.168.0.105的主机访问
```
Allow from 192.168
Deny from  192.168.0.105
```

**http-2.4**
>基于IP控制：
>Require ip IP地址或网络地址
>Require not ip IP地址或网络地址

>基于主机名控制：
>Require host 主机名或域名
>Require not host 主机名或域名

要放置于<RequireAll>配置块中或<RequireAny>配置块中


​							
##### 7、定义站点主页面：
搜索：/DirectoryIndex
格式：DirectoryIndex  index.html  index.html.var
分析：
① 查询http://192.168.0.105/ 及其子目录时，不指定文件，可以默认打开目录下的index.html文件
② **若没有设置中的两个文件**，看其他设置：**下设置9**，默认是报错；

有特定设置会显示特定设置，如首页；子配置文件 /etc/httpd/conf.d/**welcome.conf** 有设置，若只有 / 或多个，目录下没有index.html，就显示报错页面，**welcome.conf这个设置优先级高**，安全
							
##### 8、定义路径别名
格式：`Alias  /URL/  "/PATH/TO/SOMEDIR/"`
				
Alias /download/ "/rpms/pub/"

示例：
http://www.magedu.com/download/bash.rpm ==> /rpms/pub/bash.rpm
http://www.magedu.com/images/logo.png ==> /www/htdocs/images/logo.png


##### 9、设定默认字符集
`AddDefaultCharset`  UTF-8（全球） 默认

中文字符集：GBK, GB2312（中文简体）, GB18030


##### 10、日志设定
日志类型：访问日志，错误日志
				
**错误日志：**
ErrorLog  logs/error_log
LogLevel  warn
Possible values include（可选值）: debug, info, notice, warn, error, crit, alert, emerg.
						
**访问日志：**
访问日志：搜索:/LogForma
定义访问日志格式：LogFormat format strings，
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined

**使用日志格式：**
CustomLog  logs/access_log  combined
					
参考帮助：http://httpd.apache.org/docs/2.2/mod/mod_log_config.html#formats
					

|     选项名     |                             作用                             |
| :------------: | :----------------------------------------------------------: |
|       %h       |                         客户端IP地址                         |
|       %l       |              Remote User, 通常为一个减号（“-”）              |
|       %u       | 验证（basic ，digest ）远程用户, 非登录访问时，为一个减号"-" |
|       %t       |                    服务器收到请求时的时间                    |
|       %r       | First line of request，即表示请求报文的首行；记录了此次请求的“方法”，“URL”以及协议版本 |
|      %>s       |                          响应状态码                          |
|       %b       |     响应报文的大小，单位是字节；不包括响应报文的http首部     |
|  %{Referer}i   | 请求报文中首部“referer”的值；即从哪个页面中的超链接跳转至当前页面的 |
| %{User-Agent}i |     请求报文中首部“User-Agent”的值；即发出请求的应用程序     |

| status(状态码) |           作用           |
| :------------: | :----------------------: |
|  1xx：100-101  |         信息提示         |
|  2xx：200-206  |           成功           |
|  3xx：300-305  |          重定向          |
|  4xx：400-415  |  错误类信息，客户端错误  |
|  5xx：500-505  | 错误类信息，服务器端错误 |


| 常用状态码 |                             作用                             |
| :--------: | :----------------------------------------------------------: |
|    200     |  成功，请求的所有数据通过响应报文的entity-body部分发送；OK   |
|    301     | 请求的URL指向的资源已经被删除；但在响应报文中通过首部Location指明了资源现在所处的新位置；Moved Permanently |
|    302     | 与301相似，但在响应报文中通过Location指明资源现在所处临时新位置; Found |
|    304     | 客户端发出了条件式请求，但服务器上的资源未曾发生改变，则通过响应此响应状态码通知客户端；Not Modified |
|    401     |       需要输入账号和密码认证方能访问资源；Unauthorized       |
|    403     |                    请求被禁止；Forbidden                     |
|    404     |          服务器无法找到客户端请求的资源；Not Found           |
|    500     |            服务器内部错误；Internal Server Error             |
|    502     |     代理服务器从后端服务器收到了一条伪响应；Bad Gateway      |


​																			
##### 11、基于用户的访问控制
>认证质询：
>WWW-Authenticate：响应码为401，拒绝客户端请求，并说明要求客户端提供账号和密码；				
>
>认证：
>Authorization：客户端用户填入账号和密码后再次发送请求报文；认证通过时，则服务器发送响应的资源；

认证方式有两种：
- basic：明文 
- digest：消息摘要认证
  			

安全域：需要用户认证后方能访问的路径；应该通过名称对其进行标识，以便于告知用户认证的原因；
				
**用户的账号和密码存放于何处？**
>虚拟账号：仅用于访问某服务时用到的认证标识
>					
>存储：
>文本文件；
>SQL数据库；
>ldap目录存储；

**basic认证配置示例**
(1) 定义安全域

```
<Directory "">
	Options None
	AllowOverride None
	AuthType Basic
	AuthName "String“   提示信息
	AuthUserFile  "/PATH/TO/HTTPD_USER_PASSWD_FILE"
	Require  user  username1  username2 ...
</Directory>
```

允许账号文件中的所有用户登录访问：
Require  valid-user
						
(2) 提供账号和密码存储（文本文件）
使用专用命令完成此类文件的创建及用户管理
`htpasswd  [options]   /PATH/TO/HTTPD_PASSWD_FILE  username `
-c：自动创建此处指定的文件，因此，仅应该在此文件不存在时使用；
-m：md5格式加密
-s: sha格式加密
-D：删除指定用户
-b：批模式添加用户
`htpasswd -b  [options]   /PATH/TO/HTTPD_PASSWD_FILE  username password		`						

**基于组账号进行认证**
(1) 定义安全域
在主文件中配置或在/conf.d目录下创建单独文件管理

```	
<Directory "">
	Options None
	AllowOverride None
	AuthType Basic
	AuthName "String“   提示信息
	AuthUserFile  "/PATH/TO/HTTPD_USER_PASSWD_FILE"
	AuthGroupFile "/PATH/TO/HTTPD_GROUP_FILE"
	Require  group  grpname1  grpname2 ...
</Directory>
```

(2) 创建用户账号和组账号文件
组文件：每一行定义一个组
GRP_NAME: username1  username2  ...
											
##### 12、虚拟主机
一个虚拟服务器可服务多个网站
**站点标识： socket**
- IP相同，但端口不同；
- IP不同，但端口均为默认端口；
- FQDN不同；
  请求报文中首部
  Host: www.magedu.com 
  					

**有三种实现方案：**
>基于ip：为每个虚拟主机准备至少一个ip地址；
>基于port：为每个虚拟主机使用至少一个独立的port；
>基于FQDN:为每个虚拟主机使用至少一个FQDN；

注意（专用于httpd-2.2）：一般虚拟机不要与中心主机混用；因此，要使用虚拟主机，得先禁用'main'主机；
禁用方法：注释中心主机的DocumentRoot指令即可；
					
虚拟主机的配置方法：
```
<VirtualHost  IP:PORT>
	ServerName FQDN
	DocumentRoot  ""
</VirtualHost>
```
其它可用指令：
>ServerAlias：虚拟主机的别名；可多次使用；
>ErrorLog：
>CustomLog：
><Directory "">
>...
></Directory>
>Alias
>...

###### 基于IP的虚拟主机：

```
<VirtualHost 172.16.100.6:80>
	ServerName www.a.com
	DocumentRoot "/www/a.com/htdocs"
</VirtualHost>
```

```
<VirtualHost 172.16.100.7:80>
	ServerName www.b.net
	DocumentRoot "/www/b.net/htdocs"
</VirtualHost>
```

```
<VirtualHost 172.16.100.8:80>
	ServerName www.c.org
	DocumentRoot "/www/c.org/htdocs"
</VirtualHost>
```

###### 基于端口的虚拟主机：

```
<VirtualHost 172.16.100.6:80>
	ServerName www.a.com
	DocumentRoot "/www/a.com/htdocs"
</VirtualHost>
```

```
<VirtualHost 172.16.100.6:808>
	ServerName www.b.net
	DocumentRoot "/www/b.net/htdocs"
</VirtualHost>
```

```
<VirtualHost 172.16.100.6:8080>
	ServerName www.c.org
	DocumentRoot "/www/c.org/htdocs"
</VirtualHost>
```

###### 基于FQDN的虚拟主机：

```
<VirtualHost 172.16.100.6:80>
	ServerName www.a.com
	DocumentRoot "/www/a.com/htdocs"
</VirtualHost>
```

```
<VirtualHost 172.16.100.6:80>
	ServerName www.b.net
	DocumentRoot "/www/b.net/htdocs"
</VirtualHost>
```

```
<VirtualHost 172.16.100.6:80>
	ServerName www.c.org
	DocumentRoot "/www/c.org/htdocs"
</VirtualHost>											
```

注意：如果是httpd-2.2，则使用基于FQDN虚机主机时，需要事先使用如下指令：
`NameVirtualHost  IP:PORT`

##### 13、status页面
这个功能需要status_module 模块：`LoadModule status_module`
`httpd -M | grep status` 查询这个模块有没有被加载

在总配置文件中**搜索/server-status**
LoadModule status_module modules/mod_status.so 这个模块在总配置文件有加载

httpd-2.2

```				
<Location /server-status>
	SetHandler server-status
	Order allow,deny
	Allow from 192.168
</Location>				
```

httpd-2.4

```				
<Location /server-status>
	SetHandler server-status
    <RequireAll>	
         Require ip 192.168
    </RequireAll>	
</Location> 			
```


##### 14、curl命令
>curl是基于URL语法在命令行方式下工作的文件传输工具，它支持FTP, FTPS, HTTP, HTTPS, GOPHER, TELNET, DICT, FILE及LDAP等协议。curl支持HTTPS认证，并且支持HTTP的POST、PUT等方法， FTP上传， kerberos认证，HTTP上传，代理服务器， cookies， 用户名/密码认证， 下载文件断点续传，上载文件断点续传, http代理服务器管道（ proxy tunneling）， 甚至它还支持IPv6， socks5代理服务器,，通过http代理服务器上传文件到FTP服务器等等，功能十分强大。

格式：`curl  [options]  [URL...]`

```
[root@promote ~]# curl http://192.168.0.105/
<html>
	<title>Test Page</title>
	<body>
		<h1>Welcome to Mageedu</h1>
		<p>hello</p>
	</body>
</html>
```

**curl的常用选项：**
-A/--user-agent <string>：设置用户代理发送给服务器

```
[root@promote ~]# curl -A "chrome 100.0" http://192.168.0.105/
```

 --basic：使用HTTP基本认证
--tcp-nodelay：使用TCP_NODELAY选项

-e/--referer <URL>：来源网址

```
[root@promote ~]# curl -A "chrome 100.0" -e "http://www.google.com/search?q=hello" http://192.168.0.105/
```

--cacert <file>：CA证书 (SSL)
--compressed：要求返回是压缩的格式
-H/--header <line>：自定义首部信息传递给服务器
-I/--head：只显示响应报文首部信息
--limit-rate <rate>：设置传输速度
-u/--user <user[:password]>：设置服务器的用户和密码
-0/--http1.0：使用HTTP 1.0	

###### elinks
格式：`elinks  [OPTION]... [URL]...`

```
[root@promote ~]# elinks http://192.168.0.105/
```



![](https://upload-images.jianshu.io/upload_images/16547068-0947c8ba5c13ded0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)



**-dump: 不进入交互式模式，而直接将URL的内容输出至标准输出** 

```
[root@promote ~]# elinks -dump  http://192.168.0.105/
                               Welcome to Mageedu

   hello
```


##### 15、user/group
指定以哪个用户的身份运行httpd服务进程；
`User apache`
`Group apache`


##### 16、使用mod_deflate模块压缩页面优化传输速度
适用场景：
(1) 节约带宽，额外消耗CPU；同时，可能有些较老浏览器不支持；
(2) 压缩适于压缩的资源，例如文件文件；

```
#设置一个输出过滤器
SetOutputFilter DEFLATE
#根据内容类型来执行过滤，将以下格式添加到输出过滤器上
# Restrict compression to these MIME types
AddOutputFilterByType DEFLATE text/plain
AddOutputFilterByType DEFLATE text/html
AddOutputFilterByType DEFLATE application/xhtml+xml
AddOutputFilterByType DEFLATE application/x-javascript
AddOutputFilterByType DEFLATE text/javascript
AddOutputFilterByType DEFLATE text/css
#指明压缩级别，压缩级别越高，压缩比越大，就越耗费CPU
#Level of compression(Highest 9 – Lowest 1)
DeflateCompressionLevel 9
#排除以下浏览器不做压缩
BrowserMatch "^Mozilla/2" no-gzip
```

##### 17、https,  http over ssl 
###### SSL会话的简化过程
>(1) 客户端发送可供选择的加密方式，并向服务器请求证书；
>(2) 服务器端发送证书以及选定的加密方式给客户端；
>(3) 客户端取得证书并进行证书验正：

>如果信任给其发证书的CA：
>(a) 验正证书来源的合法性；用CA的公钥解密证书上数字签名；
>(b) 验正证书的内容的合法性：完整性验正
>(c) 检查证书的有效期限；
>(d) 检查证书是否被吊销；
>(e) 证书中拥有者的名字，与访问的目标主机要一致；

>(4) 客户端生成临时会话密钥（对称密钥），并使用服务器端的公钥加密此数据发送给服务器，完成密钥交换；
>(5) 服务用此密钥加密用户请求的资源，响应给客户端；

注意：SSL会话是基于IP地址创建；所以单IP的主机上，仅可以使用一个https虚拟主机；


**配置httpd支持https：**
(1) 为服务器申请数字证书；
测试：通过私建CA发证书
(a) 创建私有CA
(b) 在服务器创建证书签署请求
(c) CA签证
						
(2) 配置httpd支持使用ssl，及使用的证书；
`yum -y install mod_ssl`

配置文件：/etc/httpd/conf.d/ssl.conf
DocumentRoot
ServerName
SSLCertificateFile
SSLCertificateKeyFile
						
(3) 测试基于https访问相应的主机；
`openssl  s_client  [-connect host:port] [-cert filename] [-CApath directory] [-CAfile filename]`
					

**https实现过程**

```   
[root@www CA]# cd /etc/pki/CA/ 
[root@www CA]# (umask 077;openssl genrsa -out private/cakey.pem 2048)
Generating RSA private key, 2048 bit long modulus
..........................................................................+++
...........................+++
e is 65537 (0x10001)
[root@www CA]# ll private/
total 4
-rw-------. 1 root root 1679 Nov 1 04:37 cakey.pem
[root@www CA]# openssl req -new -x509 -key private/cakey.pem -out
cacert.pem
[root@www CA]# touch serial index.txt
[root@www CA]# echo 01 > serial
Httpd添加Https站点
#在Httpd服务器端生成证书签署请求
[root@www CA]# cd /etc/httpd/
[root@www httpd]# mkdir ssl
[root@www httpd]# cd ssl/
[root@www ssl]# (umask 077;openssl genrsa -out httpd.key 1024)
[root@www ssl]# openssl req -new -key httpd.key -out httpd.csr
[root@www ssl]# ls
httpd.csr httpd.key //此时可以将证书签署请求发送给CA
#CA签署请求
[root@www CA]# openssl ca -in /etc/httpd/ssl/httpd.csr -out
certs/httpd.crt
#将生成的证书发送给Httpd服务器
[root@www CA]# ll /etc/httpd/ssl/
-rw-r--r--. 1 root root 3866 Nov 1 04:52 httpd.crt
Httpd添加Https站点
#Httpd安装mod_ssl模块
yum install mod_ssl
httpd -M | grep ssl
Syntax OK
ssl_module (shared)
#修改/etc/httpd/conf.d/ssl.conf配置文件
DocumentRoot "/var/www/html"
ServerName www.magedu.com
SSLCertificateFile /etc/httpd/ssl/httpd.crt
SSLCertificateKeyFile /etc/httpd/ssl/httpd.key
#重启服务，测试https是否正常访问
```


##### 18、httpd自带的工具程序
- htpasswd：basic认证基于文件实现时，用到的账号密码文件生成工具
- apachectl：httpd自带的服务控制脚本，支持start和stop
- apxs：由httpd-devel包提供，扩展httpd使用第三方模块的工具
- rotatelogs：日志滚动工具
  access.log -->
  access.log, access.1.log  -->
  access.log, acccess.1.log, access.2.log
- suexec：访问某些有特殊权限配置的资源时，临时切换至指定用户身份运行
- ab： apache bench
  		


##### 19、httpd的压力测试工具
压力测试：benchmark
命令行工具：ab, webbench, http_load, seige
图形界面工具：jmeter, loadrunner
tcpcopy：网易，复制生产环境中的真实请求，并将之保存下来；
			
格式：`ab  [OPTIONS]  URL`
-n：总请求数
-c：模拟的并行数
-k：以持久连接模式测试

```
[root@promote ~]# ab -n 100000 -c 10 http://192.168.0.105/index.html
This is ApacheBench, Version 2.3 <$Revision: 1430300 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.0.105 (be patient)
Completed 10000 requests
Completed 20000 requests
Completed 30000 requests
Completed 40000 requests
Completed 50000 requests
Completed 60000 requests
Completed 70000 requests
Completed 80000 requests
Completed 90000 requests
Completed 100000 requests
Finished 100000 requests


Server Software:        Apache/2.4.6
Server Hostname:        192.168.0.105
Server Port:            80

Document Path:          /index.html
Document Length:        105 bytes

Concurrency Level:      10
Time taken for tests:   13.924 seconds
Complete requests:      100000
Failed requests:        0
Write errors:           0
Total transferred:      36600000 bytes
HTML transferred:       10500000 bytes
Requests per second:    7181.69 [#/sec] (mean)
Time per request:       1.392 [ms] (mean)
Time per request:       0.139 [ms] (mean, across all concurrent requests)
Transfer rate:          2566.89 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.2      0      33
Processing:     0    1   0.8      1      37
Waiting:        0    1   0.6      1      33
Total:          0    1   0.8      1      37

Percentage of the requests served within a certain time (ms)
  50%      1
  66%      1
  75%      1
  80%      1
  90%      2
  95%      2
  98%      2
  99%      2
```