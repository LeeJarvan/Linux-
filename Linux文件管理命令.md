#### pwd：printing working directory  显示工作目录

    [root@promote ~]# pwd
    /root

#### cd：change directory 切换目录
格式：`cd [/PATH/TO/SOMEDIR]`
**cd：切换回家目录**

    [root@promote etc]# cd 
    [root@promote ~]# pwd
    /root
*注意：bash中，~表示家目录*
**cd ~：切换回自己的家目录**

    [root@promote ~]# cd /bin
    [root@promote bin]# cd ~
    [root@promote ~]# pwd
    /root

**cd ~USERNAME：切换至指定用户的家目录**

    [root@promote ~]# cd /bin
    [root@promote bin]# cd ~root
    [root@promote ~]# pwd
    /root

**cd -：在上一次所在目录与当前目录之间来回切换**

    [root@promote ~]# cd -
    /bin
    [root@promote bin]# cd -
    /root
    [root@promote ~]# cd -
    /bin
    [root@promote bin]# cd -
    /root


#### ls：list  列出指定目录下的内容
格式：`ls [OPTION]... [FILE]...`
**-a：显示所有文件，包括隐藏文件**

    [root@centos7 ~]# ls -a
    .   anaconda-ks.cfg  .bash_logout   .bashrc  .config  .dbus    Documents  .esd_auth      .local  Pictures  
    .tcshrc    Videos
    ..  .bash_history    .bash_profile  .cache   .cshrc   Desktop  Downloads  .ICEauthority  Music   Public    
    Templates  .Xauthority


 **-A：显示除 . 和 . . 之外的所有文件**
    
    [root@centos7 ~]# ls -A
    anaconda-ks.cfg  .bash_logout   .bashrc  .config  .dbus    Documents  .esd_auth      .local  Pictures  
    .tcshrc    Videos
    .bash_history    .bash_profile  .cache   .cshrc   Desktop  Downloads  .ICEauthority  Music   Public    
    Templates  .Xauthority


**-l：--long，长格式列表，即显示文件的详细属性信息**

    [root@centos7 ~]# ls -l
    total 4
    -rw-------. 1 root root 1628 Mar 24 14:34 anaconda-ks.cfg
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Desktop
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Documents
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Downloads
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Music
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Pictures
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Public
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Templates
    drwxr-xr-x. 2 root root    6 Mar 24 14:52 Videos

  -：表示文件类型，-，d，b，c，l，s，p

**rw-r--r--**
|字符名称|含义|
|:---: |:---:|
|rw-|文件属主的权限|
|r--|文件属组的权限|
|r--|其他用户（非属主、属组）的权限|
|1|数字表示文件被硬链接的次数|
| 第一个root|文件的属主|
| 第二个root|文件的属组|
|1628|数字表示文件的大小，单位是字节     |


**-h，--human-readable  对文件大小单位换算   换算后的结果可能是非精确值**

    [root@centos7 ~]# ls -h
    anaconda-ks.cfg  Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos

```
[root@centos7 ~]# ls -l -h
total 4.0K
-rw-------. 1 root root 1.6K Mar 24 14:34 anaconda-ks.cfg
drwxr-xr-x. 2 root root    6 Mar 24 14:52 Desktop
drwxr-xr-x. 2 root root    6 Mar 24 14:52 Documents
drwxr-xr-x. 2 root root    6 Mar 24 14:52 Downloads
drwxr-xr-x. 2 root root    6 Mar 24 14:52 Music
drwxr-xr-x. 2 root root    6 Mar 24 14:52 Pictures
drwxr-xr-x. 2 root root    6 Mar 24 14:52 Public
drwxr-xr-x. 2 root root    6 Mar 24 14:52 Templates
drwxr-xr-x. 2 root root    6 Mar 24 14:52 Videos
```

>Mar 24 14:34：文件最近一次被修改的时间
>anaconda-ks.cfg：文件名


**-d：只查看目录自身属性，而非其内部的文件列表**

    [root@centos7 ~]# ls -d
    .

**-r：reverse，逆序显示**

    [root@centos7 ~]# ls -r
    Videos  Templates  Public  Pictures  Music  Downloads  Documents  Desktop  anaconda-ks.cfg


