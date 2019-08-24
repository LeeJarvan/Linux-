#### 检查安装环境
>本次安装主机配置
>主机地址：10.130.6.31
>16核，32G内存，50G硬盘+500G存储

由于公司主机均已远超oracle最低安装标准，不再进行配置说明

#### 准备oracle11g安装包
通过其他已安装oracle11g主机上发送安装包到该台主机

```
[root@r0 ~]# scp /data1/oradata/p13390677_112040_Linux-x86-64_1of7.zip /data1/oradata/p13390677_112040_Linux-x86-64_2of7.zip oidd@10.130.6.31:/oradata/
```

#### 开始安装
##### 创建oinstall和dba组

```
[root@r1 ~]# groupadd oinstall
[root@r1 ~]# groupadd dba
```

##### 创建Oracle用户

```
[root@r1 ~]# useradd -g oinstall -G dba oracle
[root@r1 ~]# passwd oracle
...
```

##### 验证创建及所属组是否正确

```
[root@r1 ~]# id oracle
uid=1001(oracle) gid=1001(oinstall) groups=1001(oinstall),1002(dba)
```

##### 配置内核参数

```
[root@r1 ~]# vim /etc/sysctl.conf 
# System default settings live in /usr/lib/sysctl.d/00-system.conf.
# To override those settings, enter new settings here, or in an /etc/sysctl.d/<name>.conf file
#
# For more information, see sysctl.conf(5) and sysctl.d(5).
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 8388608
kernel.shmmax = 7516192768 #7G
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
```

参数的值不能小于上面的配置，因为这是oracle官方建议的最小值，所以生产环境建议调整为这些参数，以优化系统性能。

注意：kernel.shmmax的值，#最低：536870912，最大值：比物理内存小1个字节的值，建议比物理内存小一点点就可以（过小的话后期会导致数据库实例无法启动或无法监听）

###### 修改后使之生效

```
[root@r1 ~]# sysctl -p
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 8388608
sysctl: setting key "kernel.shmmax": Invalid argument
kernel.shmmax = 7516192768 #7G
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
```

##### 修改用户权限

```
[root@r1 ~]# vim  /etc/security/limits.conf
# 末尾添加
# End of file
oracle soft nproc 2047
oracle hard nproc 16384
oracle soft nofile 1024
oracle hard nofile 65536
oracle soft stack 10240
oracle hard stack 10240
```

##### 在/etc/pam.d/login 文件中，使用文本编辑器或vi命令增加或修改以下内容

```
[root@r1 ~]# vim /etc/pam.d/login
session required /lib64/security/pam_limits.so
session required pam_limits.so
```

##### 在/etc/profile 文件中，使用文本编辑器或vi命令增加或修改以下内容

```
[root@r1 ~]# vim /etc/profile
if [ $USER = "oracle" ]; then
   if [ $SHELL = "/bin/ksh" ]; then
       ulimit -p 16384
       ulimit -n 65536
    else
       ulimit -u 16384 -n 65536
   fi
fi

[root@r1 ~]# source /etc/profile
```

##### 创建安装目录

```
[root@r1 ~]# mkdir -p /data/oracle/app/
[root@r1 ~]# chown -R oracle:oinstall /data/oracle/app/
[root@r1 ~]# chmod -R 775 /data/oracle/app/
```

##### 配置环境变量
切换到Oracle用户操作：

```
[root@r1 ~]# su - oracle
[oracle@r1 ~]$ vim ~/.bash_profile
export ORACLE_BASE=/data/app/oracle
export ORACLE_SID=orcl
export ROACLE_PID=ora11g
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/usr/lib
export ORACLE_HOME=/data/app/oracle/product/11.2.0/db_1
export PATH=$PATH:$ORACLE_HOME/bin
export LANG="zh_CN.UTF-8"
export NLS_LANG="SIMPLIFIED CHINESE_CHINA.AL32UTF8"
export NLS_DATE_FORMAT='yyyy-mm-dd hh24:mi:ss'

[oracle@r1 ~]$ source ~/.bash_profile
```

##### 在root用户下解压安装包

