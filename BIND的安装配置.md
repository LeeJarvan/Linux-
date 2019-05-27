### BIND： Berkeley Internet Name Domain
>该工具目前由ISC组织代为维护，站点：ISC.org
>
>Bind是DNS协议的一种实现，其运行的进程名为named

**程序包：**
- bind：提供的dns server程序、以及几个常用的测试程序；
- bind-libs：被bind和bind-utils包中的程序共同用到的库文件；
- bind-utils：bind客户端程序集，例如dig, host, nslookup等；
- bind-chroot：选装，为了安全目的，让named运行于jail模式（沙箱）下；
  		

#### bind主配置文件
**/etc/named.conf**

>包含进来其它文件:
>/etc/named.iscdlv.key
>/etc/named.rfc1912.zones
>/etc/named.root.key

```
options {
        listen-on port 53 { 127.0.0.1; }; #设置监控能与外部主机通信的IP地址
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";  #指定区域数据文件的存放目录
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { localhost; };  #限制查询的来源为本地
        recursion yes;  #是否开启递归查询
        dnssec-enable yes;  #学习时建议关闭
        dnssec-validation yes;  #学习时建议关闭
        bindkeys-file "/etc/named.iscdlv.key";
        managed-keys-directory "/var/named/dynamic";
        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
};
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};
zone "." IN {  #根区域，包含着多个DNS顶级域信息
        type hint; 
        file "named.ca";
};
include "/etc/named.rfc1912.zones";  #把区域管理文件的内容包含进此文件
include "/etc/named.root.key";
```

**格式：**

|   配置段   |      格式       |
| :--------: | :-------------: |
| 全局配置段 | options { ... } |
| 日志配置段 | logging { ... } |
| 区域配置段 |  zone { ... }   |

>zone：那些由本机负责解析的区域，或转发的区域
>						
>注意：**每个配置语句必须以分号结尾**

**缓存名称服务器的配置：**
>监听能与外部主机通信的地址					

```
listen-on port 53;
listen-on port 53 { 172.16.100.67; };
```

**学习时，建议关闭dnssec**

```
dnssec-enable no;
dnssec-validation no;
dnssec-lookaside no;	
```

**关闭仅允许本地查询：**
`//allow-query  { localhost; };`（单行注释）
			
**检查配置文件语法错误：**
`named-checkconf   [/etc/named.conf]`

**解析库文件：/var/named/目录下**
一般名字为：ZONE_NAME.zone

注意：
(1) 一台DNS服务器可同时为多个区域提供解析；
(2) 必须要有根区域解析库文件： named.ca；
正向：named.localhost
反向：named.loopback
							


**bind辅助程序：**
>rndc：remote name domain contoller，远程名称服务器控制工具
>工作在953/tcp端口，但默认监听于127.0.0.1地址，因此仅允许本地使用；


**bind程序安装完成之后，默认即可做缓存名称服务器使用；如果没有专门负责解析的区域，直接即可启动服务；**
- CentOS 6: service  named  start
- CentOS 7: systemctl  start  named.service
  			
```
[root@promote ~]# systemctl status named.service
● named.service - Berkeley Internet Name Domain (DNS)
   Loaded: loaded (/usr/lib/systemd/system/named.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2019-05-13 17:44:41 CST; 15s ago
  Process: 7837 ExecStart=/usr/sbin/named -u named -c ${NAMEDCONF} $OPTIONS (code=exited, status=0/SUCCESS)
  Process: 7834 ExecStartPre=/bin/bash -c if [ ! "$DISABLE_ZONE_CHECKING" == "yes" ]; then /usr/sbin/named-checkconf -z "$NAMEDCONF"; else echo "Checking of zone files is disabled"; fi (code=exited, status=0/SUCCESS)
 Main PID: 7839 (named)
   CGroup: /system.slice/named.service
           └─7839 /usr/sbin/named -u named -c /etc/named.conf

May 13 17:44:41 promote.cache-dns.local named[7839]: zone 0.in-addr.arpa/IN: loaded serial 0
May 13 17:44:41 promote.cache-dns.local named[7839]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
May 13 17:44:41 promote.cache-dns.local named[7839]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0...al 0
May 13 17:44:41 promote.cache-dns.local named[7839]: zone localhost.localdomain/IN: loaded serial 0
May 13 17:44:41 promote.cache-dns.local named[7839]: zone localhost/IN: loaded serial 0
May 13 17:44:41 promote.cache-dns.local named[7839]: all zones loaded
May 13 17:44:41 promote.cache-dns.local named[7839]: running
May 13 17:44:41 promote.cache-dns.local named[7839]: network unreachable resolving './DNSKEY/IN': 2001:500:2::c#53
May 13 17:44:41 promote.cache-dns.local named[7839]: network unreachable resolving './NS/IN': 2001:500:2::c#53
May 13 17:44:41 promote.cache-dns.local systemd[1]: Started Berkeley Internet Name Domain (DNS).
Hint: Some lines were ellipsized, use -l to show in full.
```