**-R：recursive，递归显示**

    [root@centos7 ~]# ls -R
    .:
    anaconda-ks.cfg  Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
    
    ./Desktop:
    
    ./Documents:
    
    ./Downloads:
    
    ./Music:
    
    ./Pictures:
    
    ./Public:
    
    ./Templates:
    
    ./Videos:

#### cat：concatenate  文本文件查看工具
格式：`cat [OPTION]... [FILE]...  `

    [root@centos7 ~]# cat /etc/fstab
    
    #
    # /etc/fstab
    # Created by anaconda on Sun Mar 24 14:30:18 2019
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    UUID=385f075d-1421-48a1-911b-d6ec2f9c2016 /                       xfs     defaults        0 0
    UUID=3710be3c-5419-4d96-9795-e5f6863b59b3 /boot                   xfs     defaults        0 0
    UUID=3f0703b3-e4e1-4f8d-84c4-dfb4c6ec4dc6 /data                   xfs     defaults        0 0
    UUID=8c696c94-b458-44c3-a218-a9c09512e4bd swap                    swap    defaults        0 0

**-n：给显示的文本行编号**

    [root@centos7 ~]# cat -n /etc/issue
     1	\S
     2	Kernel \r on an \m
     3	


**-e：显示行结束符$**

    [root@centos7 ~]# cat -e /etc/issue
    \S$
    Kernel \r on an \m$
    $


#### file：查看文件内容类型
格式：`file [FILE]...`

    [root@centos7 ~]# file /etc/shadow
    /etc/shadow: ASCII text
    [root@centos7 ~]# file /etc/issue
    /etc/issue: ASCII text
*注意：和上述 cat命令联系，cat只能查看文本文件，如果需要查看该文件是否为文本文件可以先用 file命令*
#### tac：文本文件查看工具   
>和cat作用一样 逆序显示

    [root@centos7 ~]# tac /etc/issue
    
    Kernel \r on an \m
    \S
    [root@centos7 ~]# cat /etc/issue
    \S
    Kernel \r on an \m

#### 分屏查看命令：more less
>格式：`more FILE`
>翻屏至文件尾部后自动退出
>格式：`less FILE`
>和man命令一样

#### head：查看文件前n行
>格式：`head [options] FILE`
>**-n #或 -#：指定要查看的行数**

#### tail：查看文件后n行
>格式：`tail [options] FILE`
>**-n #或 -#：指定要查看的行数**
>**-f：查看文件尾部内容结束后不退出，跟随显示新增的行**

#### stat：stat - display file or file system status
>格式：`stat FILE . . .`
>文件：两类数据
>      元数据：metadata  使用stat看到的就是元数据
>      数据：data  使用cat看到的都是数据
>时间戳：Access: 2019-03-26 09:17:24.612736837 +0800
>             Modify: 2019-03-26 09:17:24.612736837 +0800
>             Change: 2019-03-26 09:17:24.612736837 +0800

    [root@centos7 ~]# stat /tmp/1.txt
      File: ‘/tmp/1.txt’
      Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
    Device: 802h/2050d	Inode: 67614504    Links: 1
    Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
    Context: unconfined_u:object_r:user_tmp_t:s0
    Access: 2019-03-26 09:17:24.612736837 +0800
    Modify: 2019-03-26 09:17:24.612736837 +0800
    Change: 2019-03-26 09:17:24.612736837 +0800
    Birth: -
|字符名称|含义|
|:---: |:---:|
|File|文件名称|
| Size|文件大小|
|Blocks|IO块大小|
|regular file|这里是显示文件的类型，这是一个普通文件|
|Device|所在设备|
| Inode|Inode节点号|
| Links|被链接的次数|
|Access（第一个）|访问权限|
|Uid|uid号和属主|
|Gid|gid号和属组|
|Access（第二个）|文件最近一次的访问时间|
|Modify|文件的修改时间|
|Chang|文件的改变时间|

**修改文件的时间戳信息用 touch命令**

