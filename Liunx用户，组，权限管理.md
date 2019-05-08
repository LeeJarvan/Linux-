>Linux系统是一个多用户多任务的分时操作系统，任何一个要使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。
>用户的账号一方面可以帮助系统管理员对使用系统的用户进行跟踪，并控制他们对系统资源的访问；另一方面也可以帮助用户组织文件，并为用户提供安全性保护。

>每个用户账号都拥有一个唯一的用户名和各自的口令。
>用户在登录时键入正确的用户名和口令后，就能够进入系统和自己的主目录。

**实现用户账号的管理，要完成的工作主要有如下几个方面：**
* 用户账号的添加、删除与修改。
* 用户口令的管理。
* 用户组的管理。



**3A：Authentication（认证），Authorization（授权），Audition（审计）**

#### 用户标识：userID，UID
>用户类别：
>* root用户：root用户是系统中唯一的超级管理员，它具有等同于操作系统的权限。
>  管理员：0
>* 普通用户：1-65535
>  ①系统用户：1-999（centos7），1-499（centos6）
>  ②登录用户：1000-60000（centos7），500-60000（centos6）

6bits二级制数字：0-65535

>**名称解析：名称转换**
>username <--> UID
>根据名称解析库进行：/etc/passwd


#### 组：用户组，用户容器
组标识：GroupID，GID
>**组类别1：**
>* 管理员组：0
>* 普通用户组：1-65535
>        ①系统组：1-999（centos7），1-499（centos6）
>        ②登录组：1000-60000（centos7），500-60000（centos6）

>**名称解析：名称转换**
>groupname <--> gid
>解析库：/etc/group

>**组类别2：**
>* 用户的基本组
>* 用户的附加组

**组类别3：**
>* 私有组：组名同用户名，且至包含一个用户
>* 公共组：组内可以包含多个用户


认证信息：通过比对事先存储的，与登录时提供的信息是否一致

>password：/etc/shadow
>组password：/etc/gshadow

**密码使用策略：**
*   使用随机密码
*   最短长度不要低于8位
*   应该使用大写字母，小写字母，数字和标点符号四类字符中至少三类
*   定期更换

密码期限：



![](https://upload-images.jianshu.io/upload_images/16547068-240ba80b686af6d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

**加密算法：**

* 对称加密：加密和解密使用同一个密码

* 非对称加密：加密和解密使用的是一对密钥

   

**密钥对：公钥（public key），私钥（private key）**

单向加密：只能加密，不能解密：提取数据特征码

   * 定长输出

   * 雪崩效应

**算法：**

    md5：message disgest，128bits
    
    sha：secure hash algorithm，160bits
    
    sha224，sha256，sha384，sha512
    
    在计算之时加salt，添加的随机数


#### /etc/passwd：用户的信息库
name:password:UID:GID:GECOS:directory:shell
|字符名称|含义|
|:---:|:---:|
|name|用户名|
| password|可以是加密的密码，也可以是占位符x|
|GID|软件的版本|
|GECOS|用户所属主组的ID号|
|directory|用户的家目录|
|shell|用户的默认shell，登录时默认shell程序|

#### /etc/shadow：用户密码

用户名：加密的密码：最近一次修改密码的时间：密码的最短使用权限：密码的最长使用期限：警告期段：过期期限：保留字段

#### /etc/group：组的信息库
group_name:password:GID:user_list
|字符名称|含义|
 |:---:|:---:|
 group_name|组名|
 password|可以是加密的密码，也可以是占位符x|
GID|软件的版本|
|user_list|该用户的用户成员：以此组为附加组的用户的用户列表|



**安全上下文：**
>1、进程以某用户的身份运行； 进程是发起此进程用户的代理，因此以此用户的身份和权限完成所有操作；
>2、权限匹配模型：
>(1) 判断进程的属主，是否为被访问的文件属主；如果是，则应用属主的权限；否则进入第2步；
>(2) 判断进程的属主，是否属于被访问的文件属组；如果是，则应用属组的权限；否则进入第3步；
>(3) 应用other的权限；

#### groupadd：添加组
格式：`group [选项] group_name`
**-g GID：指定GID，默认是上一个组的GID+1**
**-r：创建系统组**

#### groupmod：修改组属性
格式：`groupmod [选项] GROUP`

**-g GID：修改GID**

**-n new_name：修改组名**

#### groupdel：删除组

格式：`groupdel [选项] GROUP`

#### useradd：创建用户

格式：`useradd [选项] 登录名`

**-u，--uid UID：指定UID**

**-g，--gid GROUP：指定基本组ID，此组得事先存在**

**-G，--groups GROUP1[,GROUP2,...[,GROUPN]]]：指明用户所属的附加组，多个组之间用逗号分隔**