**查看其监听的端口：**	

```
[root@promote ~]# netstat -tunlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:53            0.0.0.0:*               LISTEN      7839/named          
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      6848/sshd           
tcp        0      0 127.0.0.1:953           0.0.0.0:*               LISTEN      7839/named          
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      7068/master         
tcp6       0      0 ::1:53                  :::*                    LISTEN      7839/named          
tcp6       0      0 :::22                   :::*                    LISTEN      6848/sshd           
tcp6       0      0 ::1:953                 :::*                    LISTEN      7839/named          
tcp6       0      0 ::1:25                  :::*                    LISTEN      7068/master         
udp        0      0 127.0.0.1:53            0.0.0.0:*                           7839/named          
udp        0      0 0.0.0.0:68              0.0.0.0:*                           6652/dhclient       
udp6       0      0 ::1:53                  :::*                                7839/named          
```

#### 测试工具：
##### dig命令：
格式：`dig  [-t RR_TYPE]  name  [@SERVER]  [query options]`
用于测试dns系统，因此其不会查询hosts文件
若未安装dig命令，则使用`yum install bind-utils -y`安装
							
**查询选项：**
>+[no]trace：跟踪解析过程
>+[no]recurse：进行递归解析

```
[root@promote ~]# dig -t A www.baidu.com

; <<>> DiG 9.9.4-RedHat-9.9.4-73.el7_6 <<>> -t A www.baidu.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43204
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.baidu.com.			IN	A

;; ANSWER SECTION:
www.baidu.com.		8299	IN	CNAME	www.a.shifen.com.
www.a.shifen.com.	8299	IN	A	111.13.100.91
www.a.shifen.com.	8299	IN	A	111.13.100.92

;; Query time: 13 msec
;; SERVER: 221.131.143.69#53(221.131.143.69)
;; WHEN: Mon May 13 17:54:10 CST 2019
;; MSG SIZE  rcvd: 101
```

**注意：反向解析测试**
dig  -x  IP

```
[root@promote ~]# dig -x 121.51.36.46
```

**模拟完全区域传送：**
`dig  -t  axfr  DOMAIN  [@server]`
								
##### host命令：
格式：`host  [-t  RR_TYPE]  name  SERVER_IP`

```
[root@promote ~]# host -t A www.baidu.com
www.baidu.com is an alias for www.a.shifen.com.
www.a.shifen.com has address 111.13.100.92
www.a.shifen.com has address 111.13.100.91
[root@promote ~]# host -t NS baidu.com
baidu.com name server ns4.baidu.com.
baidu.com name server dns.baidu.com.
baidu.com name server ns3.baidu.com.
baidu.com name server ns7.baidu.com.
baidu.com name server ns2.baidu.com.
[root@promote ~]# host -t MX baidu.com
baidu.com mail is handled by 15 mx.n.shifen.com.
baidu.com mail is handled by 20 mx1.baidu.com.
baidu.com mail is handled by 20 jpmx.baidu.com.
baidu.com mail is handled by 20 mx50.baidu.com.
baidu.com mail is handled by 10 mx.maillb.baidu.com.
```

##### nslookup命令：
格式：`nslookup  [-options]  [name]  [server]`
						
**交互式模式：**
nslookup>
server  IP：以指定的IP为DNS服务器进行查询
set  q=RR_TYPE：要查询的资源记录类型
name：要查询的名称

```
[root@promote ~]# nslookup
> server 192.168.0.105
Default server: 192.168.0.105
Address: 192.168.0.105#53
> set q=A
> www.sohu.com
```

##### rndc命令：
>named服务控制命令

```
[root@promote ~]# rndc status
version: 9.9.4-RedHat-9.9.4-73.el7_6 <id:8f9657aa>
CPUs found: 8
worker threads: 8
UDP listeners per interface: 8
number of zones: 101
debug level: 0
xfers running: 0
xfers deferred: 0
soa queries in progress: 0
query logging is OFF
recursive clients: 0/0/1000
tcp clients: 0/100
server is up and running
```