#### touch：touch - change file timestamps  当目标文件不存在时直接创建
格式：`touch [OPTION]... FILE...`
**-c：指定的文件路径不存在时不予创建**

    [root@centos7 ~]# touch -c /tmp/ljw
    [root@centos7 ~]# ls /tmp
    1.txt                                                                         tracker-extract-files.0
    hsperfdata_root                                                               vmware-root_5644-1003073663
    ks-script-FMHiRV                                                              vmware-root_5754-701204016
    l.1                                                                           vmware-root_5794-700548750
    ljw                                                                           vmware-root_6159-1950294947

**-a：仅修改access time**
**-m：仅修改modify time**
**-t STAMP：[[CC]YY]MMDDhhmm[.ss]**

    [root@centos7 ~]# touch -m -t 0212010313.23 /tmp/1.txt
    [root@centos7 ~]# stat /tmp/1.txt
      File: ‘/tmp/1.txt’
      Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
    Device: 802h/2050d	Inode: 67614504    Links: 1
    Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
    Context: unconfined_u:object_r:user_tmp_t:s0
    Access: 2019-03-26 09:17:24.612736837 +0800
    Modify: 2002-12-01 03:13:23.000000000 +0800
    Change: 2019-03-26 09:26:33.116726874 +0800
    Birth: -




#### echo：回显命令
格式：`echo [SHORT-OPTION]... [STRING]...`
**-n：不自动进行换行操作**
```
[root@centos7 ~]# echo -n hello
hello[root@centos7 ~]# 
```
**-e：让转义符生效**     
|  \n  |   \t   |          \b          |
| :--: | :----: | :------------------: |
| 换行 | 制表符 | 退格，删除前一个字符 |

    [root@centos7 ~]# echo "hello \neveryone"
    hello \neveryone
    [root@centos7 ~]# echo -e "hello \neveryone"
    hello 
    everyone
STRING可以用引号，单引号和双引号均可用
**单引号：强引用，变量引用不执行替换**

    [root@centos7 ~]# echo '$SHELL'
    $SHELL

**双引号：弱引用，变量引用会被替换**

    [root@centos7 ~]# echo "$SHELL"
    /bin/bash

*注意：变量引用的正规符号   
$（name）*


#### 关机或重启命令：shutdown
格式：`shutdown [OPTIONS...] [TIME] [WALL...]`
OPTIONS：
**-h：halt 立即关闭**

    [root@centos7 ~]# shutdown -h 13:00
    Shutdown scheduled for Tue 2019-03-26 13:00:00 CST, use 'shutdown -c' to cancel.
    在下午一点关机（-h参数使用24小时制）

**-r：reboot 重启**

    [root@centos7 ~]# shutdown -r +10
    Shutdown scheduled for Mon 2019-03-25 21:41:21 CST, use 'shutdown -c' to cancel.
    [root@centos7 ~]# 
    Broadcast message from root@centos7.localdomain (Mon 2019-03-25 21:31:21 CST):
    
    The system is going down for reboot at Mon 2019-03-25 21:41:21 CST!
    在10分钟后重启

**-c：cancel 取消预定的定时关机操作**

WALL：向所有终端发送消息
#### 关机命令：

    [root@centos7 ~]# systemctl poweroff
    [root@centos7 ~]# poweroff

#### 重启命令：

    [root@centos7 ~]# reboot


*日期相关的命令：Linux系统启动时从硬件读取日期和时间信息，读取完成后，就不再与硬件相关联*
#### date ：系统时钟 显示日期时间  
格式：`date [OPTION]... [+FORMAT]`

    [root@centos7 ~]# date
    Mon Mar 25 21:38:12 CST 2019
FORMAT：格式符
|   %F   |     %T     |                             %s                              |
| :----: | :--------: | :---------------------------------------------------------: |
| 年月日 | 小时分钟秒 | 1970年1月1日（unix元年）0点0分0秒到命令执行那一刻经过的秒数 |


按照“年-月-日 小时:分钟:秒”的形式显示当前时间

    [root@centos7 ~]# date "+%Y-%m-%d %H:%M:%S"
    2019-03-25 21:54:33
设置日期时间：`date [MMDDhhmm[[CC]YY][.ss]]`

    [root@centos7 ~]# date 032521582019.01
    Mon Mar 25 21:58:01 CST 2019