**-c，--comment COMMENT：指明注释信息**

**-d，--home-dir HOME_DIR：以指定的路径为用户的家目录，通过复制/etc/skel此目录并重命名实现，指定的家目录路径如果事先存在，则不会为用户复制环境配置文件**

**-s, --shell SHELL：指定用户的默认shell，可用的所有shell列表存储在/etc/shells文件中**

**-r, --system，创建系统用户**

**-M, --no-create-home：不为用户创建主目录**

**-f, --inactive INACTIVE：用户密码后，账户被彻底禁用之前的天数。 0表示立即禁用。-1表示禁用这个功能，如果未指定，useradd将使用/etc/defult/useradd中的INACTIVE指定的默认禁用周期，活着吗默认为-1**

    [root@centos7 ~]# useradd -d /home/lee -u 8888 -s /sbin/nologin linuxprobe
    [root@centos7 ~]# id linuxprobe
    uid=8888(linuxprobe) gid=8888(linuxprobe) groups=8888(linuxprobe)


注意：创建用户时的诸多默认设定配置文件为/etc/login.defs

**-D：显示创建用户的默认配置**

-D [选项 ] ：修改默认选项的值

修改的结果保存于/etc/default/useradd文件中

#### usermod：修改用户属性

格式：`usermod [options] LOGIN`

**-u, --uid UID：修改用户ID为此处指定的新UID**

**-g, --gid GROUP：修改用户所属的基本组**

**-G, --groups GROUP1[,GROUP2,...[,GROUPN]]]：修改用户所属的附加组，原来的附加组会被覆盖**

**-a, --append：与-G一同使用，用于用户追加新的附加组**

**-c，--comment COMMENT：修改注释信息**

**-d，--home-dir HOME_DIR：修改用户的家目录，用户原有的文件不会被转移至新位置**

**-m, --move-home：与-d一同使用，用于将原来的家目录移动为新的家目录**

**-l, --login NEW_LOGIN：修改用户名**

**-s, --shell SHELL：修改用户的默认shell**

**-L, --lock：锁定用户密码，即在用户原来的密码字符串之前添加一个“！”**

**-U, --unlock：解锁用户密码**

    [root@centos7 ~]# id linuxprobe
    uid=8888(linuxprobe) gid=8888(linuxprobe) groups=8888(linuxprobe)
    [root@centos7 ~]# usermod -G root linuxprobe
    [root@centos7 ~]# id linuxprobe
    uid=8888(linuxprobe) gid=8888(linuxprobe) groups=8888(linuxprobe),0(root)
    [root@centos7 ~]# usermod -u 9999 linuxprobe
    [root@centos7 ~]# id linuxprobe
    uid=9999(linuxprobe) gid=8888(linuxprobe) groups=8888(linuxprobe),0(root)


#### userdel：删除用户

格式：`userdel [options] LOGIN`

**-r：删除用户时一定删除其家目录**


#### passwd：
`passwd [-k] [-l] [-u [-f]] [-d] [-e] [-n mindays] [-x maxdays] [-w warndays] [-i inactivedays] [-S] [--stdin] [username]`

（1）passwd：修改用户自己的密码

    [root@centos7 ~]# passwd
    Changing password for user root.
    New password: 
    Retype password:
    [root@centos7 ~]# passwd lee
    Changing password for user lee.
    New password:
    Retype password:

（2）passwd USERNAME：修改知道指定用户的密码，但仅root有此权限

**-l，-u：锁定和解锁用户**

**-d：删除用户密码串**

**-e DATE：过期期限，日期**

**-n DAYS：密码的最短使用期限**

**-x DAYS：密码的最大使用期限**

**-i DAYS：非活动期限**

**-w DAYS：警告期限**

>--stdin：
>~]#  echo “PASSWORD” | passwd --stdin USERNAME

#### gpasswd：
格式：` gpasswd [option] group`

**-a USERNAME：向组中添加用户**