**清空缓存**
`rndc  flush`
						

#### 正反解析区域的配置流程
##### 配置解析一个正向区域：

以magedu.com域为例：
				
**(1) 定义区域**
在主配置文件中或主配置文件辅助配置文件中实现

```
zone  "ZONE_NAME"  IN  {
    type  {master|slave|hint|forward};
    file  "ZONE_NAME.zone"; 
};	
```

```
vim /etc/named.rfc1912.zones 
zone  "magedu.com" IN {
        type master;
        file "magedu.com.zone";
};
```

注意：区域名字即为域名
					
**(2) 建立区域数据文件（主要记录为A或AAAA记录）**
>在/var/named目录下建立区域数据文件

文件为：/var/named/magedu.com.zone

```
[root@promote named]# vim magedu.com.zone
$TTL 3600
$ORIGIN magedu.com.
@       IN      SOA     ns1.magedu.com.   dnsadmin.magedu.com. (
                2017010801
                1H
                10M
		        3D
		        1D )
	    IN      NS      ns1
	    IN      MX   10 mx1
	    IN      MX   20 mx2
ns1     IN      A       172.16.100.67
mx1     IN      A       172.16.100.68
mx2     IN      A       172.16.100.69
www     IN      A       172.16.100.67
web     IN      CNAME   www
bbs     IN      A       172.16.100.70
bbs     IN      A       172.16.100.71						
```

>权限及属组修改：
>`chgrp  named  /var/named/magedu.com.zone`
>`chmod  o=  /var/named/magedu.com.zone`
>							
>检查语法错误：
>`named-checkzone  ZONE_NAME   ZONE_FILE`
>`named-checkconf `

**(3) 让服务器重载配置文件和区域数据文件**
`rndc  reload` 或`systemctl  reload  named.service`
					

**示例：**
首先编辑主配置文件/etc/named.conf中的全局配置，设置监听服务器IP地址及允许DNS查询请求等设置：

```
[root@localhost named]# vim /etc/named.conf
listen-on port 53 { any; };
allow-query     { any; };
recursion no;
dnssec-enable no;
dnssec-validation no;
```

然后编辑/etc/named.rfc1912.zones文件，设置正向区域：

```
[root@localhost named]# vim /etc/named.rfc1912.zones
zone "magedu.com" IN {
        type master;
        file "magedu.com.zone";
        allow-update { none; };
};
```

随后在/var/named/目录下创建区域数据文件magedu.com.zone：

```
[root@localhost named]# vim /var/named/magedu.com.zone
$TTL 3600
@       IN      SOA     ns.magedu.com.  10XXXXXX83.qq.com. (
        20180421
        1D
        1H
        1W
        3H
)
@       IN      NS      ns.magedu.com.
magedu.com.     IN      MX      10      mx1.magedu.com.
magedu.com.     IN      MX      20      mx2.magedu.com.
mx1     IN      A       192.168.0.1
mx2     IN      A       192.168.0.2
ns      IN      A       192.168.0.188
qq      IN      A       114.114.114.114
www     IN      A       199.247.21.135
web     IN      CNAME   www
```

最后检查相关配置文件是否有错误：

```
[root@localhost named]# named-checkconf /etc/named.conf 
[root@localhost named]# named-checkzone magedu.com /var/named/magedu.com.zone 
zone magedu.com/IN: loaded serial 20180421
OK
```

如没有报错，重启加载启动named服务：

```
[root@localhost named]# systemctl restart named
```

在其他主机上验证解析结果：

```
[root@localhost ~]# nslookup
> server 192.168.0.188
Default server: 192.168.0.188
Address: 192.168.0.188#53
> set q=A     
> www.magedu.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   www.magedu.com
Address: 199.247.21.135
> mx1.magedu.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   mx1.magedu.com
Address: 192.168.0.1
> web.magedu.com
Server:     192.168.0.188
Address:    192.168.0.188#53

web.magedu.com  canonical name = www.magedu.com.
Name:   www.magedu.com
Address: 199.247.21.135
> qq.magedu.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   qq.magedu.com
Address: 114.114.114.114
> www.magedu.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   www.magedu.com
Address: 199.247.21.135
```

解析成功。



##### 配置解析一个反向区域

**(1) 定义区域**
在主配置文件中或主配置文件辅助配置文件中实现

```
zone  "ZONE_NAME"  IN  {
   type  {master|slave|hint|forward};
   file  "ZONE_NAME.zone"; 
};	
```