#### hwclock，clock：硬件时钟  显示或设定硬件时钟

    [root@centos7 ~]# hwclock
    Mon 25 Mar 2019 10:00:10 PM CST  -0.698718 seconds
    [root@centos7 ~]# clock
    Mon 25 Mar 2019 10:00:56 PM CST  -0.445681 seconds

**-s, --hctosy：以hc为准，系统设定和硬件一样**

**-w, --systohc：以sys为准，硬件设置和系统一样**
#### cal
格式：`cal [[month] year]`
    
    [root@centos7 ~]# cal
         March 2019     
    Su Mo Tu We Th Fr Sa
                    1  2
     3  4  5  6  7  8  9
    10 11 12 13 14 15 16
    17 18 19 20 21 22 23
    24 25 26 27 28 29 30
    31
    [root@centos7 ~]# cal 2019
                               2019                               
    
           January               February                 March       
    Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
           1  2  3  4  5                   1  2                   1  2
     6  7  8  9 10 11 12    3  4  5  6  7  8  9    3  4  5  6  7  8  9
    13 14 15 16 17 18 19   10 11 12 13 14 15 16   10 11 12 13 14 15 16
    20 21 22 23 24 25 26   17 18 19 20 21 22 23   17 18 19 20 21 22 23
    27 28 29 30 31         24 25 26 27 28         24 25 26 27 28 29 30
                                                  31
           April                   May                   June        
    Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
        1  2  3  4  5  6             1  2  3  4                      1
    7  8  9 10 11 12 13    5  6  7  8  9 10 11    2  3  4  5  6  7  8
    14 15 16 17 18 19 20   12 13 14 15 16 17 18    9 10 11 12 13 14 15
    21 22 23 24 25 26 27   19 20 21 22 23 24 25   16 17 18 19 20 21 22
    28 29 30               26 27 28 29 30 31      23 24 25 26 27 28 29
                                                  30
            July                  August                September     
    Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
        1  2  3  4  5  6                1  2  3    1  2  3  4  5  6  7
     7  8  9 10 11 12 13    4  5  6  7  8  9 10    8  9 10 11 12 13 14
    14 15 16 17 18 19 20   11 12 13 14 15 16 17   15 16 17 18 19 20 21
    21 22 23 24 25 26 27   18 19 20 21 22 23 24   22 23 24 25 26 27 28
    28 29 30 31            25 26 27 28 29 30 31   29 30
    
           October               November               December      
    Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
           1  2  3  4  5                   1  2    1  2  3  4  5  6  7
     6  7  8  9 10 11 12    3  4  5  6  7  8  9    8  9 10 11 12 13 14
    13 14 15 16 17 18 19   10 11 12 13 14 15 16   15 16 17 18 19 20 21
    20 21 22 23 24 25 26   17 18 19 20 21 22 23   22 23 24 25 26 27 28
    27 28 29 30 31         24 25 26 27 28 29 30   29 30 31


#### which：显示命令的完整路径
格式：`which [options] [--] programname [...]`
     
    [root@centos7 ~]# which ls
    alias ls='ls --color=auto'
    /usr/bin/ls

 如果不想让which显示命令别名，用\which实现

    [root@centos7 ~]# \which ls
    /usr/bin/ls

--skip-alias：忽略别名

    [root@centos7 ~]# which --skip-alias
    Usage: /usr/bin/which [options] [--] COMMAND [...]
    Write the full path of COMMAND(s) to standard output.

#### whereis：显示二进制文件，源码文件，手册页
格式：`whereis [options] `
**-b   只看二进制格程序路径**     
**-m  只搜索使用手册文件路径**
**-s   只搜索源文件路径**

#### who：查看登录当前系统的相关用户信息
格式：`who [OPTION]`

    [root@centos7 ~]# who
    root     :0           2019-03-26 08:43 (:0)
    root     pts/0        2019-03-26 08:43 (192.168.0.101)
**-b：显示最近一次操作系统启动的时间**

    [root@centos7 ~]# who -b
         system boot  2019-03-26 08:41

**-d：显示死亡进程**

    [root@centos7 ~]# who -d