```
[root@r1]# unzip linux.x64_11gR2_database_1of2.zip
[root@r1]# unzip linux.x64_11gR2_database_2of2.zip
[root@r1]# cd /data1/oradata/
[root@r1 oradata]# ll
总用量 2487200
drwxrwxr-x 7 oracle oinstall        128 8月  27 2013 database
-rw------- 1 oidd   oidd     1395582860 8月   8 17:02 p13390677_112040_Linux-x86-64_1of7.zip
-rw------- 1 oidd   oidd     1151304589 8月   8 17:02 p13390677_112040_Linux-x86-64_2of7.zip
```

##### 切换到Oracle用户，复制响应文件模板

```
[oracle@r1 ~]$ mkdir /home/oracle/etc/
[oracle@r1 ~]$ cp  /usr/local/src/database/response/* /home/oracle/etc/
[oracle@r1 etc]$ ll
总用量 80
-rwxr-xr-x 1 oracle oinstall 44533 8月  13 09:37 dbca.rsp
-rwxr-xr-x 1 oracle oinstall 25304 8月  13 10:18 db_install.rsp
-rwxr-xr-x 1 oracle oinstall  5871 8月  12 16:10 netca.rsp
```

##### 设置响应文件权限

```
[oracle@r1 ~]$ su - root
...
[root@r1 ~]# chmod 700 /home/oracle/etc/*.rsp
```

##### 切换到oracle用户，修改安装Oracle软件的响应文件/home/oracle/etc/db_install.rsp

```
[root@r1 ~]# su - oracle
[oracle@r1 ~]$vi /home/oracle/etc/db_install.rsp
oracle.install.option=INSTALL_DB_SWONLY     // 安装类型
ORACLE_HOSTNAME=r1        // 主机名称（hostname查询）
UNIX_GROUP_NAME=oinstall     // 安装组
INVENTORY_LOCATION=/data/app/oracle/oraInventory   //INVENTORY目录（不填就是默认值）
SELECTED_LANGUAGES=en,zh_CN,zh_TW // 选择语言
ORACLE_HOME=/data/app/oracle/product/11.2.0/db_1    //oracle_home
ORACLE_BASE=/data/app/oracle     //oracle_base
oracle.install.db.InstallEdition=EE 　　　　// oracle版本
oracle.install.db.EEOptionsSelection=false 　　//自定义安装，否，使用默认组件
oracle.install.db.DBA_GROUP=dba /　　/ dba用户组
oracle.install.db.OPER_GROUP=oinstall // oper用户组
oracle.install.db.config.starterdb.type=GENERAL_PURPOSE //数据库类型
oracle.install.db.config.starterdb.globalDBName=orcl //globalDBName
oracle.install.db.config.starterdb.SID=orcl      //SID
oracle.install.db.config.starterdb.memoryLimit=81920 //自动管理内存的内存(M)
oracle.install.db.config.starterdb.password.ALL=oracle //设定所有数据库用户使用同一个密码
SECURITY_UPDATES_VIA_MYORACLESUPPORT=false         //（手动写了false）
DECLINE_SECURITY_UPDATES=true 　　//设置安全更新（貌似是有bug，这个一定要选true，否则会无限提醒邮件地址有问题，终止安装。PS：不管地址对不对）
```

##### 开始静默安装
```
[oracle@r1 database]$ ./runInstaller -silent -responseFile /home/oracle/etc/db_install.rsp
```

当出现`Successfully Setup Software`即为成功

##### 执行脚本
切换到root用户，如果出现切换失败，拒绝权限，可以直接exit

```
[oracle@r1 database]$su root
... 
[root@r1 ~]# cd /data/app/oracle/oraInventory/
[root@r1 oraInventory]# ls
ContentsXML  logs  oraInst.loc  orainstRoot.sh  oui
[root@r1 oraInventory]# ./orainstRoot.sh
[root@r1 ~]# cd /data/app/oracle/product/11.2.0/db_1/
[root@r1 db_1]# ls
apex         css          emcli          jdbc  network      oui       scheduler        uix
assistants   ctx          EMStage        jdev  nls          owb       slax             usm
bin          cv           has            jdk   oc4j         owm       sqldeveloper     utl
ccr          dbs          hs             jlib  odbc         perl      sqlj             wwg
cdata        dc_ocm       ide            ldap  olap         plsql     sqlplus          xdk
cfgtoollogs  deinstall    install        lib   OPatch       precomp   srvm
clone        demo         instantclient  log   opmn         racg      suptools
config       diag         inventory      md    oracore      rdbms     sysman
crs          diagnostics  j2ee           mesg  oraInst.loc  relnotes  timingframework
csmig        dv           javavm         mgw   ord          root.sh   ucp
[root@r1 db_1]# ./root.sh
```