```
vim /etc/named.rfc1912.zones 
zone  "100.16.172.in-addr.arpa" IN {
         type master;
         file "172.16.100.zone";
};
```
>注意：反向区域的名字
>反写的网段地址.in-addr.arpa 
>100.16.172.in-addr.arpa

**(2) 定义区域解析库文件（主要记录为PTR）**
					
示例：区域名称为100.16.172.in-addr.arpa；

```					
$TTL 3600
$ORIGIN 100.16.172.in-addr.arpa.
@       IN      SOA     ns1.magedu.com.  nsadmin.magedu.com. (
	    2017010801
	    1H
        10M
	    3D
		12H )
	    IN      NS      ns1.magedu.com.
67      IN      PTR     ns1.magedu.com.
68      IN      PTR     mx1.magedu.com.
69      IN      PTR     mx2.magedu.com.
70      IN      PTR     bbs.magedu.com.
71      IN      PTR     bbs.magedu.com.
67      IN      PTR     www.magedu.com.					
```


>权限及属组修改：
>`chgrp  named  /var/named/172.16.100.zone`
>`chmod  o=  /var/named/172.16.100.zone`
>							
>检查语法错误：
>`named-checkzone  ZONE_NAME   ZONE_FILE`
>`named-checkconf `

**(3) 让服务器重载配置文件和区域数据文件**
`rndc  reload` 或`systemctl  reload  named.service`					
															
**示例：**
在上述案例1的基础上，首先在/etc/named.rfc1912.zones中编辑添加反向区域：

```
zone "0.168.192.in-addr.arpa" IN {
        type master;
        file "192.168.0.zone";
        allow-update { none; };
};
```

然后在/var/named目录下生成反向区域文件192.168.0.zone:

```
[root@localhost named]# vim /var/named/192.168.0.zone
$TTL 3600
@       IN      SOA     ns.magedu.com.  10XXXXXXX3.qq.com. (
        20180421
        1D
        1H
        1W
        3H
)
@       IN      NS      ns.magedu.com.
ns      IN      A       192.168.0.188
1       IN      PTR     mx1.magdu.com.
2       IN      PTR     mx2.magdu.com.
188     IN      PTR     ns.magedu.com.
```

随后使用命令检查相应的配置文件：

```
[root@localhost named]# named-checkconf /etc/named.conf 
[root@localhost named]# named-checkconf /etc/named.rfc1912.zones 
[root@localhost named]# named-checkzone 0.168.192.in-addr.arpa /var/named/192.168.0.zone 
zone 0.168.192.in-addr.arpa/IN: loaded serial 20180421
OK
```
如无报错，则重新启动named服务：

```
[root@localhost named]# systemctl restart named
```

在其他主机上测试结果：

```
[root@localhost ~]# nslookup 
> server 192.168.0.188
Default server: 192.168.0.188
Address: 192.168.0.188#53
> set q=NS   
> 192.168.0.1
Server:     192.168.0.188
Address:    192.168.0.188#53

1.0.168.192.in-addr.arpa    name = mx1.magdu.com.
> 192.168.0.188
Server:     192.168.0.188
Address:    192.168.0.188#53

188.0.168.192.in-addr.arpa  name = ns.magedu.com.
> 192.168.0.2
Server:     192.168.0.188
Address:    192.168.0.188#53

2.0.168.192.in-addr.arpa    name = mx2.magdu.com.
> 
```

反向解析成功。


#### 主从服务器：
##### 配置一个从区域：
注意：从服务器是区域级别的概念

###### 在从服务器上：
**(1) 定义区域**
定义一个从区域：

```
zone "ZONE_NAME"  IN {
	type  slave;
	file  "slaves/ZONE_NAME.zone";
	masters  { MASTER_IP; };
};
```

注意：type类型是slave，file后面是相对于/var/named/目录的相对路径					
配置文件语法检查：`named-checkconf`
						
**(2) 重载配置**
`rndc  reload`
`systemctl  reload  named.service`
				
###### 在主服务器上：
>确保区域数据文件中为每个从服务配置NS记录，并且在正向区域文件中，需要为每个从服务器的NS记录的主机名配置一个A记录，且此A后面的地址为真正的从服务器的IP地址；序列号要+1

注意：时间要同步，ntpdate命令；
				

###### 示例：
**配置DNS主服务器**

编辑修改/etc/named.conf文件：