**-d USERNAME：从组中移除用户**

#### newgrp：临时切换指定的组为基本组
格式：`newgrp [-] [group]`

-：会模拟用户重新登录以实现重新初始化其工作环境

#### chage：更改用户密码过期信息

格式：`chage [options] LOGIN_NAME`

#### id：显示用户真实和有效ID

格式：`id [OPTION]... [USER]`

**-u：仅显示有效的UID**

**-g：仅显示用户的基本组ID**

**-G：仅显示用户所属的所有组ID**

**-n：显示名字而非ID，可以和上述几个选项一起使用**

#### su：switch user

* 登录式切换：会通过重新读取用户的配置文件来重新初始化

>~]# su - USERNAME
>~]# su -l USERNAME

* 非登录式切换：不会读取目标用户的配置文件进行初始化
>~]# su USERNAME

注意：管理员可无密码切换至其他任何用户

-c 'COMMAND'：仅以指定用户的身份运行此处指定的命令

其他几个命令：chsh，chfn，finger，whoami，pwck（检查密码有没有问题），grpck


#### 权限管理：
    [root@centos7 ~]# ll
    total 12
    -rw-r--r--. 1 root root   23 Mar 26 13:33 abc
    -rw-------. 1 root root 1628 Mar 24 14:34 anaconda-ks.cfg
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Desktop
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Documents
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Downloads
    -rw-r--r--. 1 root root   23 Nov 23 21:16 ljw
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Music
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Pictures
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Public
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Templates
    drwx------. 2 root root    6 Mar 31 09:41 test.On1N
    -rw-------. 1 root root    0 Mar 31 09:40 test.yXH
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Videos

|rwxrwxrwx|含义|
|:---:|:---:|
|左三位|定义user（owner）的权限|
|中三位|定义group的权限|
|右三位|定义other的权限|

**进程安全上下文：进程对文件的访问权限应用模型**
>进程的属主与文件的属主是否相同，如果相同，则应用属主权限
>否则，则检查进程的属主是否属于文件的属组，如果是，则应用属组权限
>否则，只能应用other权限

**权限：**
|rwx|含义|
|:---:|:---:|
| r(readable)|读|
|w(readable)|写|
| x(excutable)|执行|
**文件：**
|rwx|含义|
|:---:|:---:|
| r|可获取文件的数据|
|w|可修改文件的数据|
| x|可将此文件运行为进程|

**目录：**
|rwx|含义|
|:---:|:---:|
| r|可使用ls命令获取其下的所有文件列表|
|w|可修改此目录下的文件列表，即创建或删除文件|
| x|可cd至此目录中，且可使用ls -l来获取所有文件的详细属性信息|


>mode：rwxrwxrwx
>ownership：user，group

**权限组合机制：**

|权限位|八进制权限|
|:---:|:---:|
|---|0|
|--x|1|
|-w-|2|
|-wx|3|
|r--|4|
|r-x|5|
|rw-|6|
|rwx|7|
**文件权限格式：**



