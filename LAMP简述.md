### LAMP：
>a: apache (httpd)
>m: mysql, mariadb
>p: php, perl, python

**WEB资源类型：**
静态资源：原始形式与响应内容一致；
动态资源：原始形式通常为程序文件，需要在服务器端执行之后，将执行结果返回给客户端；

**WEB相关语言：**		
客户端技术： javascript
服务器端技术：php, jsp
			
**CGI：Common Gateway Interface**
>可以让一个客户端，从网页浏览器向执行在网络服务器上的程序传输数据；
>CGI描述了客户端和服务器程序之间传输的一种标准；

#### LAMP工作原理	



![](https://upload-images.jianshu.io/upload_images/16547068-e2025be2d23b4e29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
		
	

#### php
>脚本编程语言、嵌入到html中的嵌入式web程序开发语言；
>基于zend编译成opcode（二进制格式的字节码，重复运行，可省略编译环境）

**配置文件：/etc/php.ini,  /etc/php.d/\*.ini** 
			
配置文件在php解释器启动时被读取，因此，对配置文件的修改如何生效？
Modules：重启httpd服务；
FastCGI：重启php-fpm服务；
			
**http与php结合的方式：**
- FastCGI 
- modules (把php编译成为httpd的模块)					

```
vim /etc/php.ini
[foo]：Section Header
directive = value
```
>注释符：较新的版本中，已经完全使用;进行注释；
>\#：纯粹的注释信息
>;：用于注释可启用的directive

php.ini的核心配置选项文档：  http://php.net/manual/zh/ini.core.php
php.ini配置选项列表：http://php.net/manual/zh/ini.list.php

```				
<?php 
	...php code...
?>
```


#### 安装lamp：
##### 安装httpd+php

>CentOS 6: `yum install httpd php mysql-server php-mysql`
>
>启动服务：
>`service httpd  start`
>`service  mysqld  start`

>CentOS 7: `yum install httpd php php-mysql mariadb-server`
>
>启动服务：
>`systemctl  start  httpd.service`
>`systemctl  start  mariadb.service`


##### 配置MySQL
MySQL的命令行客户端程序：mysql
					
支持SQL语句对数据管理：
DDL： CREATE， ALTER， DROP， SHOW
DML： INSERT（增）， DELETE（删），SELECT（查）， UPDATE（改）
							
授权能远程的连接用户：
`mysql> GRANT  ALL  PRIVILEGES  ON  db_name.tbl_name TO  username@host  IDENTIFIED BY 'password'; `
						

​					

​		

​		

​			


​		