```
[root@Master ~]# vim /etc/named.conf
options {
        listen-on port 53 { 192.168.0.188; };  #监听本机IP
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { 192.168.0.0/24; };    #允许来自192.168.0.0/24网段的的解析请求；
        recursion yes;    #开启递归查询
        forward only;    #启用转发域功能，对于本域无法解析的请求，只做转发处理；
        forwarders { 114.114.114.114; };    #指定转发的DNS服务器；
        dnssec-enable no;    #关闭DNS安全扩展功能；
        dnssec-validation no;    #关闭DNS安全验证；
};
.....
```

编辑修改/etc/named.rfc1912.zones：

```
[root@Master ~]# vim /etc/named.rfc1912.zones
zone "magedu.com." IN {    #创建正向解析域
        type master;
        file "magedu.com.zone";
        allow-update { none; };
        allow-transfer { 192.168.0.189;192.168.0.190; };  #允许同步DNS的辅助服务器IP；
        notify yes;  #启用变更通告，当主服务器DNS区域文件发生变更后，通知从服务器进行比较同步；
};
zone "0.168.192.in-addr.arpa" IN {
        type master;
        file "192.168.0.zone";
        allow-update { none; };
        allow-transfer { 192.168.0.189;192.168.0.190;};
        notify yes;
};

```

新建/var/named/magedu.com.zone文件：

```
$TTL 3600
@       IN      SOA     ns1.magedu.com. 1XXXXXX3.qq.com.      (
        2018042101
        1D
        1H
        1W
        3H
)
magedu.com.     IN      NS      ns1.magedu.com.
magedu.com.     IN      NS      ns2.magedu.com.
magedu.com.     IN      NS      ns3.magedu.com.
magedu.com.     IN      MX      10      mx1.magedu.com.
magedu.com.     IN      MX      20      mx2.magedu.com.
mx1     IN      A       192.168.0.1
mx2     IN      A       192.168.0.2
ns1     IN      A       192.168.0.188
ns2     IN      A       192.168.0.189
ns3     IN      A       192.168.0.190
www     IN      A       199.247.21.135
web     IN      CNAME   www
qq      IN      A       59.37.96.63
master  IN      A       192.168.0.188
slave1  IN      A       192.168.0.189
slave2  IN      A       192.168.0.190
```

新建/var/named/192.168.0.zone文件：

```
$TTL 3600
@       IN      SOA     ns1.magedu.com.  1XXXXXXX3.qq.com. (
        2018042101
        1D
        1H
        1W
        3H
)
@       IN      NS      ns1.magedu.com.
@       IN      NS      ns2.magedu.com.  #对于反向区域文件来说，从服务器的NS记录是必须得，否则区域文件的同步会有问题
@       IN      NS      ns3.magedu.com.
1       IN      PTR     mx1.magdu.com.
2       IN      PTR     mx2.magdu.com.
188     IN      PTR     ns1.magedu.com.
189     IN      PTR     ns2.magedu.com.
190     IN      PTR     ns3.magedu.com.
188     IN      PTR     master.magedu.com.
189     IN      PTR     slave1.magedu.com.
190     IN      PTR     slave2.magedu.com.
```

检查相关的配置文件：

```
[root@Master ~]# named-checkconf /etc/named.conf 
[root@Master ~]# named-checkzone magedu.com /var/named/magedu.com.zone 
zone magedu.com/IN: loaded serial 2018042101
OK
[root@Master ~]# named-checkzone 0.168.192.ip-addr.arpa /var/named/192.168.0.zone 
zone 0.168.192.ip-addr.arpa/IN: loaded serial 2018042101
OK
```

如没有错误则启动named服务：

```
[root@Master ~]# systemctl status named
```


**搭建DNS从服务器**
在Slave server 1上编辑/etc/named.conf文件：

```
[root@Slave1 ~]# vim /etc/named.conf
options {
        listen-on port 53 { 192.168.0.189; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { 192.168.0.0/24; };
        recursion yes;
        dnssec-enable no;
        dnssec-validation no;
....
}；
.....
```

随后编辑/etc/named.rfc1912.zones：

```
[root@Slave1 ~]# vim /etc/named.rfc1912.zones
zone "magedu.com" IN {
        type slave;    #指定类型为slave ；
        file "slaves/magedu.com.zone";  #指定同步文件的存放路径及名称；
        masters { 192.168.0.188; };  #指定主服务器的IP;
        masterfile-format text;  #指定区域文件的格式为text，不指定有可能会为乱码
};
zone "0.168.192.in-addr.arpa" IN {
        type slave;
        file "slaves/192.168.0.zone";
        masters { 192.168.0.188; };
        masterfile-format text;
};
```

编辑完成后检查相应的配置文件：