**-l：显示登录进程**

    [root@centos7 ~]# who -l

**-r：运行级别**

    [root@centos7 ~]# who -r
         run-level 5  2019-03-26 08:42

#### w：增强版的who命名，显示登录用户和正在做什么

    [root@centos7 ~]# w
     08:46:28 up 4 min,  2 users,  load average: 0.11, 0.62, 0.36
    USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
    root     :0       :0               08:43   ?xdm?   1:21   0.60s /usr/libexec/gnome-session-binary --session 
    gnome-classic
    root     pts/0    192.168.0.101    08:43    4.00s  0.19s  0.09s w

#### mkdir：make directories
格式：`mkdir [OPTION]... DIRECTORY...`
**-p：自动按需创建父目录，目录不存在时自动创建目录**

    [root@centos7 ~]# mkdir /tmp/ljw/x
    [root@centos7 ~]# ls /tmp
    hsperfdata_root                                                               vmware-root_5644-1003073663
    ks-script-FMHiRV                                                              vmware-root_5754-701204016
    ljw                                                                           vmware-root_5794-700548750

**-v：verbose，显示详细过程**

    [root@centos7 ~]# mkdir -pv /tmp/a/b/c
    mkdir: created directory ‘/tmp/a’
    mkdir: created directory ‘/tmp/a/b’
    mkdir: created directory ‘/tmp/a/b/c’


**-m MODE：直接给定权限**
*注意：路径基名为命令的作用对象，基名之前的路径必须存在*

#### rmdir：remove empty directories
格式：`rmdir [OPTION]... DIRECTORY...`

    [root@centos7 ~]# rmdir /tmp/a
    rmdir: failed to remove ‘/tmp/a’: Directory not empty
    [root@centos7 ~]# rmdir /tmp/a/b/c
    单独使用rmdir命令时，只能删除空目录
**-p：删除某空目录后，如果其父附录为空，则一并删除**
**-v：verbose，显示详细过程**
#### tree
格式：`tree [options] [directory]`
```
[root@centos7 ~]# tree
.
├── anaconda-ks.cfg
├── Desktop
├── Documents
├── Downloads
├── Music
├── Pictures
├── Public
├── Templates
└── Videos
```
**-L：level，指定要显示的层级**
如果没有tree明了，使用~]# sudo yum -y install tree  安装tree命令


#### cp：copy
1. 单源复制：`cp [OPTION]... [-T] SOURCE DEST`
  如果DEST不存在，则事先创建此文件，并复制源文件的数据流至DEST中
  如果DEST存在：
   * 如果DEST是非目录文件，则覆盖目标文件
   * 如果DEST是目录文件，则先在DEST目录下创建一个与源文件同名的文件，并复制其数据流至目标文件

2. 多源复制：`cp [OPTION]... SOURCE... DIRECTORY`，`  cp [OPTION]... -t DIRECTORY SOURCE...`
  如果DEST不存在，错误
  如果DEST存在：
  如果DEST是非目录文件：错误
  如果DEST是目标文件：分别复制每个文件至目标目录中，并保持原名

常用选项：
**-i：交互式复制，即覆盖之前提醒用户确认**

    [root@centos7 ~]# touch /tmp/1.txt
    [root@centos7 ~]# touch /tmp/11.txt
    [root@centos7 ~]# cp -i /tmp/1.txt /tmp/11.txt
    cp: overwrite ‘/tmp/11.txt’? y


**-f：强制覆盖目标文件**


**-r：递归复制目录**
**-d：复制符号链接文件本身，而非其指向的源文件**


    [root@centos7 ~]# ll /etc/issue
    -rw-r--r--. 1 root root 23 Nov 23 21:16 /etc/issue
    [root@centos7 ~]# cp -d /etc/issue abc
    cp: overwrite ‘abc’? y
    [root@centos7 ~]# ll abc
    -rw-r--r--. 1 root root 23 Mar 26 13:33 abc
**-a：-dR --preserve=all，archive，用于实现归档**
--preserve=
|    字符    |     含义     |
| :--------: | :----------: |
|    mode    |     权限     |
| ownership  |  属主和属组  |
| timestamps |    时间戳    |
|  context   |   安全标签   |
|   xattr    |   扩展属性   |
|   links    |   符号链接   |
|    all     | 上述所有属性 |

