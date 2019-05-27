## CentOS7搭建php-fpm工作方式的LAMP环境，实现wordpress访问
### LAMP 分层架构图
![](https://upload-images.jianshu.io/upload_images/16547068-b690d75844e1cc97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![LAMP环境逻辑图](https://upload-images.jianshu.io/upload_images/16547068-5fd845e9798120f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 搭建LAMP
#### 搭建mysql服务

##### 安装mysql-server程序包（通过mariadb官网提供的yum源安装）

```
[root@promote ~]# vim /etc/yum.repos.d/MariaDB.repo
# MariaDB 10.3 CentOS repository list - created 2019-05-26 05:08 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.3/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1

[root@promote ~]# yum install MariaDB-server -y
```

##### 编辑/etc/my.cnf.d/server.cnf常用参数
```
[root@promote ~]# vim /etc/my.cnf.d/server.cnf
[mysqld]
skip_name_resolve=ON
innodb_file_per_table=ON
```

##### 启动服务并检查端口连接情况
```
[root@centos7 ~]# systemctl start mariadb.service
[root@centos7 ~]# systemctl enable mariadb  设置开机自动启动
Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
[root@centos7 ~]# ss -tnl  检查端口3306是否开启
State      Recv-Q Send-Q             Local Address:Port               PeerAddress:Port              

LISTEN     0      128                            *:111                         *:*                  
LISTEN     0      5                  192.168.122.1:53                          *:*                     
LISTEN     0      80                            :::3306                        :::*     
··· ···
```

##### 安全加强，执行`mysql_secure_installation `

```
[root@centos7 ~]# mysql_secure_installation 

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] n
 ... skipping.

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

##### 授权一个普通用户

```
[root@centos7 ~]# mysql -uroot -h127.0.0.1 -pcentos
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 15
Server version: 10.3.15-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> GRANT ALL ON testdb.* TO 'myuser'@'192.168.%.%' IDENTIFIED BY "mageedu";
Query OK, 0 rows affected (0.002 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;  刷新授权表
Query OK, 0 rows affected (0.002 sec)

[root@centos7 ~]# mysql -umyuser -h192.168.0.102 -pmageedu
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 16
Server version: 10.3.15-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> CREATE DATABASE testdb CHARACTER SET 'utf8';  创建数据库，指定默认字符集utf8
```

#### php-fpm服务器的搭建
##### 安装程序包

```
[root@centos7 ~]# yum install -y php-fpm php-mysql php-mbstring php-mcrypt
```

如果找不到php-mcrypt包，需先安装epel-release：

```
[root@centos7 ~]# yum install epel-release
```

>服务配置文件：/etc/php-fpm.conf，/etc/php-fpm.d/*.conf
>php环境配置文件：/etc/php.ini，/etc/php.d/\*.ini


##### 编辑/etc/php-fpm.d/www.conf文件

```
[root@centos7 ~]# vim /etc/php-fpm.d/www.conf
listen = 192.168.0.109:9000  #修改监听的端口和IP
listen.backlog = -1  #后援队列，指最大的等待队列，-1表示无限制；
listen.allowed_clients = 192.168.0.118  #指定允许哪些IP能访问此服务，此处允许httpd服务器访问
user = apache  #运行进程的用户
group = apache  #运行进程的用户组
pm = dynamic   #指定fpm的运行模式
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 35
pm.max_requests = 500
pm.status_path = /pmstatus
ping.response = pong
ping.path = /ping
php_value[session.save_handler] = files
php_value[session.save_path] = /var/lib/php/session  #此目录不存在，需手动创建，并将属主属组指定为httpd的运行用户
```

##### 创建指定目录并启动服务

```
[root@centos7 ~]# mkdir -pv /var/lib/php/session
mkdir: created directory ‘/var/lib/php/session’
[root@centos7 ~]# chown apache:apache /var/lib/php/session/
[root@centos7 ~]# systemctl start php-fpm.service
ss -tnl | grep 9000
LISTEN     0      128    192.168.0.109:9000                     *:*                  
[root@centos7 ~]# ps aux | grep fpm
root      23953  0.4  1.0 335604 10604 ?        Ss   17:18   0:00 php-fpm: master process (/etc/php-fpm.conf)
apache    23955  0.0  0.4 335604  4732 ?        S    17:18   0:00 php-fpm: pool www
apache    23956  0.0  0.4 335604  4732 ?        S    17:18   0:00 php-fpm: pool www
apache    23957  0.0  0.4 335604  4736 ?        S    17:18   0:00 php-fpm: pool www
apache    23958  0.0  0.4 335604  4736 ?        S    17:18   0:00 php-fpm: pool www
apache    23959  0.0  0.4 335604  4736 ?        S    17:18   0:00 php-fpm: pool www
root      23968  0.0  0.0 112660   976 pts/0    R+   17:19   0:00 grep --color=auto fpm                  *:*        
```

#### 搭建http服务
##### 安装httpd

```
[root@centos7 ~]# yum install httpd -y
```

安装完成后，确认是否加载了模块proxy_fcgi_module

```
[root@centos7 ~]#  httpd -M |grep fcgi
 proxy_fcgi_module (shared)
```

##### 编辑创建/etc/httpd/conf.d/fcgi.conf配置文件

```
[root@centos7 ~]# vim /etc/httpd/conf.d/fcgi.conf
DirectoryIndex index.php  #设置默认主页为index.php
ProxyRequests off  #关闭正向代理
#将以.php结尾的URL代理转发给fcgi://192.168.109:9000
ProxyPassMatch ^/(.*\.php)$ fcgi://192.168.109:9000/var/www/html/$1  
ProxyPassMatch ^/(ping|pmstatus)$ fcgi://192.168.0.109:9000/$1
```

##### 设置虚拟主机

```
[root@centos7 ~]# vim /etc/httpd/conf.d/vhosts.conf
Listen 8080
<VirtualHost *:8080>
        DirectoryIndex index.php
        ServerName www.a.com
        DocumentRoot /data/www/html
        ProxyRequests off
        ProxyPassMatch ^/(.*\.php)$  fcgi://192.168.0.109:9000/var/www/html/$1
        ProxyPassMatch ^/(ping|pmstatus)$ fcgi://192.168.0.109:9000/$1
        <Directory "/data/www/html">
                options none
                Allowoverride None
                Require all granted
        </Directory>
</VirtualHost>
```

##### 在php-fpm服务器上创建编辑index.php 和mysql.php进行测试

```
#首先创建对应的存放目录，此处设置与httpd服务上设置的fcgi://192.168.109:9000/var/www/html/$相一致
[root@fpm ~]# mkdir -pv /var/www/html/    

[root@centos7 ~]# vim /var/www/html/index.php  #
<?php
        phpinfo();
?>
[root@centos7 ~]# vim /var/www/html/mysql.php
<?php
        $conn = mysql_connect('192.168.0.188','test','magedu');
        if ($conn)
                echo "Connected to mysql.";
        else
                echo "Fail";
?>
```


##### 启动httpd服务并访问相应的页面

```
[root@localhost ~]# systemctl start httpd  
[root@localhost ~]# systemctl stop firewalld  #停止firewalld
[root@localhost ~]# systemctl disable firewalld
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.  
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
[root@localhost ~]# setenforce 0  #设置selinux为permissive
```



最后测试访问相应的页面：



![红框中的信息说明网页是以php-fpm的方式工作的](https://upload-images.jianshu.io/upload_images/16547068-fe8457ee11fc902c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)



![数据可测试成功](https://upload-images.jianshu.io/upload_images/16547068-0a8f5ac7ae59b9ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![访问pmststus页面](https://upload-images.jianshu.io/upload_images/16547068-05b63343d0b27a5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![访问ping页面](https://upload-images.jianshu.io/upload_images/16547068-9f2475038ba11650.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### wordpress搭建

在链接：[https://cn.wordpress.org/](https://link.jianshu.com/?t=https%3A%2F%2Fcn.wordpress.org%2F) 找到相应的wordpress下载链接，并通过wget命令下载到本地服务器主机并解压缩

```
[root@localhost ~]# wget https://cn.wordpress.org/wordpress-4.9.4-zh_CN.tar.gz
[root@localhost ~]# tar xf wordpress-4.9.4-zh_CN.tar.gz
[root@localhost ~]# ll -d wordpress*
drwxr-xr-x. 5 nobody 65534    4096 Feb  7 23:53 wordpress
```

随后复制解压缩的wordpress目录到/var/www/html目录下：

```
[root@localhost ~]# cp wordpress /var/www/html/
```

此时通过访问URL：http://IP-ADDRESS/wordpress即可进行到wordpress的配置页面：



![初始页面](https://upload-images.jianshu.io/upload_images/16547068-2f8cced58acec9fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



此时需要为wordpress的搭建提供相应的数据库账号及建立相应的数据库：

```
[root@localhost ~]# mysql -uroot
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
MariaDB [(none)]> create database wordpress;  #创建wordpress数据库
Query OK, 1 row affected (0.01 sec)
#创建wordpressuser作为wordpress访问数据库的账号
MariaDB [(none)]> GRANT ALL ON wordpress.* TO 'wordpressuser'@'192.168.%.%' IDENTIFIED BY "magedu"; 
Query OK, 0 rows affected (0.00 sec)
```

接着继续wordpress的初始化操作：



![image.png](https://upload-images.jianshu.io/upload_images/16547068-a129ea8661caa578.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![页面提示需手动创建wp-config.php文件](https://upload-images.jianshu.io/upload_images/16547068-7b8fa5f3c8f74f9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



用户也可以通过参考/var/www/html/wordpress/wp-config-sample.php文件来在命令行创建对应的wp-config.php文件


**手动创建wp-config.php文件**

```
[root@localhost ~]# vim /var/www/html/wordpress/wp-config.php
<?php
/**
 * WordPress基础配置文件。
 *
 * 这个文件被安装程序用于自动生成wp-config.php配置文件，
 * 您可以不使用网站，您需要手动复制这个文件，
 * 并重命名为“wp-config.php”，然后填入相关信息。
 *
 * 本文件包含以下配置选项：
 *
 * * MySQL设置
 * * 密钥
 * * 数据库表名前缀
 * * ABSPATH
 *
 * @link https://codex.wordpress.org/zh-cn:%E7%BC%96%E8%BE%91_wp-config.php
 *
 * @package WordPress
 */

// ** MySQL 设置 - 具体信息来自您正在使用的主机 ** //
/** WordPress数据库的名称 */
define('DB_NAME', 'wordpress');

/** MySQL数据库用户名 */
define('DB_USER', 'wordpressuser');

/** MySQL数据库密码 */
define('DB_PASSWORD', 'magedu');

/** MySQL主机 */
define('DB_HOST', '192.168.0.118');

/** 创建数据表时默认的文字编码 */
define('DB_CHARSET', 'utf8mb4');

/** 数据库整理类型。如不确定请勿更改 */
define('DB_COLLATE', '');

/**#@+
 * 身份认证密钥与盐。
 *
 * 修改为任意独一无二的字串！
 * 或者直接访问{@link https://api.wordpress.org/secret-key/1.1/salt/
 * WordPress.org密钥生成服务}
 * 任何修改都会导致所有cookies失效，所有用户将必须重新登录。
 *
 * @since 2.6.0
 */
define('AUTH_KEY',         '!;#@OR03j87T7qH/WrceNVH%B~>MR/y><8CbXsVG#@GG:l|<0BYx%VD8.+0*m1BA');
define('SECURE_AUTH_KEY',  'BSrmHuvZOZ}Dce&Kb6PzfLVbbs$sU,SG&4/=L}A#|Kv&{k|c@S8]_5p EUoPG%dn');
define('LOGGED_IN_KEY',    'UUVB~DZseI@mF,wJ~Y(vI^m pa-iz)M+I+CKqZzZ((5Q<_/18tz=teL3*5SNGc=*');
define('NONCE_KEY',        's-|wCdb-lE)hW2B~^<S8cczFb{A-5?Vaxwvz$kzt!KqweJ-dDcbAf:r%|}mZM{<]');
define('AUTH_SALT',        '| NrJ[5KSpfn|)x@XG^.@Nj6r9Rcj*#GadpN~o_E1^4<]>Nhh[|cZSM8ddlBMSTd');
define('SECURE_AUTH_SALT', 'y;n.E#%[7$!qX1;]Q;u@H_s=A+XpJ[i>u*XLT1!b6)b?spS!finRl@#DRZ<XWYV=');
define('LOGGED_IN_SALT',   '%x)I>ZQf)Dwp-!ZeW=}b69f^JLheG;5JGhJNS)t#2YgHf#HX,K:2[D~e3uh,g_(@');
define('NONCE_SALT',       'ZYs]r20xvwor2|3:jgZ@95ZF%eC3P49.V)7{21h9Z?{$eFWFtbN#-(I!y%/*$;%e');

/**#@-*/

/**
 * WordPress数据表前缀。
 *
 * 如果您有在同一数据库内安装多个WordPress的需求，请为每个WordPress设置
 * 不同的数据表前缀。前缀名只能为数字、字母加下划线。
 */
$table_prefix  = 'wp_';

/**
 * 开发者专用：WordPress调试模式。
 *
 * 将这个值改为true，WordPress将显示所有用于开发的提示。
 * 强烈建议插件开发者在开发环境中启用WP_DEBUG。
 *
 * 要获取其他能用于调试的信息，请访问Codex。
 *
 * @link https://codex.wordpress.org/Debugging_in_WordPress
 */
define('WP_DEBUG', false);

/**
 * zh_CN本地化设置：启用ICP备案号显示
 *
 * 可在设置→常规中修改。
 * 如需禁用，请移除或注释掉本行。
 */
define('WP_ZH_CN_ICP_NUM', true);

/* 好了！请不要再继续编辑。请保存本文件。使用愉快！ */

/** WordPress目录的绝对路径。 */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__) . '/');

/** 设置WordPress变量和包含文件。 */
require_once(ABSPATH . 'wp-settings.php');
```

最后设置相关的wordpress信息后点击安装wordpress即可




![](https://upload-images.jianshu.io/upload_images/16547068-3dee427bdb9aa00f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)