```
[root@Slave1 ~]# named-checkconf /etc/named.conf
```

如无报错，则启动named服务：

```
[root@Slave1 ~]# systemctl start named
[root@localhost ~]# systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
   Loaded: loaded (/usr/lib/systemd/system/named.service; disabled; vendor preset: disabled)
   Active: active (running) since 六 2018-04-21 18:05:47 CST; 5s ago
  Process: 11084 ExecStart=/usr/sbin/named -u named -c ${NAMEDCONF} $OPTIONS (code=exited, status=0/SUCCESS)
  Process: 11081 ExecStartPre=/bin/bash -c if [ ! "$DISABLE_ZONE_CHECKING" == "yes" ]; then /usr/sbin/named-checkconf -z "$NAMEDCONF"; else echo "Checking of zone files is disabled"; fi (code=exited, status=0/SUCCESS)
 Main PID: 11087 (named)
   CGroup: /system.slice/named.service
           └─11087 /usr/sbin/named -u named -c /etc/named.conf

4月 21 18:05:47 localhost.localdomain named[11087]: zone 0.168.192.in-addr.arpa/IN: Transfer started.
4月 21 18:05:47 localhost.localdomain named[11087]: transfer of '0.168.192.in-addr.arpa/IN' from 192.168.0.188#53: conn...42852
4月 21 18:05:47 localhost.localdomain named[11087]: zone 0.168.192.in-addr.arpa/IN: transferred serial 2018042101
4月 21 18:05:47 localhost.localdomain named[11087]: transfer of '0.168.192.in-addr.arpa/IN' from 192.168.0.188#53: Tran.../sec)
4月 21 18:05:47 localhost.localdomain named[11087]: zone 0.168.192.in-addr.arpa/IN: sending notifies (serial 2018042101)
4月 21 18:05:47 localhost.localdomain named[11087]: zone magedu.com/IN: Transfer started.
4月 21 18:05:47 localhost.localdomain named[11087]: transfer of 'magedu.com/IN' from 192.168.0.188#53: connected using ...41953
4月 21 18:05:47 localhost.localdomain named[11087]: zone magedu.com/IN: transferred serial 2018042101
4月 21 18:05:47 localhost.localdomain named[11087]: transfer of 'magedu.com/IN' from 192.168.0.188#53: Transfer complet.../sec)
4月 21 18:05:47 localhost.localdomain named[11087]: zone magedu.com/IN: sending notifies (serial 2018042101)
Hint: Some lines were ellipsized, use -l to show in full.
```

如上述过程所示，从服务器能正常同步主服务器的正向和反向解析区域文件。

**测试DNS主从服务器的解析结果**
主服务器正向解析：

```
[root@Slave2 ~]# dig -t A www.magedu.com @192.168.188

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7_4.2 <<>> -t A www.magedu.com @192.168.188
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 55749
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 3, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.magedu.com.            IN  A

;; ANSWER SECTION:      #成功获取正向解析结果
www.magedu.com.     3600    IN  A   199.247.21.135

;; AUTHORITY SECTION:
magedu.com.     3600    IN  NS  ns1.magedu.com.
magedu.com.     3600    IN  NS  ns2.magedu.com.
magedu.com.     3600    IN  NS  ns3.magedu.com.

;; ADDITIONAL SECTION:
ns1.magedu.com.     3600    IN  A   192.168.0.188
ns2.magedu.com.     3600    IN  A   192.168.0.189
ns3.magedu.com.     3600    IN  A   192.168.0.190

;; Query time: 2 msec
;; SERVER: 192.168.0.188#53(192.168.0.188)
;; WHEN: Sat Apr 21 05:13:05 EDT 2018
;; MSG SIZE  rcvd: 161
```

从服务器正向解析：

```
[root@Slave2 ~]# dig -t A www.magedu.com @192.168.189

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7_4.2 <<>> -t A www.magedu.com @192.168.189
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 36011
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 3, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.magedu.com.            IN  A

;; ANSWER SECTION:    #成功获取正向解析结果
www.magedu.com.     3600    IN  A   199.247.21.135

;; AUTHORITY SECTION:
magedu.com.     3600    IN  NS  ns1.magedu.com.
magedu.com.     3600    IN  NS  ns3.magedu.com.
magedu.com.     3600    IN  NS  ns2.magedu.com.

;; ADDITIONAL SECTION:
ns1.magedu.com.     3600    IN  A   192.168.0.188
ns2.magedu.com.     3600    IN  A   192.168.0.189
ns3.magedu.com.     3600    IN  A   192.168.0.190

;; Query time: 1 msec
;; SERVER: 192.168.0.189#53(192.168.0.189)
;; WHEN: Sat Apr 21 05:13:02 EDT 2018
;; MSG SIZE  rcvd: 161
```


