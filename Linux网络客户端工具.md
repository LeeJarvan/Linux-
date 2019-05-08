### ICMP：Internet Control Message Protocol       
Internet 控 制 报 文 协 议 ICMP （ Internet Control MessageProtocol）是网络层的一个重要协议。ICMP协议用来在网络设备间传递各种差错和控制信息，它对于收集各种网络信息、诊断和排除各种网络故障具有至关重要的作用。使用基于ICMP的应用时，需要对ICMP的工作原理非常熟悉。
#### ping命令：    
>send ICMP ECHO_REQUEST to network hosts

格式：ping  [OPTION]  destination
**-c #：发送的ping包个数**
```
[root@promote ~]# ping -c 3 qq.com
PING qq.com (111.161.64.40) 56(84) bytes of data.
64 bytes from dns40.online.tj.cn (111.161.64.40): icmp_seq=1 ttl=44 time=55.1 ms
64 bytes from dns40.online.tj.cn (111.161.64.40): icmp_seq=2 ttl=44 time=55.9 ms
64 bytes from dns40.online.tj.cn (111.161.64.40): icmp_seq=3 ttl=44 time=56.7 ms

--- qq.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 55.103/55.930/56.767/0.679 ms
```
**-w #：ping命令超时时长**
```
[root@promote ~]# ping -w 2 qq.com
PING qq.com (111.161.64.48) 56(84) bytes of data.

--- qq.com ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1000ms
```
**-W #：一次ping操作中，等待对方响应的超时时长**
**-s #：指明ping包报文大小**
```
[root@promote ~]# ping -s 128 qq.com
PING qq.com (111.161.64.40) 128(156) bytes of data.
136 bytes from dns40.online.tj.cn (111.161.64.40): icmp_seq=1 ttl=44 time=56.0 ms
136 bytes from dns40.online.tj.cn (111.161.64.40): icmp_seq=2 ttl=44 time=55.5 ms
136 bytes from dns40.online.tj.cn (111.161.64.40): icmp_seq=3 ttl=44 time=56.9 ms
136 bytes from dns40.online.tj.cn (111.161.64.40): icmp_seq=4 ttl=44 time=55.6 ms
```
#### hping命令： 
>[root@promote ~]# yum install hping3 -y 需要事先安装

>send (almost) arbitrary TCP/IP packets to network hosts

**快速发ping包**
**--fast**
**--faster**
**--flood**

**-i uX：每哥多少微秒发送一个包**
```
[root@promote ~]# hping -i 10000 qq.com
HPING qq.com (ens33 111.161.64.40): NO FLAGS are set, 40 headers + 0 data bytes
^C
--- qq.com hping statistic ---
1 packets transmitted, 0 packets received, 100% packet loss
round-trip min/avg/max = 0.0/0.0/0.0 ms
```
#### traceroute命令：
>print the route packets trace to network host
>跟踪从源主机到目标主机之间经过的网关；
```
[root@promote ~]# traceroute qq.com
traceroute to qq.com (111.161.64.40), 30 hops max, 60 byte packets
 1  promote.cache-dns.local (192.168.0.1)  0.723 ms  0.539 ms  0.365 ms
 2  221.181.161.66 (221.181.161.66)  3.302 ms  3.226 ms  3.061 ms
 3  221.181.159.117 (221.181.159.117)  3.641 ms  3.929 ms  4.314 ms
 4  promote.cache-dns.local (183.207.19.21)  9.358 ms 53.29.207.183.static.js.chinamobile.com (183.207.29.53)  9.363 ms 149.25.207.183.static.js.chinamobile.com (183.207.25.149)  9.322 ms
```
#### ftp命令：
>ftp: File Transfer Protocol
>ftp服务命令行客户端工具；
#### lftp命令：
格式：lftp  [-p port]  [-u user[,pass]] [site]
>get, mget（m开头表示多文件）
>put, mput
>rm, mrm
#### lftpget命令：
>lftp专用下载工具

格式：lftpget [-c] [-d] [-v] URL [URL...]
**-c：继续此前的下载**
#### wget命令：
>非交互式网络下载器

格式：wget [option]... [URL]...
**-b：在后台执行下载操作**
**-q：静默模式，不显示下载进度**
**-O file：下载的文件的保存位置**
**-c：续传**
**--limit-rate=amount：以指定的速率传输文件**