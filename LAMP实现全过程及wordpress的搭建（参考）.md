### centos 6 编译安装LAMP，php基于FPM模式的应用WordPress
#### 准备安装包+依赖包
`mkdir /root/src` 准备个目录放包

>① 软件包
>apr-1.6.2.tar.gz
>apr-util-1.6.0.tar.gz
>httpd-2.4.28.tar.bz2
>mariadb-5.5.57-linux-x86_64.tar.gz
>php-5.6.31.tar.xz
>wordpress-4.8.1-zh_CN.tar.gz
>xcache-3.2.0.tar.gz

![](https://upload-images.jianshu.io/upload_images/16547068-80b2beca2a177c9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>② 所依赖的相关包

>openssl-devel expat-devel pcre-devel 　　http所依赖的

>bzip2-devel libxml2-devel libmcrypt-devel 　　php所依赖的，注意：需epel扩展源


#### 编译httpd-2.4
① 解包解压缩，把3个都放到统一目录httpd-2.4.28下

```
tar xvf apr-1.6.2.tar.gz
tar xvf apr-util-1.6.0.tar.gz
tar xvf httpd-2.4.28.tar.bz2
mv apr-1.6.2 httpd-2.4.28/srclib/apr
mv apr-util-1.6.0 httpd-2.4.28/srclib/apr-util
```

② 执行脚本

```
cd /root/src/httpd-2.4.28
./configure --prefix=/app/httpd24 \
--enable-so \
--enable-ssl \
--enable-cgi \
--enable-rewrite \
--with-zlib \
--with-pcre \
--with-included-apr \
--enable-modules=most \
--enable-mpms-shared=all \
--with-mpm=prefork
```

③ 并行、多线程编译安装

```
make -j 4 && make install
```

#### 编译安装http后的设置
① 修改PATH路径，因为是编译安装

```
vim /etc/profile.d/lamp.sh
PATH=/app/httpd24/bin:/usr/local/mysql/bin/:/app/php/bin/:$PATH 顺便把后边的mysql和php的也设置进去
. /etc/profile.d/lamp.sh 　　让设置生效
```

② 设置开机自启

```
cp /etc/init.d/httpd /etc/init.d/httpd24   拷个服务脚本，没有的话去其他机器拷一个
vim /etc/init.d/httpd24  修改路径
chkconfig --add httpd24  设置开机启动，哪个级别
service httpd24 start  开启服务
```

![](https://upload-images.jianshu.io/upload_images/16547068-2702b354007c6937.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 二进制安装mariadb-5.5.57

##### 解包解压缩

`tar xvf mariadb-5.5.57-linux-x86_64.tar.gz  -C /usr/local/`

##### 创建mysql用户

① 加硬盘，创建逻辑卷，存放数据库
以为要存放数据库，最好空间越大越好，这里用一个硬盘作为逻辑卷，不够可以加空间

`echo '- - -' > /sys/class/scsi_host/host2/scan `   同步硬盘，虚拟机才能用，host2不行就host0

② 创建逻辑卷

```
pvcreate /dev/sdb  创建pv
vgcreate vg_mysqldb /dev/sdb  创建vg
lvcreate -n lv_mysqldb -l +100%FREE vg_mysqldb  创建lv
mkfs.ext4 /dev/vg_mysqldb/lv_mysqldb -L /data/mysqldb 　文件系统格式化
```

③ 挂载

```
mkdir /data/mysqldb -p 　　创建挂载点，就是数据库存放的地方
vim /etc/fstab 　　设置开机自动挂载
/dev/vg_mysqldb/lv_mysqldb /data/mysqldb ext4 defaults,acl 0 0
mount -a   挂载
```

④ 创建用户
```
useradd -d /data/mysqldb -r -m -s /sbin/nologin mysql
chown mysql /data/mysqldb/
```

##### cd /usr/local/ 发现mariadb的目录名字不符合要求

`ln -s mariadb-5.5.57-linux-x86_64/ mysql` 创建软连接也可以改名

##### 创建修改配置文件
① 拷贝配置文件

```
cd /usr/local/mysql/
ls support-files/　　 包里自带的有配置文件，但地方不对，要放在/etc/mysql/my.cnf
mkdir /etc/mysql
cp support-files/my-huge.cnf /etc/mysql/my.cnf
```

② 修改配置文件

```
vim /etc/mysql/my.cnf 修改配置文件
[mysqld]
datadir = /data/mysqldb　　//指定总目录，必须的
innodb_file_per_table = on　　//让每一个表数据库都是一个文件，方便管理
skip_name_resolve = on 　　//忽略名字的反向解析，加快速度
```

##### 执行脚本，创建系统数据库

`cd /usr/local/mysql` 一定要在这个目录下执行脚本，因为脚本写死了
`./scripts/mysql_install_db --user=mysql --datadir=/data/mysqldb` 执行脚本
完成后就会在/app/mysqldb/ 生成mysql系统数据库

##### 把服务脚本复制过去

```
cp support-files/mysql.server /etc/init.d/mysqld
chkconfig --add mysqld 设置服务在哪个运行级别，在哪个运行级别开启服务
chkconfig --list mysqld
service mysqld start 失败，看失败原因：缺少日志文件，日志文件须有读写权限
```

##### 创建日志文件

```
touch /var/log/mysqld.log
chown mysql /var/log/mysqld.log
service mysqld start 开启成功
```

##### 运行安全初始化脚本，同上实验

`mysql_secure_installation`

##### 运行mysql，创建WordPress的数据库和管理员并授权

```
mysql -uroot -palong（自己设的密码）
MariaDB [(none)]> create database blogdb;
MariaDB [(none)]> grant all on blogdb.* to 'wpadm'@'localhost' identified by 'along' ;
```

#### 编译安装php-5.6.31
① 解包 tar xvf
② 执行脚本

```
cd /root/src/php-5.6.31
./configure \
--prefix=/app/php \
--with-mysql=/usr/local/mysql \
--with-openssl \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--enable-mbstring \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--enable-sockets \
--enable-fpm \
--with-mcrypt \
--with-config-file-path=/etc/php/ \
--with-config-file-scan-dir=/etc/php.d \
--with-bz2
```

③ `make -j 4 && make install`
④ 创建并修改php 的配置文件和服务脚本

```
cd /root/src/php-5.6.31 在编译源代码的路径
mkdir /etc/php
cp php.ini-production /etc/php/php.ini 复制生产类型的配置文件
cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm 复制个模板
chmod +x /etc/init.d/php-fpm
```

⑤ 编辑php配置文件

```
cd /app/php/etc 在编译源后的路径
cp /app/php/etc/php-fpm.conf.default /app/php/etc/php-fpm.conf
```

**注意：**在centos 7 中php-fpm.conf.default 和www.conf.default 这两个文件都需要cp，但centos 6把这两个文件合在php-fpm.conf.default 中了

```
vim /app/php/etc/php-fpm.conf 可以不修改，根据自己想要的设置
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 2
pm.max_spare_servers = 5 和pm.start_servers 一致
pid = /app/php/var/run/php-fpm.pid
```
⑥ 开启php-ftm服务

```
service php-fpm start
ss -ntl 开启9000端口
chkconfig --add php-fpm
```

#### 修改http的主配置文件，让其支持php
① 取消两行对模块的注释，默认被注释了

```
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so
```

![](https://upload-images.jianshu.io/upload_images/16547068-bb03de973d0cb0ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

② 修改下面行

```
DirectoryIndexindex.phpindex.html
```

![](https://upload-images.jianshu.io/upload_images/16547068-4a6f39bed5d5faf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

③ 加下面四行

```
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
ProxyRequests Off
ProxyPassMatch ^/(.*\.php)$ fcgi://127.0.0.1:9000/app/httpd24/htdocs/$1
```

#### 布署wordpress
① 解包解压缩
② 把所有东西移到/app/httpd24/htdocs/

```
cd /src/wordpress
rm -rf /app/httpd24/htdocs/*
mv wordpress/* /app/httpd24/htdocs/
```

③ 准备WordPress的配置文件 wp-config.php

```
cd /app/httpd24/htdocs
cp wp-config-sample.php wp-config.php
vim wp-config.php 修改4行
define('DB_NAME', 'blogdb');
define('DB_USER', 'wpadm');
define('DB_PASSWORD', 'along');
define('DB_HOST', 'localhost');
```

④ 网页打开，设置，成功
![](https://upload-images.jianshu.io/upload_images/16547068-69467c7aa5530dab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

⑤ ab 测试性能
`ab -c 10 -n 100 http://192.168.30.111/`

#### 编译安装xcache
① 解包
`tar xvf xcache-3.2.0.tar.gz`

② 执行脚本
```
cd xcache-3.2.0 发现没有configure脚本
yum -y install php-devel
phpize 安装包，执行这个命令，生成configure脚本
./configure --enable-xcache --with-php-config=/app/php/bin/php-config
```
③ 编译安装
`make && make install`

④ 修改配置文件

```
mkdir /etc/php.d/
cp xcache.ini /etc/php.d/
ls /app/php/lib/php/extensions/no-debug-non-zts-20131226/
vim /etc/php.d/xcache.ini 因为是源码编译，xcache不是放在对应路径下，所以需修改
extension = /app/php/lib/php/extensions/no-debug-non-zts-20131226/xcache.so 修改此行
service php-fpm restart
```



![](https://upload-images.jianshu.io/upload_images/16547068-fd0482bb15c889e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 测试

① ab 测试性能
`ab -c 10 -n 100 http://192.168.30.111/ `确实性能提升很大

② 也可用文件测试
/app/httpd24/htdocs

```
vim a.php
<?php
phpinfo() 确认xcache 已加载
?>
能看到xcache模块的信息
```

### centos 7 编译安装LAMP，php基于模块的应用WordPress

#### 准备各个包，软件+相关的包
`mkdir /root/src` 准备个目录放包

>① 软件包
>apr-1.6.2.tar.gz
>apr-util-1.6.0.tar.gz
>httpd-2.4.28.tar.bz2
>mariadb-10.2.9-linux-x86_64.tar.gz
>php-7.1.10.tar.xz
>phpMyAdmin-4.0.10.20-all-languages.zip
>wordpress-4.8.1-zh_CN.tar.gz
>xcache-3.2.0.tar.gz



![](https://upload-images.jianshu.io/upload_images/16547068-91552b70609e1499.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




>② 所依赖的相关包
>`openssl-devel expat-devel pcre-devel` http所依赖的
>`bzip2-devel libxml2-devel libmcrypt-devel` php所依赖的，注意：需epel扩展源
>`yum -y install openssl-devel expat-devel pcre-devel`
>`yum -y install bzip2-devel libxml2-devel libmcrypt-devel`

#### 编译httpd2.4

##### 解包解压缩，3个各自编译安装各自的
>apr-1.6.2.tar.bz2
>apr-util-1.6.0.tar.bz2
>httpd-2.4.28.tar.bz2

##### 安装apr-1.6.2.tar.bz2

```
cd apr-1.6.2
./configure --prefix=/app/apr  执行脚本
make && make install  并行编译安装
```

##### 安装apr-util-1.6.0.tar.bz2

```
cd ../apr-util-1.6.0
./configure --prefix=/app/apr-util --with-apr=/app/apr/
make -j 2 && make install
```

检查是否成功：`ls /app/apr-util/`

##### 编译安装httpd-2.4

```
cd ../httpd-2.4.28
./configure --prefix=/app/httpd24 \
--enable-so \
--enable-ssl \
--enable-cgi \
--enable-rewrite \
--with-zlib \
--with-pcre \
--with-apr=/app/apr/ \
--with-apr-util=/app/apr-util/ \
--enable-modules=most \
--enable-mpms-shared=all \
--with-mpm=prefork 执行脚本
```

##### make -j 4 && make install 并行，多线程编译安装

#### 编译安装http 后的设置

① 修改PATH路径，因为是编译安装

```
vim /etc/profile.d/lamp.sh
PATH=/app/httpd24/bin:/usr/local/mysql/bin/:/app/php/bin/:$PATH 顺便把后边的mysql和php的也设置进去
. /etc/profile.d/lamp.sh 让设置生效
```

② 启动服务
`apachectl` 启动服务
`ss -tnl` 查看端口

#### 二进制安装mariadb-10.2.8
##### 解包解压缩
`tar xvf mariadb-10.2.9-linux-x86_64.tar.gz -C /usr/local/`

##### 创建mysql用户

```
useradd -d /app/mysqldb -r -m -s* /sbin/nologin mysql
chown mysql /app/mysqldb/
```

##### cd /usr/local/ 发现mariadb的目录名字不符合要求
`ln -s mariadb-10.2.9-linux-x86_64/ mysql` 创建软连接也可以改名

##### 创建修改配置文件

① 拷贝配置文件

```
cd /usr/local/mysql/
ls support-files/  包里自带的有配置文件，但地方不对，要放在/etc/mysql/my.cnf
mkdir /etc/mysql
cp support-files/my-huge.cnf /etc/mysql/my.cnf
```

② 修改配置文件

```
vim /etc/mysql/my.cnf 修改配置文件
[mysqld]
datadir = /app/mysqldb //指定总目录，必须的
innodb_file_per_table = on //让每一个表数据库都是一个文件，方便管理
skip_name_resolve = on //忽略名字的反向解析，加快速度
```

##### 执行脚本，创建系统数据库

```
cd /usr/local/mysql 一定要在这个目录下执行脚本，因为脚本写死了
./scripts/mysql_install_db **--user=**mysql** --datadir=**/app/mysqldb 执行脚本
```

完成后就会在/app/mysqldb/ 生成mysql系统数据库

##### 把服务脚本复制过去

```
cp support-files/mysql.server /etc/init.d/mysqld
chkconfig --add mysqld 设置服务在哪个运行级别，在哪个运行级别开启服务
chkconfig --list mysqld
service mysqld start 失败，看失败原因：缺少日志文件，日志文件须有读写权限
```



![](https://upload-images.jianshu.io/upload_images/16547068-cc88a81ee9de3a70.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### 创建日志文件

```
mkdir /var/log/mariadb/
touch /var/log/mariadb/mariadb.log
chown mysql /var/log/mariadb/mariadb.log
service mysqld start 开启成功
```

##### 运行安全初始化脚本
`mysql_secure_installation`

##### 运行mysql，创建WordPress的数据库和管理员并授权

```
mysql -uroot -palong（自己设的密码）
MariaDB [(none)]> create database blogdb;
MariaDB [(none)]> grant all on blogdb.* to 'wpadm'@'localhost' identified by 'along' ;
```



![](https://upload-images.jianshu.io/upload_images/16547068-20a9c1364e69c643.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 编译安装php-7.1.10

① 解包 tar xvf

② 执行脚本

```
cd /root/src/php-7.1.10/
./configure \
--prefix=/app/php 
--enable-mysqlnd \
--with-mysqli=mysqlnd \
--with-openssl \
--with-pdo-mysql=mysqlnd \
--enable-mbstring \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--enable-sockets \
--with-apxs2=/app/httpd24/bin/apxs \ （php基于模块方式）
--with-mcrypt \
--with-config-file-path=/etc \
--with-config-file-scan-dir=/etc/php.d \
--enable-maintainer-zts \
--disable-fileinfo
```

③ `make -j 4 && make install`

④ 创建并修改php 的配置文件和服务脚本

```
cp php.ini-production /etc/php.ini
vim /app/httpd24/conf/httpd.conf
```

在文件尾部加两行

```
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
```

修改下面行

```
<IfModule dir_module>
DirectoryIndex index.php index.html
</IfModule>

apachectl stop
apachectl
```

⑤ 测试
测试php和mariadb连接

```
vim /app/httpd24/htdocs/index.php
<?php
$mysqli=new mysqli("localhost","root","along");
if(mysqli_connect_errno()){
echo "连接数据库失败!";
$mysqli=null;
exit;
}

echo "连接数据库成功!";
$mysqli->close();
phpinfo();
?>
```

#### 布署phpmyadmin
注意：不要随便下载最新版，因为有的版本不支持PHP有些版本，例：4.7只支持php7.2

##### rz、解压缩，装包

`unzip phpMyAdmin-4.0.10.20-all-languages.zip`

##### cp -r phpMyAdmin-4.0.10.20-all-languages /app/httpd24/htdocs/pma/ 把文件放到//app/httpd24/htdocs下

网页打开发现确实东西 php-mbstring包



![](https://upload-images.jianshu.io/upload_images/16547068-550a15f281b6e6fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



`yum -y install php-mbstring`
`systemctl restart httpd` 重启服务

##### 在网页上设置数据库
① 登录



![](https://upload-images.jianshu.io/upload_images/16547068-5339fb91ade946ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



② 创建wpdb数据库，一会给WordPress用



![](https://upload-images.jianshu.io/upload_images/16547068-253a350ef6636722.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



③ 创建用户wpuser



![](https://upload-images.jianshu.io/upload_images/16547068-477fc9f0e8919083.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

给他对wpdb数据库的全部权限



![](https://upload-images.jianshu.io/upload_images/16547068-2db89c87ffd175e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



③ 加下面四行
```
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
ProxyRequests Off
ProxyPassMatch ^/(.*\.php)$ fcgi://127.0.0.1:9000/app/httpd24/htdocs/$1
```
#### 布署wordpress
① 解包解压缩
② 把所有东西移到/app/httpd24/htdocs/

```
cd /src/wordpress
mv wordpress/* /app/httpd24/htdocs/
```

③ 准备WordPress的配置文件 wp-config.php

```
cd /app/httpd24/htdocs
cp wp-config-sample.php wp-config.php
vim wp-config.php 修改4行
define('DB_NAME', 'wpdb');
define('DB_USER', 'along');
define('DB_PASSWORD', 'along');
define('DB_HOST', '192.168.30.222');
```
④ 网页打开，设置，成功



![](https://upload-images.jianshu.io/upload_images/16547068-15080fc3589696f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



⑤ ab 测试性能
`ab -c 10 -n 100 http://192.168.30.111/`


本博客参考：https://www.cnblogs.com/along21/p/7753969.html#auto_id_7
