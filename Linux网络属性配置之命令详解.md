#### ifcfg命令家族： ifconfig, route, netstat
##### ifconfig命令：接口及地址查看和管理
格式1：`ifconfig [INTERFACE]`
```
[root@promote ~]# ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.106  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::ba43:2ebf:979:e12f  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:2e:ff:f7  txqueuelen 1000  (Ethernet)
        RX packets 55  bytes 8466 (8.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 55  bytes 7740 (7.5 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
**ifconfig -a：显示所有接口，包括inactive（非激活）状态的接口**
```
[root@promote ~]# ifconfig -a
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.106  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::ba43:2ebf:979:e12f  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:2e:ff:f7  txqueuelen 1000  (Ethernet)
        RX packets 94  bytes 12346 (12.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 65  bytes 9606 (9.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
格式2：ifconfig interface [aftype] options | address ...
`ifconfig IFACE IP/MASK [up|down]`
`ifconfig IFACE IP netmask NETMASK`

**options：**
[-]promisc：混杂模式
```
[root@promote ~]# ifconfig eth0 promisc
[root@promote ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:0C:29:9A:D6:CB  
          inet addr:192.168.0.105  Bcast:192.168.0.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe9a:d6cb/64 Scope:Link
          UP BROADCAST RUNNING PROMISC MULTICAST  MTU:1500  Metric:1
```
注意：立即送往内核中的TCP/IP协议栈，并生效；

**管理IPv6地址：**
`add addr/prefixlen`
`del addr/prefixlen`

##### route命令：路由查看及管理
**路由条目类型：**

>主机路由：目标地址为单个IP；
>网络路由：目标地址为IP网络；
>默认路由：目标为任意网络，0.0.0.0/0.0.0.0

**查看：**
```
[root@promote ~]# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.0.1     0.0.0.0         UG    100    0        0 ens33
192.168.0.0     0.0.0.0         255.255.255.0   U     100    0        0 ens33
```
**添加：**
`route add [-net|-host] target [netmask Nm] [gw GW] [[dev] If]`

>route add -net  10.0.0.0/8  gw  192.168.10.1  dev  eth1
>route add  -net  0.0.0.0/0.0.0.0  gw 192.168.10.1  
>route add  default  gw 192.168.10.1  

**删除：**
`route  del  [-net|-host] target  [gw Gw]  [netmask Nm]  [[dev] If]`
>route  del  -net  10.0.0.0/8  gw 192.168.10.1
>route  del  default

##### netstat命令：
Print network connections, routing tables, interface statistics, masquerade connections, and multicast  memberships

**显示路由表：netstat  -rn**
-r：显示内核路由表
-n：数字格式
```
[root@promote ~]# netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         192.168.0.1     0.0.0.0         UG        0 0          0 ens33
192.168.0.0     0.0.0.0         255.255.255.0   U         0 0          0 ens33
```
**显示网络连接：**
`netstat  [--tcp|-t]  [--udp|-u]  [--udplite|-U]  [--sctp|-S]  [--raw|-w]  [--listening|-l]  [--all|-a]  [--numeric|-n]   [--extend|-e[--extend|-e]]  [--program|-p]`

>-t：TCP协议的相关连接，连接均有其状态；FSM（Finate State Machine）；
>-u：UDP相关的连接
>-w：raw socket相关的连接
>-l：处于监听状态的连接
>-a：所有状态
>-n：以数字格式显示IP和Port；
>-e：扩展格式
>-p：显示相关的进程及PID；

**常用组合：**
-tan,  -uan,  -tnl,  -unl,  -tunlp
```
[root@promote ~]# netstat -tan  
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0     52 192.168.0.106:22        192.168.0.102:1989      ESTABLISHED
tcp6       0      0 :::22                   :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN     
[root@promote ~]# netstat -uan  
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
udp        0      0 0.0.0.0:68              0.0.0.0:*              
[root@promote ~]# netstat -tnl  
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN     
[root@promote ~]# netstat -unl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
udp        0      0 0.0.0.0:68              0.0.0.0:*    
[root@promote ~]# netstat -tunlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      6979/sshd           
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      7279/master         
tcp6       0      0 :::22                   :::*                    LISTEN      6979/sshd           
tcp6       0      0 ::1:25                  :::*                    LISTEN      7279/master         
udp        0      0 0.0.0.0:68              0.0.0.0:*                           6785/dhclient       

```
**传输层协议：**
>tcp：面向连接的协议；通信开始之前，要建立一个虚链路；通信完成后还要拆除连接；
>udp：无连接的协议；直接发送数据报文；

**显示接口的统计数据：**
`netstat    {--interfaces|-I|-i}    [iface]   [--all|-a]   [--extend|-e]   [--verbose|-v]   [--program|-p]  [--numeric|-n]`

所有接口：
```
[root@promote ~]# netstat -i
Kernel Interface table
Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
ens33            1500     2187      0      0 0           699      0      0      0 BMRU
lo              65536        4      0      0 0             4      0      0      0 LRU
```
指定接口：
netstat  -I<IFace>（中间不带空格）
```
[root@promote ~]# netstat -Iens33
Kernel Interface table
Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
ens33            1500     2375      0      0 0           818      0      0      0 BMRU
```
##### ifup/ifdown命令：

注意：通过配置文件/etc/sysconfig/network-scripts/ifcfg-IFACE来识别接口并完成配置；



##### hostname命令：配置主机名
>查看：hostname
>配置：hostname  HOSTNAME
>当前系统有效，重启后无效；

##### hostnamectl命令（CentOS 7）：
**hostnamectl  status：显示当前主机名信息**
```
[root@promote ~]# hostnamectl status
   Static hostname: localhost.localdomain
Transient hostname: promote.cache-dns.local
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 6f14150e17f24a19917ff162dd467b32
           Boot ID: 4dff9602e6a34d7fbaccb7184deddb8b
    Virtualization: vmware
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-957.el7.x86_64
      Architecture: x86-64
```
**hostnamectl  set-hostname：设定主机名，永久有效**

>配置文件：/etc/sysconfig/network
>HOSTNAME=<HOSTNAME>
>注意：此方法的设置不会立即生效； 但以后会一直有效；

##### 配置DNS服务器指向：
>配置文件：/etc/resolv.conf
>nameserver   DNS_SERVER_IP

**如何测试(host/nslookup/dig)：**
`dig -t A FQDN`（主机名）
FQDN --> IP
`dig -x IP`
IP --> FQDN

#### iproute家族：
##### ip命令：
show / manipulate routing, devices, policy routing and tunnels
格式：`ip [ OPTIONS ] OBJECT { COMMAND | help `

**OBJECT := { link | addr | route | netns  }**
注意： OBJECT可简写，各OBJECT的子命令也可简写；

ip  OBJECT：
**ip link： network device configuration**
```
[root@promote ~]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:2e:ff:f7 brd ff:ff:ff:ff:ff:ff
```
**ip  link  set - change device attributes**
>dev NAME (default)：指明要管理的设备，dev关键字可省略；
>up和down：启用和禁用
>multicast on或multicast off：启用或禁用多播功能；
>name NAME：重命名接口
>mtu NUMBER：设置MTU的大小，默认为1500；
>netns PID：ns为namespace，用于将接口移动到指定的网络名称空间；

>ip  link  show  - display device attributes
>ip  link  help -  显示简要使用帮助；

**ip netns：  - manage network namespaces**
>ip  netns  list：列出所有的netns
>ip  netns  add  NAME：创建指定的netns
>ip  netns  del  NAME：删除指定的netns
>ip  netns   exec  NAME  COMMAND：在指定的netns中运行命令

**ip address - protocol address management**
>ip address add - add new protocol address
>`ip  addr  add  IFADDR  dev  IFACE`
>`[root@promote ~]# ip addr add 192.168.10.100/24 dev eth1`

[label NAME]：为额外添加的地址指明接口别名
`[root@promote ~]# ip addr add 192.168.10.100/24 dev eth1 label eth1:0`
[broadcast ADDRESS]：广播地址；会根据IP和NETMASK自动计算得到；
[scope SCOPE_VALUE]：作用域
- global：全局可用
- link：接口可用
- host：仅本机可用                                                

>ip address delete - delete protocol address
>`ip addr delete IFADDR dev IFACE`


>ip address show - look at protocol addresses
>`ip addr list [IFACE]`：显示接口的地址

>ip address flush - flush protocol addresses
>`ip addr flush dev IFACE`

**ip route - routing table management**
>ip route add - add new route
>ip route change - change route
>ip route replace - change or add new one
>`ip route add TYPEPREFIX via GW [dev IFACE] [src SOURCE_IP]`
>
>示例：
>ip route add 192.168.0.0/24  via 10.0.0.1  dev eth1 src  10.0.20.100
>ip  route  add default  via  GW                        

>ip route delete - delete route
>`ip route del TYPEPRIFIX`
>
>示例：
>ip  route delete  192.168.1.0/24
>ip route show - list routes
>TYPE PRIFIX  

>ip route flush - flush routing tables
>TYPE  PRIFIX

>ip route get - get a single route
>ip  route  get  TYPE PRIFIX
>
>示例：ip route  get  192.168.0.0/24

##### ss命令：
格式：`ss [options] [ FILTER ]`

**options：**
>-t：TCP协议的相关连接
>-u：UDP相关的连接
>-w：raw socket相关的连接
>-l：监听状态的连接
>-a：所有状态的连接
>-n：数字格式
>-p：相关的程序及其PID
>-e：扩展格式信息
>-m：内存用量
>-o：计时器信息

**FILTER := [ state TCP-STATE ]  [ EXPRESSION ]**

**TCP的常见状态：**
>TCP FSM：
>LISTEN：监听
>ESTABLISEHD：建立的连接
>FIN_WAIT_1：
>FIN_WAIT_2：
>SYN_SENT：
>SYN_RECV：
>CLOSED：

**EXPRESSION：**
>dport = 目标端口
>sport = 源端口
>
>示例：'( dport = :22 or sport = :22)'
>~]# ss   -tan    '(  dport = :22 or sport = :22  )'
>~]# ss  -tan  state  ESTABLISHED

