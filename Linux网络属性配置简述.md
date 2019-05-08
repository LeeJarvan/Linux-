#### OSI的概念：
Open System Interconnect开放系统互连参考模型，是由ISO（国际标准化组织）定义的。它是个灵活的、
稳健的和可互操作的模型，并不是协议，是用来了解和设计网络体系结构的。

**OSI模型的目的：**
规范不同系统的互联标准，使两个不同的系统能够较容易的通信，而不需要改变底层的硬件或软件的逻辑，每个厂商都要遵守这个模型。
![](https://upload-images.jianshu.io/upload_images/16547068-84e9f01c0554bb1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### OSI的优点(不限于)
建立七层模型的主要目的是为解决异种网络互连时所遇到的兼容性问题。它的最大优点是
将服务、接口和协议这三个概念明确地区分开来：
服务：说明某一层为上一层提供一些什么功能
接口：说明上一层如何使用下层的服务
协议：涉及如何实现本层的服务；
![](https://upload-images.jianshu.io/upload_images/16547068-90c0d7466099ab7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**应用层**

*  为应用软件提供接口，使应用程序能够使用网络服务
*  常见的应用层协议：
*  http(80)、ftp(20/21)、smtp(25)、pop3(110)、
*  telnet(23)、dns(53)等

**表示层**
*  数据的解码和编码  JPEG（标准）
*  数据的加密和解密
*  数据的压缩和解压缩  ZIP（标准）

**会话层**
*  负责建立、管理和终止表示层实体之间的会话连接
*  在各个节点之间提供会话控制

**传输层**
*  负责将来自上层应用程序的数据进行分段和重组，并将它们组合为同样的数据流形式。
*  提供端到端的数据传输服务
*  工作在传输层的协议：
  TCP（可靠的）
  UDP（不可靠的）

**网络层**
*  定义了逻辑地址（三层地址）
*  进行路由选择、维护路由表
*  负责将分组数据从源端传输到目的端

**设备：路由器（隔离广播）**
>广播、组播控制
>维护路由表，维护路由信息
>路由发现及路径选择
>数据转发
>连接广域网(WAN)、地址转换（NAT）
>真三层交换机：具有路由功能（RIP OSPF BGP ISIS等路由协议）的交换机 
>弱三层交换机：只支持RIP协议的交换机
>路由器的每一个接口属于一个广播域

**数据链路层**
*  提供可靠的数据传输服务，把帧从一跳（结点）移动到另一跳（结点）。【从一个交换机传输到另一个交换机】
*  组帧、物理编址（MAC地址）、流量控制、差错控制、接入控制
*  数据链路层在物理层基础上向网络层提供服务。
*  数据链路层在物理链路上提供可靠的数据传输。
*  局域网的数据链路层协议有以太网、令牌环网等。
*  广域网数据链路层协议有PPP、HDLC、Frame Relay、X.25等。

**以太网二层逻辑地址：MAC地址**
![](https://upload-images.jianshu.io/upload_images/16547068-577595f3ad6daba5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

>MAC（Media Access Control）地址，48bits，意译为媒体访问控制，或称为物理地址、硬件地址，用来定义物理设备的位置。
>ICANN组织：把前24bits，2^24地址块，售卖

>LLC（Logic Link Control）逻辑链路控制：负责识别网络层协议，然后对它们进行封装；提供流量控制并控制比特流的排序

**设备：交换机（Switch）（独享带宽）：多端口网桥**
>实现点到点的数据传输
>每个端口是一个冲突域
>整台交换机属于一个广播域

![](https://upload-images.jianshu.io/upload_images/16547068-5bf6c0e65bd05372.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
![](https://upload-images.jianshu.io/upload_images/16547068-9f4f6b509aece052.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

**物理层**
>负责把逐个的比特从一跳（结点）移动到另一跳（结点），以0101这种二进制形式进行传播。

物理层功能：
*  定义接口和媒体的物理特性（USB,RJ45）
*  定义比特的表示、数据传输速率、信号的传输模式（单工、半双工、全双工）
        单工：单向传输（汽车钥匙）
         半双工：对讲机
         全双工：电话

*  定义网络物理拓扑（网状、星型、环型、总线型等拓扑）

物理层标准规定了信号、连接器和电缆要求。

**设备：集线器 Hub**
![](https://upload-images.jianshu.io/upload_images/16547068-b526f8fd97e0ea6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>设备共享带宽
>整台设备在同一个冲突域 (Collision Domain)
>整台设备都在同一个广播域( Broadcast Domain)

**广播域**
>单播：A→B
>广播：A→多人（类似操场广播体操）
>组播：A→某个房间里的人（类似群聊）

![](https://upload-images.jianshu.io/upload_images/16547068-8bd91bccd29a3ca7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>终端站点越多，冲突(域)越大（载波监听）
>采用CSMA/CD机制 16字箴言 “先听后发,边发边听,冲突停止,延迟重发”

##### OSI模型与TCP/IP协议簇
![](https://upload-images.jianshu.io/upload_images/16547068-0f6a1f1156f2286b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* TCP/IP协议簇是Internet的基础，也是当今最流行的组网形式；
* 传输控制协议/英特网协议(TCP/IP)组是由美国国防部(DoD)所创建的，主要用来确保数据的完整性及在毁灭性战争中维持通信；
* 是由一组不同功能的协议组合在一起构成的协议簇；
* 利用一组协议完成OSI所实现的功能。

#### IP地址的构成及子网掩码
##### IP编址
![](https://upload-images.jianshu.io/upload_images/16547068-267105952d4e6173.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* IP地址分为网络部分和主机部分。
* IP地址由32个二进制位组成，通常用点分十进制形式表示。
* 相同网络位可以互相通信。

![](https://upload-images.jianshu.io/upload_images/16547068-0bed9625635b76a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### IP地址分类：
![](https://upload-images.jianshu.io/upload_images/16547068-914236731aca475b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/16547068-091b964172e2865b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### IP地址类型
**私有地址范围**
* 10.0.0.0~10.255.255.255
* 172.16.0.0~172.31.255.255
* 192.168.0.0~192.168.255.255

**特殊地址**
* 127.0.0.0 ~ 127.255.255.255（环回地址）  用于检测网路是否正常
* 0.0.0.0（任意网络）
* 255.255.255.255（任意网络的广播地址）

>公有地址：唯一的，不可以重复使用的地址，在公网（互联网）中使用的是公有地址。
>私有地址：能复用的地址，在家里，公司中使用的是私有地址。（局域网中使用）                      
#### 子网掩码
![](https://upload-images.jianshu.io/upload_images/16547068-4053f5696d405402.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>32位二进制表示子网掩码
>“1”表示网络位；“0”表示主机位
>若网络位为24位，则需要有24个连续的“1”
##### 默认子网掩码
![](https://upload-images.jianshu.io/upload_images/16547068-a2080e896b55ac5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### 地址规划
![](https://upload-images.jianshu.io/upload_images/16547068-63ab0f76511a2e06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>网络地址（主机地址为0）=IP地址和子网掩码的“与”运算结果

#### 动态路由协议的分类
![](https://upload-images.jianshu.io/upload_images/16547068-28930fb1c72f8779.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### 路由的作用范围
![](https://upload-images.jianshu.io/upload_images/16547068-1a5bf7cdda1309cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


>路由条目：
>目标地址  下一跳(nexthop)
>目标地址的类别：
>主机：主机路由
>网络：网络路由
>0.0.0.0/0.0.0.0：默认路由                            

**OS：多用户，多任务**
**多任务：多进程**

**互联网通信时，进程的数字标识：16bits**
>0-65535：1-65535
>1-1023：固定分配，而且只有管理员有权限启用
>1024-4W：半固定
>4W+：临时端口

**段口进程地址：**
IP+PORT：套接字地址




#### 将Linux主机接入到网络中：
>- IP/NETMASK：本地通信
>- 路由（网关）：跨网络通信
>- DNS服务器地址：基于主机名的通信
>  主DNS服务器地址
>  备用DNS服务器地址
>  第三备份DNS服务器地址

#### 配置方式：
##### 静态指定：
**命令简述：**
>ifcfg家族：
>- ifconfig：配置IP，NETMASK
>- route：路由
>- netstat：状态及统计数据查看

>iproute2家族：
>- ip OBJECT：
>  addr：地址和掩码；
>  link：接口
>  route：路由
>- ss：状态及统计数据查看

>CentOS 7：nm(Network Manager)家族
>- nmcli：命令行工具
>- nmtui：text window 工具

**注意：**
>(1) DNS服务器指定    
>配置文件：/etc/resolv.conf
>(2) 本地主机名配置
>hostname
>配置文件：/etc/sysconfig/network
>CentOS 7：hostnamectl                    

**配置文件：**
>RedHat及相关发行版
>/etc/sysconfig/network-scripts/ifcfg-NETCARD_NAME

##### 动态分配：依赖于本地网络中有DHCP服务
>DHCP：Dynamic Host Configure Procotol
>* 为了实现网络可以动态合理地分配IP地址给主机使用，提出了DHCP协议。
>* DHCP（Dynamic Host Configuration Protocol，动态主机配置协议）是一个局域网的网络协议，使用UDP协议工作。
>* DHCP相对于静态手工配置的优点：
>  效率高
>  灵活性强
>  易于管理

**DHCP应用场景**
![](https://upload-images.jianshu.io/upload_images/16547068-06ed751cba9b4fb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* DHCP服务器能够为大量主机分配IP地址，并能够集中管理。
* DHCP采用C/S模式



#### 网络接口命名方式：
##### 传统命名：
>- 以太网：ethX, [0,oo)，例如eth0, eth1, ...
>  PPP网络：pppX, [0,...], 例如，ppp0, ppp1, ...
>- 可预测命名方案（CentOS）：
>  支持多种不同的命名机制：
>  Fireware, 拓扑结构

(1) 如果Firmware或BIOS为主板上集成的设备提供的索引信息可用，则根据此索引进行命名，如eno1, eno2, ...
(2) 如果Firmware或BIOS为PCI-E扩展槽所提供的索引信息可用，且可预测，则根据此索引进行命名，如ens1, ens2, ...
(3) 如果硬件接口的物理位置信息可用，则根据此信息命名，如enp2s0, ...
(4) 如果用户显式定义，也可根据MAC地址命名，例如enx122161ab2e10, ...

上述均不可用，则仍使用传统方式命名；

##### 命名格式的组成：
>en：ethernet
>wl：wlan
>ww：wwan

##### 名称类型：
>o<index>：集成设备的设备索引号；
>s<slot>：扩展槽的索引号；
>x<MAC>：基于MAC地址的命名；
>p<bus>s<slot>：基于总线及槽的拓扑结构进行命名；

**练习：100.0.0.16/28 对应网段的网关地址、广播地址、可分配IP地址范围**
网关地址：100.0.0.16
广播地址：100.0.0.31
可分配IP地址范围：100.0.0.17 - 100.0.0.30