主服务器反向解析：

```
[root@Slave2 ~]# dig -x 192.168.0.1 @192.168.0.188

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7_4.2 <<>> -x 192.168.0.1 @192.168.0.188
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 64876
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 3, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;1.0.168.192.in-addr.arpa.  IN  PTR

;; ANSWER SECTION:     #成功获取反向解析结果
1.0.168.192.in-addr.arpa. 3600  IN  PTR mx1.magdu.com.

;; AUTHORITY SECTION:
0.168.192.in-addr.arpa. 3600    IN  NS  ns2.magedu.com.
0.168.192.in-addr.arpa. 3600    IN  NS  ns3.magedu.com.
0.168.192.in-addr.arpa. 3600    IN  NS  ns1.magedu.com.

;; ADDITIONAL SECTION:
ns1.magedu.com.     3600    IN  A   192.168.0.188
ns2.magedu.com.     3600    IN  A   192.168.0.189
ns3.magedu.com.     3600    IN  A   192.168.0.190

;; Query time: 2 msec
;; SERVER: 192.168.0.188#53(192.168.0.188)
;; WHEN: Sat Apr 21 05:19:32 EDT 2018
;; MSG SIZE  rcvd: 189
```


从服务器反向解析：

```
[root@Slave2 ~]# dig -x 192.168.0.188 @192.168.0.189

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7_4.2 <<>> -x 192.168.0.188 @192.168.0.189
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 58662
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 3, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;188.0.168.192.in-addr.arpa.    IN  PTR

;; ANSWER SECTION:    #成功获取反向解析结果
188.0.168.192.in-addr.arpa. 3600 IN PTR ns1.magedu.com.
188.0.168.192.in-addr.arpa. 3600 IN PTR master.magedu.com.

;; AUTHORITY SECTION:
0.168.192.in-addr.arpa. 3600    IN  NS  ns2.magedu.com.
0.168.192.in-addr.arpa. 3600    IN  NS  ns3.magedu.com.
0.168.192.in-addr.arpa. 3600    IN  NS  ns1.magedu.com.

;; ADDITIONAL SECTION:
ns1.magedu.com.     3600    IN  A   192.168.0.188
ns2.magedu.com.     3600    IN  A   192.168.0.189
ns3.magedu.com.     3600    IN  A   192.168.0.190

;; Query time: 2 msec
;; SERVER: 192.168.0.189#53(192.168.0.189)
;; WHEN: Sat Apr 21 05:20:18 EDT 2018
;; MSG SIZE  rcvd: 202
```

##### 子域授权：		
**正向解析区域授权子域的方法：**

```
ops.magedu.com. 		IN 	NS  	ns1.ops.magedu.com.
ops.magedu.com. 		IN 	NS  	ns2.ops.magedu.com.
ns1.ops.magedu.com. 	IN 	A 	IP.AD.DR.ESS
ns2.ops.magedu.com. 	IN 	A 	IP.AD.DR.ESS
```

**定义转发：**
注意：被转发的服务器必须允许为当前服务做递归；
			
(1) 区域转发：仅转发对某特定区域的解析请求；

```
zone  "ZONE_NAME"  IN {
    type  forward;
    forward  {first|only};
    forwarders  { SERVER_IP; };
};
```

>first：首先转发；转发器不响应时，自行去迭代查询；
>only：只转发；

(2) 全局转发：针对凡本地没有通过zone定义的区域查询请求，通通转给某转发器；

```
vim /etc/named.conf
options {
... ...
forward  {only|first};
forwarders  { SERVER_IP; };
... ...
};
```




#### bind中的安全相关的配置：
>acl：访问控制列表；把一个或多个地址归并一个命名的集合，随后通过此名称即可对此集合全内的所有主机实现统一调用；

```		
acl  acl_name  {
     ip;
     net/prelen;
};
```

**示例：**

```
acl  mynet {
      172.16.0.0/16;
      127.0.0.0/8;
};
```

**bind有四个内置的acl**
- none：没有一个主机
- any：任意主机
- local：本机
- localnet：本机所在的IP所属的网络
  			