​    

**-p：保留源文件或目录的属性**

    [root@centos7 ~]# stat /etc/issue
      File: ‘/etc/issue’
      Size: 23        	Blocks: 8          IO Block: 4096   regular file
    Device: 802h/2050d	Inode: 67199714    Links: 1
    Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
    Context: system_u:object_r:etc_t:s0
    Access: 2019-03-26 09:03:47.229025436 +0800
    Modify: 2018-11-23 21:16:58.000000000 +0800
    Change: 2019-03-24 14:30:37.691004012 +0800
     Birth: -
    [root@centos7 ~]# cp -p /etc/issue ljw
    [root@centos7 ~]# stat ljw
     File: ‘ljw’
      Size: 23        	Blocks: 8          IO Block: 4096   regular file
    Device: 802h/2050d	Inode: 102558741   Links: 1
    Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
    Context: unconfined_u:object_r:admin_home_t:s0
    Access: 2019-03-26 09:03:47.229025436 +0800
    Modify: 2018-11-23 21:16:58.000000000 +0800
    Change: 2019-03-26 13:39:04.792595732 +0800
    Birth: -

#### mv：move （和cp类似）
    mv [OPTION]... [-T] SOURCE DEST
    mv [OPTION]... SOURCE... DIRECTORY
    mv [OPTION]... -t DIRECTORY SOURCE...

**-i：交互式操作，覆盖前先询问用户，如果源文件与目标文件或目标目录中的文件同名，则询问用户是否覆盖目标文件**
**-f：若目标文件或目录与现有的文件或目录重复，则直接覆盖现有的文件或目录**

#### rm：remove
格式：`rm [OPTION]... FILE...`
**-i：删除已有文件或目录之前先询问用户。（只有root用户默认rm=rm -i）**

    [root@centos7 ~]# rm 3.txt
    rm：是否删除普通空文件 "3.txt"？y
    [root@centos7 ~]#
**-f：强制删除文件或目录**

    [root@centos7 ~]# rm -f 10.txt
    [root@centos7 ~]#
**-r：递归处理，将指定目录下的所有文件与子目录一并处理**

*删除目录：rm -rf /PATH/TO/DIR
危险操作：rm -rf /    或者 rm -rf  /*     自杀
*注意：所有不用文件建议不要直接删除，而是移动至某个专用目录（模拟回收站）*




#### install：复制文件
>install命令与cp命令类似，均可以将文件或目录拷贝到指定的路径；但是install命令可以控制目标文件的属性。

install命令：复制文件和设置文件属性

       install [OPTION]... [-T] SOURCE DEST  单源复制
    
       install [OPTION]... SOURCE... DIRECTORY  多源复制
    
       install [OPTION]... -t DIRECTORY SOURCE...  多源复制
    
       install [OPTION]... -d DIRECTORY...  创建空目录

**-m，--mode=MODE，设定目标文件权限，默认为755**

**-o，--owner=OWNER，设定目标文件的属主**

**-g，--group=GROUP，设定目标文件属组**

**-d：创建目录**
    

    [root@centos7 ~]# install /etc/passwd /tmp/passwd.bak
    [root@centos7 ~]# cat /tmp/passwd.bak | head -5
    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/bin:/sbin/nologin
    daemon:x:2:2:daemon:/sbin:/sbin/nologin
    adm:x:3:4:adm:/var/adm:/sbin/nologin
    lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin


#### mktemp：创建临时文件或临时目录
格式：`mktemp [OPTION]... [TEMPLATE]`

>使用mktemp 命令生成临时文件时，文件名参数应当以"文件名.XXXX"的形式给出，mktemp 会根据文件名参数建立一个临时文件




例如：mktemp test.XXXX


    [root@centos7 ~]# mktemp test.XXX
    test.yXH


**-d：创建临时目录**

    [root@centos7 ~]# mktemp -d test.XXXX
    test.On1N
**-u：干跑，不创建文件测试使用**

*注意：mktemp会将创建的临时文件名直接返回，因此，可直接通过没命令引用保存起来*