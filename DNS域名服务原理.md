### DNS
DNS( Domain Name System)是“域名系统”的英文缩写，应用层协议，C/S架构模式，是一种组织成域层次结构的计算机和网络服务命名系统，它用于TCP/IP网络，它所提供的服务是用来将主机名和域名转换为IP地址的工作。

#### DNS域名
域名系统作为一个层次结构和分布式数据库，包含各种类型的数据，包括主机名和域名。DNS数据库中的名称形成一个分层树状结构称为域命名空间。域名包含单个标签分隔点，例如：www.baidu.com
完全限定的域名 (FQDN) 唯一地标识在 DNS 分层树中的主机的位置，通过指定的路径中点分隔从根引用的主机的名称列表。主机的 FQDN 是 www.baidu.com. **一个域名可以对应多个ip，一个ip也可以对应多个域名**
			
![](https://upload-images.jianshu.io/upload_images/16547068-d38044d468d0e657.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### DNS域名称空间的组织方式		

| 名称类型 |                             说明                             |              示例               |
| :------: | :----------------------------------------------------------: | :-----------------------------: |
|   根域   | DNS域名中使用时，规定由尾部句点(.)来指定名称位于根或更高级别的域层次结构 | 单个句点(.)或句点用于末尾的名称 |
|  顶级域  |      用来指示，某个国家/地区或组织使用的名称的类型名称       |              .com               |
|  二级域  |          个人或组织在Internet上使用的名称的类型名称          |            baidu.com            |
|   子域   |        已注册的二级域名派生的域名，通俗的讲就是网站名        |          www.baidu.com          |
|  主机名  | 通常情况下，DNS域名的最左侧的标签标识网络上的特定计算机，如h1 |        h1.www.baidu.com         |


### DNS服务器
**DNS查询类型：**
- 递归查询
- 迭代查询



![](https://upload-images.jianshu.io/upload_images/16547068-aae1d630108e5a51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
			
**DNS名称解析方式：**

>名称 --> IP：正向解析
>IP --> 名称：反向解析
>			
>注意：二者的名称空间，非为同一个空间，即非为同一棵树；因此，也不是同一个解析库；



![](https://upload-images.jianshu.io/upload_images/16547068-dec14574879dd6ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
			

##### DNS服务器类型：
- 负责解析至少一个域：
- 主名称服务器
  辅助名称服务器
- 不负责哉解析：缓存名称服务器
  			

![](https://upload-images.jianshu.io/upload_images/16547068-9c6c808ed766a995.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



**一次完整的查询请求经过的流程：**

>Client --> hosts文件 --> DNS Local Cache --> DNS  Server (recursion) 递归请求 
>-->自己负责解析的域：直接查询数据库并返回答案
>-->不是自己负责解析域：Server Cache --> iteration(迭代)

**DNS服务器返回的解析答案：**
根据是否查询到答案
- 肯定答案：有结果
- 否定答案：没有查询对应的映射关系，否定答案也会被缓存下来，该缓存的生命周期（TTL：Time to Live）由服务器中的数据库定义

根据由何种服务器返回的结果				
- 权威答案：由直接负责的DNS服务器返回的答案
- 非权威答案：其他缓存服务器返回的结果

#### 主-辅DNS服务器：
- 主DNS服务器：维护所负责解析的域数据库的那台服务器；**读写**操作均可进行；
- 从DNS服务器：从主DNS服务器那里或其它的从DNS服务器那里“复制”一份解析库；只能进行**读**操作；

**“复制”操作的实施方式：**

|          方式           |                             作用                             |
| :---------------------: | :----------------------------------------------------------: |
|    序列号（serial）     | 表示配置版本的序列号，通常情况下，序列号sn遵循“年+月+日+编号”的格式，在修改了区域文件后需要手动修改序列号； |
| 刷新时间间隔（refresh） |          从服务器每多久到主服务器检查序列号更新状况          |
|  重试时间间隔（retry）  | 从服务器从主服务器请求同步解析库失败时，再次发起尝试请求的时间间隔 |
|   过期时长（expire）    | 从服务器始终联系不到主服务器时，多久之后放弃从主服务器同步数据；停止提供服务 |
|         minimum         |                  高速缓存否定回答的存活时间                  |

**主服务器”通知“从服务器随时更新数据**
				
##### 区域传送：
- 全量传送：axfr, 传送整个数据库；
- 增量传送：ixfr, 仅传送变量的数据；
  				
>区域(zone)：物理概念
>域(domain)：逻辑概念
>如：magedu.com是一个域，它由一个正向解析库区域和一个反向解析库区域组成


#### 区域数据库文件：
数据库中存放数据，称为资源记录RR（Resource Record）

**记录类型：**

|               记录类型                |                         作用                          |
| :-----------------------------------: | :---------------------------------------------------: |
| SOA（Start Of Authority）起始授权记录 | 个区域解析库有且只能有一个SOA记录，而且必须放在第一条 |
|    NS（Name Service）域名服务记录     |    一个区域解析库可以有多个NS记录；其中一个为主的     |
|         A（ Address）地址记录         |                  FQDN --> IPv4，解析                  |
|       AAAA（ Address）地址记录        |                  FQDN --> IPv6，解析                  |
|    CNAME（Canonical Name）别名记录    |                                                       |
|          PTR（Pointer）指针           |                   IP --> FQDN，解析                   |
|    MX（Mail eXchanger）邮件交换器     |       用于指出某个DNS区域中的邮件服务器的主机名       |


##### 资源记录的定义格式：
**语法：`name [TTL] IN RR_TYPE value`**
			
###### SOA：
name: 当前区域的名字；例如”mageud.com.”，或者“2.3.4.in-addr.arpa.”；
value：有多部分组成
- 当前区域的区域名称（也可以使用主DNS服务器名称）；
- 当前区域管理员的邮箱地址；但地址中不能使用@符号，一般使用点号来替代；
- (主从服务协调属性的定义以及否定答案的TTL)
  					

**例如：**

```
magedu.com. 	 86400 	        IN 	        SOA 	 magedu.com. 	admin.magedu.com.  (
                 2017010801	    ; serial
                 2H 			; refresh
                 10M 		    ; retry
                 1W			    ; expire
                 1D			    ; negative answer ttl 
)	
```
>201701080：序列号，不超过十位
>2H：refresh刷新时间
>10M：retry重试时间
>1W：expire过期时间
>1D：negative answer ttl否定答案生命周期


###### NS：
- name: 当前区域的区域名称
- value：当前区域的某DNS服务器的名字，例如ns.magedu.com.
  注意：一个区域可以有多个ns记录； 
  				

**例如：**

```
magedu.com. 	86400 	IN 	NS  	ns1.magedu.com.
magedu.com. 	86400 	IN 	NS  	ns2.magedu.com.
```

###### MX：
- name: 当前区域的区域名称
- value：当前区域某邮件交换器的主机名

注意：MX记录可以有多个；但每个记录的value之前应该有一个数字表示其优先级；
优先级：0-99，数字越小优先级越高；				


**例如：**

```
magedu.com. 		IN 	MX 	10  	mx1.magedu.com.
magedu.com. 		IN 	MX 	20  	mx2.magedu.com.
```

###### A：
- name：某FQDN，例如www.magedu.com.
- value：某IPv4地址
  			

**例如：**

```
www.magedu.com.		IN 	A	1.1.1.1
www.magedu.com.		IN 	A	1.1.1.2
bbs.magedu.com.		IN 	A	1.1.1.1
```

###### AAAA：
- name：FQDN
- value: IPv6
  		
###### PTR：
- name：IP地址，有特定格式，IP反过来写，而且加特定后缀；例如1.2.3.4的记录应该写为4.3.2.1.in-addr.arpa.
- value：FQND
  			

**例如：**

```
4.3.2.1.in-addr.arpa.  	IN   PTR	www.magedu.com.
```

###### CNAME：
- name：FQDN格式的别名
- value：FQDN格式的正式名字
  			

**例如：**

```
web.magedu.com.  	IN  	CNAME  www.magedu.com.
```


###### 注意：
>(1) TTL可以从全局继承；
>(2) @表示当前区域的名称；
>(3) 相邻的两条记录其name相同时，后面的可省略；
>(4) 对于正向区域来说，各MX，NS等类型的记录的value为FQDN，此FQDN应该有一个A记录；