|    访问控制指令     |                             作用                             |
| :-----------------: | :----------------------------------------------------------: |
|  allow-query  {};   |                   允许查询的主机；白名单；                   |
| allow-transfer {};  | 允许向哪些主机做区域传送；默认为向所有主机；应该配置仅允许从服务器； |
| allow-recursion {}; |        允许哪此主机向当前DNS服务器发起递归查询请求；         |
|  allow-update {};   |           DDNS，允许动态更新区域数据库文件中内容；           |

#### bind view 视图（智能DNS实现）
>view就是将不同IP地址段发来的查询响应到不同的DNS解析。

##### 示例
>192.168.0.189/32代表电信网络，192.168.0.190/32代表联通网络，进行模拟测试

配置修改此前实例DNS主服务器的named.conf：

```
acl "telecom"{
        192.168.0.189;
};
acl "unicom"{
        192.168.0.190;
};
options{
...
};
logging{
...
};
view  telecom {
        match-clients { telecom;};
        zone "." IN {
                type hint;
                file "named.ca";
        };
        zone "charlie.com" IN {
                type master;
                file "charlie.com.zone.telecom";
        };
        include "/etc/named.rfc1912.zones";
        include "/etc/named.root.key";
};

view unicom {
        match-clients { unicom;};
        zone "." IN {
                type hint;
                file "named.ca";
        };
        zone "charlie.com" IN {
                type master;
                file "charlie.com.zone.unicom";
        };
        include "/etc/named.rfc1912.zones";
        include "/etc/named.root.key";
};

view others {
        match-clients { any;};
        zone "." IN {
                type hint;
                file "named.ca";
        };
        include "/etc/named.rfc1912.zones";
        include "/etc/named.root.key";
};
```

新建charlie.com.zone.telecom：

```
[root@Master ~]# vim /var/named/charlie.com.zone.telecom 
$TTL 3600
@       IN      SOA     ns.charlie.com. 1XXXXXX3.qq.com (
        00
        1D
        1H
        1W
        3H
)
@       IN      NS      ns.charlie.com.
ns      IN      A       192.168.0.188
@       IN      MX      10      mx.charlie.com.
mx      IN      A       192.168.0.188
www     IN      A       1.1.1.1
blog    IN      A       1.1.1.2
```

新建charlie.com.zone.unicom：

```
[root@Master ~]# vim /var/named/charlie.com.zone.unicom
$TTL 3600
@       IN      SOA     ns.charlie.com. 1XXXXX3.qq.com. (
        00
        1D
        1H
        1W
        3H
)
@       IN      NS      ns.charlie.com.
ns      IN      A       192.168.0.188
@       IN      MX      10      mx.charlie.com.
mx      IN      A       192.168.0.188
www     IN      A       2.2.2.1
blog    IN      A       2.2.2.2
```

检查相应的配置文件：

```
[root@Master ~]# named-checkconf /etc/named.conf 
[root@Master ~]# named-checkzone charlie.com /var/named/charlie.com.zone.telecom 
zone charlie.com/IN: loaded serial 0
OK
[root@Master ~]# named-checkzone charlie.com /var/named/charlie.com.zone.unicom 
zone charlie.com/IN: loaded serial 0
OK
```

重启或重载named服务：

```
[root@Master ~]# systemctl restart named

在192.168.0.189从服务器上验证解析结果：
[root@slave1 ~]# nslookup
> server 192.168.0.188
Default server: 192.168.0.188
Address: 192.168.0.188#53
> set q=A
> www.charlie.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   www.charlie.com
Address: 1.1.1.1    #能正确解析出指定的telecomIP；
> blog.charlie.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   blog.charlie.com
Address: 1.1.1.2     #能正确解析出指定的telecomIP；
> ns1.magedu.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   ns1.magedu.com
Address: 192.168.0.188
```

在192.168.0.190从服务器上验证解析结果：

```
[root@slave2 ~]# nslookup
> server 192.168.0.188
Default server: 192.168.0.188
Address: 192.168.0.188#53
> set q=A
> www.charlie.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   www.charlie.com
Address: 2.2.2.1     #能正确解析出指定的unicomIP；
> blog.charlie.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   blog.charlie.com
Address: 2.2.2.2     #能正确解析出指定的unicomIP；
> ns1.magedu.com
Server:     192.168.0.188
Address:    192.168.0.188#53

Name:   ns1.magedu.com
Address: 192.168.0.188
> 
```

到此一个智能DNS解析便搭建完成了，如果能将公网上的电信和联通IP分别写入ACL列表中，并且将此服务器接入了多个运营商线路，使得其能够在公网上提供DNS解析，那么此服务器就能为来自不同运行商的客户端IP提供智能DNS解析了。