若没有生成orainstRoot.sh这个文件，解决办法：删除/etc/oraInst.loc文件，再次重新静默安装，记住要删除之前安装后生成的文件，不然会出现磁盘容量溢出

##### 配置监听程序

```
[oracle@r1 ~]$ netca /silent /responsefile /home/oracle/etc/netca.rsp
Parsing command line arguments:
Parameter "silent" = true
Parameter "responsefile" = /home/oracle/etc/netca.rsp
Done parsing command line arguments.
Oracle Net Services Configuration:
Profile configuration complete.
Oracle Net Listener Startup:
Running Listener Control: 
/data/app/oracle/product/11.2.0/db_1/bin/lsnrctl start LISTENER
Listener Control complete.
Listener started successfully.
Listener configuration complete.
Oracle Net Services configuration successful. The exit code is 0
```

##### 修改监听配置文件

```
[oracle@r1 ~]$ cd /data/app/oracle/product/11.2.0/db_1/network/admin/
[oracle@r1 admin]$ ls
diag                          samples                     sqlnet19081311上午1634.bak
listener1908125PM1519.bak     shrept.lst                  sqlnet.ora
listener19081311上午1533.bak  sqlnet19081311上午1515.bak  tnsnames.ora
listener.ora          sqlnet19081311上午1533.bak  tnsnames.ora_2019081221
[oracle@r1 admin]$ vi listener.ora
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = orcl)
      (ORACLE_HOME = /data/app/oracle/product/11.2.0/db_1)
    )
  )


LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.130.6.31)(PORT = 1521))
    )
  )
```

##### 启动监听程序

```
[oracle@OIDDCDR11 bin]$ lsnrctl start
LSNRCTL for Linux: Version 11.2.0.4.0 - Production on 14-8月 -2019 15:03:53

Copyright (c) 1991, 2013, Oracle.  All rights reserved.
Starting /u01/app/oracle/product/11.2.0/db_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 11.2.0.1.0 - Production
System parameter file is /u01/app/oracle/product/11.2.0/db_1/network/admin/listener.ora
Log messages written to /u01/app/oracle/diag/tnslsnr/docker/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=docker)(PORT=1521)))

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1521)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 11.2.0.1.0 - Production
Start Date                01-SEP-2016 11:23:31
Uptime                    0 days 0 hr. 0 min. 0 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /data/app/oracle/product/11.2.0/db_1/network/admin/listener.ora
Listener Log File         /data/app/oracle/diag/tnslsnr/docker/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=r1)(PORT=1521)))
The listener supports no services
The command completed successfully
```

#### 建库

```
[oracle@r1 ~]$ dbca -silent -responseFile etc/dbca.rsp
Enter SYS user password: 
  
Enter SYSTEM user password: 
 
sh: /bin/ksh: No such file or directory
sh: /bin/ksh: No such file or directory
Copying database files
1% complete
3% complete
11% complete
18% complete
26% complete
37% complete
Creating and starting Oracle instance
40% complete
45% complete
50% complete
55% complete
56% complete
57% complete
60% complete
62% complete
Completing Database Creation
66% complete
70% complete
73% complete
74% complete
85% complete
96% complete
100% complete
Look at the log file Look at the log file "/data/app/oracle/cfgtoollogs/dbca/orcl11g/orcl11g.log" for further details.
```

##### 启动数据库

```
[oracle@r1 ~]$ ./sqlplus /nolog
SQL> connect /as sysdba
Connected.
SQL> startup
...
```

##### 创建用户并授权

```
SQL>create user OIDD2 identified by password;
SQL> grant dba to OIDD2;
```

##### SecureCRT设置转发端口并登录

![](https://upload-images.jianshu.io/upload_images/16547068-867e3fc4db8aaadc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16547068-744795ec35382d5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)