#### 通过修改配置文件改变网络属性
IP/NETMASK/GW/DNS等属性的配置文件：/etc/sysconfig/network-scripts/ifcfg-IFACE
IFACE：接口名称；
路由的相关配置文件：/etc/sysconfig/networkj-scripts/route-IFACE

>配置文件/etc/sysconfig/network-scripts/ifcfg-IFACE通过大量参数来定义接口的属性；其可通过vim等文本编辑器直接修改，也可以使用专用的命令的进行修改（CentOS 6：system-config-network (setup)，CentOS 7: nmtui）

![CentOS 7](https://upload-images.jianshu.io/upload_images/16547068-e599aeffc8359e08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![CentOS 6](https://upload-images.jianshu.io/upload_images/16547068-f987e3c595e6136f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**ifcfg-IFACE配置文件参数：**
```
[root@promote network-scripts]# vi ifcfg-ens33 
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="29b3550e-2483-41a6-bb4a-e0621d862cf7"
DEVICE="ens33"
ONBOOT="yes"
```
>DEVICE：此配置文件对应的设备的名称；
>ONBOOT：在系统引导过程中，是否激活此接口；
>UUID：此设备的惟一标识；
>IPV6INIT：是否初始化IPv6；
>BOOTPROTO：激活此接口时使用什么协议来配置接口属性，常用的有dhcp、bootp、static、none；
>TYPE：接口类型，常见的有Ethernet, Bridge；
>DNS1：第一DNS服务器指向；
>DNS2：备用DNS服务器指向；
>DOMAIN：DNS搜索域；
>IPADDR： IP地址；
>NETMASK：子网掩码；CentOS 7支持使用PREFIX以长度方式指明子网掩码；
>GATEWAY：默认网关；
>USERCTL：是否允许普通用户控制此设备；
>PEERDNS：如果BOOTPROTO的值为“dhcp”，是否允许dhcp server分配的DNS服务器指向覆盖本地手动指定的DNS服务器指向；默认为允许；
>HWADDR：设备的MAC地址；
>NM_CONTROLLED：是否使用NetworkManager服务来控制接口；

#### 网络服务：
>network
>NetworkManager

##### 管理网络服务：
>CentOS 6:  service  SERVICE  {start|stop|restart|status}
>CentOS 7：systemctl  {start|stop|restart|status}  SERVICE[.service]

>配置文件修改之后，如果要生效，需要重启网络服务；
>CentOS 6：# service  network  restart
>CentOS 7：# systemctl  restart  network.service

**用到非默认网关路由：/etc/sysconfig/network-scripts/route-IFACE**
支持两种配置方式，但不可混用：
>(1) 每行一个路由条目：
>TARGET  via  GW
>
>(2) 每三行一个路由条目：
>ADDRESS#=TARGET
>NETMASK#=MASK
>GATEWAY#=NEXTHOP

#### 给接口配置多个地址：
ip addr之外，ifconfig或配置文件都可以；
>(1) ifconfig  IFACE_LABEL  IPADDR/NETMASK
>IFACE_LABEL： eth0:0, eth0:1, ...
>
>(2) 为别名添加配置文件；
>DEVICE=IFACE_LABEL
>BOOTPROTO：网上别名不支持动态获取地址，只能是static, none

##### nmcli命令：
格式：`nmcli [OPTIONS] OBJECT {COMMAND|help}`
>device - show and manage network interfaces
>COMMAND := { status | show | connect | disconnect | delete | wifi | wimax }
```
[root@promote ~]# nmcli device show
GENERAL.DEVICE:                         ens33
GENERAL.TYPE:                           ethernet
GENERAL.HWADDR:                         00:0C:29:2E:FF:F7
GENERAL.MTU:                            1500
GENERAL.STATE:                          100 (connected)
GENERAL.CONNECTION:                     ens33
GENERAL.CON-PATH:                       /org/freedesktop/NetworkManager/ActiveConnection/1
WIRED-PROPERTIES.CARRIER:               on
IP4.ADDRESS[1]:                         192.168.0.106/24
IP4.GATEWAY:                            192.168.0.1
IP4.ROUTE[1]:                           dst = 0.0.0.0/0, nh = 192.168.0.1, mt = 100
IP4.ROUTE[2]:                           dst = 192.168.0.0/24, nh = 0.0.0.0, mt = 100
IP4.DNS[1]:                             221.131.143.69
IP4.DNS[2]:                             112.4.0.55
IP4.DOMAIN[1]:                          DHCP
IP4.DOMAIN[2]:                          HOST
IP6.ADDRESS[1]:                         fe80::ba43:2ebf:979:e12f/64
IP6.GATEWAY:                            --
IP6.ROUTE[1]:                           dst = fe80::/64, nh = ::, mt = 100
IP6.ROUTE[2]:                           dst = ff00::/8, nh = ::, mt = 256, table=255

GENERAL.DEVICE:                         lo
GENERAL.TYPE:                           loopback
GENERAL.HWADDR:                         00:00:00:00:00:00
GENERAL.MTU:                            65536
GENERAL.STATE:                          10 (unmanaged)
GENERAL.CONNECTION:                     --
GENERAL.CON-PATH:                       --
IP4.ADDRESS[1]:                         127.0.0.1/8
IP4.GATEWAY:                            --
IP6.ADDRESS[1]:                         ::1/128
IP6.GATEWAY:                            --
```
>connection - start, stop, and manage network connections
>COMMAND := { show | up | down | add | edit | modify | delete | reload | load }
```
[root@promote ~]# nmcli connection show
NAME   UUID                                  TYPE      DEVICE 
ens33  29b3550e-2483-41a6-bb4a-e0621d862cf7  ethernet  ens33  
```
**modify [ id | uuid | path ] <ID> [+|-]<setting>.<property> <value>**
如何修改IP地址等属性：
>`nmcli conn modify IFACE [+|-]setting.property value`
>ipv4.address
>ipv4.gateway
>ipv4.dns1
>ipv4.method
>manual


#### tcpdump命令：
tcpdump - dump traffic on a network
>是一款 sniffer 工具，它可以打印所有经过网络接口的数据包的头信息，也可以使用-w选项将数据包保存到文件中，方便以后分析。

直接启动tcpdump将监视第一个网络接口上所有流过的数据包
`tcpdump`
监视指定网络接口的数据包
`tcpdump -i eth1`
如果不指定网卡，默认 tcpdump 只会监视第一个网络接口，一般是 eth0
监视指定主机的数据包

打印所有进入或离开sundown的数据包。
`tcpdump host sundown`

也可以指定 ip , 例如截获所有 210.27.48.1 的主机收到的和发出的所有数据包
`tcpdump host 210.27.48.1`

**option：**
>-a #将网络地址和广播地址转变成名字
>-A #以ASCII格式打印出所有分组，并将链路层的头最小化
>-b #数据链路层上选择协议，包括ip/arp/rarp/ipx都在这一层
>-c #指定收取数据包的次数，即在收到指定数量的数据包后退出tcpdump
>-d #将匹配信息包的代码以人们能够理解的汇编格式输出
>-dd  #将匹配信息包的代码以c语言程序段的格式输出
>-ddd #将匹配信息包的代码以十进制的形式输出
>-D #打印系统中所有可以监控的网络接口
>-e #在输出行打印出数据链路层的头部信息
>-f #将外部的Internet地址以数字的形式打印出来，即不显示主机名
>-F #从指定的文件中读取表达式，忽略其他的表达式
>-i #指定监听网络接口
>-l #使标准输出变为缓冲形式，可以数据导出到文件
>-L #列出网络接口已知的数据链路
>-n #不把网络地址转换为名字
>-N 不输出主机名中的域名部分，例如www.baidu.com只输出www
>-nn #不进行端口名称的转换
>-P #不将网络接口设置为混杂模式
>-q #快速输出，即只输出较少的协议信息
>-r #从指定的文件中读取数据，一般是-w保存的文件
>-w #将捕获到的信息保存到文件中，且不分析和打印在屏幕
>-s #从每个组中读取在开始的snaplen个字节，而不是默认的68个字节
>-S #将tcp的序列号以绝对值形式输出，而不是相对值
>-T #将监听到的包直接解析为指定的类型的报文，常见的类型有rpc（远程过程调用）和snmp（简单网络管理协议）
>-t #在输出的每一行不打印时间戳
>-tt #在每一行中输出非格式化的时间戳
>-ttt #输出本行和前面以后之间的时间差
>-tttt #在每一行中输出data处理的默认格式的时间戳
>-u #输出未解码的NFS句柄
>-v #输出稍微详细的信息，例如在ip包中可以包括ttl和服务类型的信息
>-vv#输出相信的保报文信息