![](https://upload-images.jianshu.io/upload_images/16547068-39bd82dadb755c4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

####权限管理命令：

**三类用户：**

|  u   |  g   |  o   |  a   |
| :--: | :--: | :--: | :--: |
| 属主 | 属组 | 其他 | 所有 |

#### chmod：
（1）`chmod [OPTION]... MODE[,MODE]... FILE...`

**MODE表示法：**
①赋权表示法：直接操作一类用户的所有权限位
u=，g=，o=，a=

    [root@centos7 ~]# ll
    total 12
    -rw-r--r--. 1 root root   23 Mar 26 13:33 abc
    -rw-------. 1 root root 1628 Mar 24 14:34 anaconda-ks.cfg
    [root@centos7 ~]# chmod u=rw,g=r,o=rw anaconda-ks.cfg 
    [root@centos7 ~]# ll
    total 12
    -rw-r--r--. 1 root root   23 Mar 26 13:33 abc
    -rw-r--rw-. 1 root root 1628 Mar 24 14:34 anaconda-ks.cfg

②授权表示法：直接操作一类用户的一位权限位r，w，x
u+，u-，g+，g-，o+，o-，a+，a-

    [root@centos7 ~]# ll
    total 12
    -rw-r--r--. 1 root root   23 Mar 26 13:33 abc
    -rw-------. 1 root root 1628 Mar 24 14:34 anaconda-ks.cfg
    [root@centos7 ~]# chmod g+x anaconda-ks.cfg
    [root@centos7 ~]# ll
    total 12
    -rw-r--r--. 1 root root   23 Mar 26 13:33 abc
    -rw---x---. 1 root root 1628 Mar 24 14:34 anaconda-ks.cfg

全局使用r，w，x直接添加即可

    [root@centos7 ~]# chmod -x anaconda-ks.cfg 
注意：全局+w时，只对属主+w

（2）`chmod [OPTION]... OCTAL-MODE FILE...`
八进制权限位
（3）`chmod [OPTION]... --reference=RFILE FILE...`
 引用性修改，参照其他目录的权限

##### Option：

**-R, --recursive：递归修改**

注意：用户仅能修改属主为自己的那些文件权限


#### 从属关系管理命令：chown，chgrp

`chown [OPTION]... [OWNER][:[GROUP]] FILE...`

`chown [OPTION]... --reference=RFILE FILE...`

Option：-R：递归修改

`chgrp [OPTION]... GROUP FILE...`

`chgrp [OPTION]... --reference=RFILE FILE...`

*注意：仅管理员可以修改文件的属主和属组
用户对目录有写权限，但对目录下的文件没有写权限，不可以修改此文件内容，可以删除此文件*



#### umask：文件的权限反向掩码，遮罩码

文件：666-umask=默认权限

目录：777-umask=默认权限

注意：之所以文件用666去减，表示文件默认不能拥有执行权限，如果减得的结果中有执行权限，则需要将其加1

例如：umask：023

 666-023=624（623） 

 777-023=754

##### umask命令：

umask：查看当前umask

umask MASK：设置umask

*注意：此类设定仅对当前shell进程有效*




#### Linux系统上的特殊权限

特殊权限：SUID， SGID， STICKY



**SUID：**
默认情况下：用户发起的进程，进程的属主是其发起者；因此，其以发起者的身份在运行；

SUID的功用：用户运行某程序时，如果此程序拥有SUID权限，那么程序运行为进程时，进程的属主不是发起者，而程序文件自己的属主；

管理文件的SUID权限：
chmod u+|-s FILE...

展示位置：属主的执行权限位

如果属主原本有执行权限，显示为小写s，否则，显示为大写S

    [root@centos7 ~]# ls -l /etc/shadow
    ----------. 1 root root 1348 Mar 31 17:42 /etc/shadow
    [root@centos7 ~]# ls -l /bin/passwd
    -rwsr-xr-x. 1 root root 27832 Jun 10  2014 /bin/passwd

**SGID：**
功用：当目录属组有写权限，且有SGID权限时，那么所有属于此目录的属组，且以属组身份在此目录中新建文件或目录时，新文件的属组不是用户的基本组，而是此目录的属组；

管理文件的SGID权限：
chmod g+|-s FILE...

展示位置：属组的执行权限位

如果属组原本有执行权限，显示为小写s;否则，显示为大写S

**Sticky：**
功用：对于属组或全局可写的目录，组内的所有用户或系统上的所有用户对在此目录中都能创建新文件或删除所有的已有文件；如果为此类目录设置Sticky权限，则每个用户能创建新文件，且只能删除自己的文件；

管理文件的Sticky权限：
chmod o+|-t FILE...

展示位置：其它用户的执行权限位

如果其它用户原本有执行权限，显示为小写t，否则，显示为大写T；

**系统上的/tmp和/var/tmp目录默认均有sticky权限**

管理特殊权限的另一方式：


| suid | sgid | sticy | 权限位 | 八进制权限 |
| :--: | :--: | :---: | :----: | :--------: |
|  0   |  0   |   0   |  ---   |     0      |
|  0   |  0   |   1   |  --x   |     1      |
|  0   |  1   |   0   |  -w-   |     2      |
|  0   |  1   |   1   |  -wx   |     3      |
|  1   |  0   |   0   |  r--   |     4      |
|  1   |  0   |   1   |  r-x   |     5      |
|  1   |  1   |   0   |  rw-   |     6      |
|  1   |  1   |   1   |  rwx   |     7      |

基于八进制方式赋权时，可于默认的三位八进制数字左侧再加一位八进制数字；

例如：chmod 1777

### facl：file access control lists

    文件的额外赋权机制：
    
        在原来的u,g,o之外，另一层让普通用户能控制赋权给另外的用户或组的赋权机制；

#### getfacl命令：
格式：`getfacl FILE...`

user:USERNAME:MODE

group:GROUPNAME:MODE

    [root@centos7 ~]# getfacl /root
    getfacl: Removing leading '/' from absolute path names
    # file: root
    # owner: root
    # group: root
    user::r-x
    user:lee:rwx
    group::r-x
    mask::rwx
    other::---


#### setfacl命令：

赋权给用户：
`setfacl  -m  u:USERNAME:MODE  FILE...`

赋权级组：
`setfacl  -m  g:GROUPNAME:MODE FILE...`

撤销赋权：
`setfacl  -x u:USERNAME  FILE...`
`setfacl  -x  g:GROUPNAME  FILE...`

    [root@centos7 ~]# su - lee
    Last login: Sun Mar 31 17:57:20 CST 2019 on pts/0
    [lee@centos7 ~]$ cd /root
    -bash: cd: /root: Permission denied
    [lee@centos7 ~]$ exit
    logout
    [root@centos7 ~]# setfacl -Rm u:lee:rwx /root
    [root@centos7 ~]# su - lee
    Last login: Sun Mar 31 17:58:59 CST 2019 on pts/0
    [lee@centos7 ~]$ cd /root
    [lee@centos7 root]$ ls
    abc  anaconda-ks.cfg  Desktop  Documents  Downloads  ljw  Music  Pictures  Public  Templates  
    test.On1N  test.yXH  Videos
    [lee@centos7 root]$ exit
    logout
    [root@centos7 ~]# ls -ld /root
    dr-xrwx---+ 17 root root 4096 Mar 31 17:37 /root
    权限最后一个变成了+号意味着该文件已经设置了ACL

#### su命令与sudo服务：
格式：` su [options...] [-] [user [args...]]`
从root管理员切换至普通用户lee

    [root@centos7 ~]# id
    uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0- 
    s0:c0.c1023
    [root@centos7 ~]# su - lee
    Last login: Sun Mar 31 17:59:49 CST 2019 on pts/0
    [lee@centos7 ~]$ 

su - USERNAME：带有-表示完全切到新的用户，即把环境变量也变更为新用户的相应信息，而不保留原始的信息
*注意：root用户切换到普通用户时不需要密码验证，而普通用户切换到root用户需要密码验证*

**sudo服务**
格式：`sudo [参数] 命令名称`
|字符|含义|
|:---:|:---:|
|-h|列出帮助信息|
|-l|列出当前用户可执行的命令|
|u用户名或UID值|以指定用户身份执行命令|
|-k|清空密码的有效时间，下次执行sudo时需要再次进行密码验证|
|-b|在后台执行指定的命令|
|-p|更改询问密码的提示语|
如果担心直接修改配置文件会出现问题，则可以使用sudo命令提供的visudo命令来配置用户权限

    [root@centos7 ~]# visudo
    1 ## Sudoers allows particular users to run various commands as
    2 ## the root user, without needing the root password.
    99 ## Allow root to run any commands anywhere
    100 lee     ALL=(ALL)       ALL
    101 root    ALL=(ALL)       ALL
    [lee@centos7 ~]$ sudo ls /root
    [sudo] password for lee: 
    abc  anaconda-ks.cfg  Desktop  Documents  Downloads  ljw  Music  Pictures  Public  Templates  
    test.On1N  test.yXH  Videos
每次执行sudo命令都会要求输一次密码，可以使用如下方法使用户执行sudo命令时不需要密码验证

    [root@centos7 ~]# whereis poweroff
    poweroff: /usr/sbin/poweroff /usr/share/man/man8/poweroff.8.gz
    [root@centos7 ~]# visudo
    99 ## Allow root to run any commands anywhere
    100 lee     ALL=(ALL)       ALL
    101 root    ALL=NOPASSWD:   /usr/bin/poweroff
    [root@centos7 ~]# su - lee
    Last login: Sun Mar 31 18:28:32 CST 2019 on pts/0
    [lee@centos7 ~]$ sudo ls /root
    abc  anaconda-ks.cfg  Desktop  Documents  Downloads  ljw  Music  Pictures  Public  Templates  
    test.On1N  test.yXH  Videos