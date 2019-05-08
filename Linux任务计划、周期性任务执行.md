**未来的某时间点执行一次某任务：at, batch**

**周期性运行某任务：crontab**

**执行结果：会通过邮件发送给用户**
#### 检查邮件服务是否开启（25端口）

```
[root@promote ~]# netstat -tnlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      6970/sshd           
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      7270/master         
tcp6       0      0 :::22                   :::*                    LISTEN      6970/sshd           
tcp6       0      0 ::1:25                  :::*                    LISTEN      7270/master       

[root@promote ~]# ss -tnl
State       Recv-Q Send-Q                     Local Address:Port                                    Peer Address:Port              
LISTEN      0      128                                    *:22                                                 *:*                  
LISTEN      0      100                            127.0.0.1:25                                                 *:*                  
LISTEN      0      128                                   :::22                                                :::*                  
LISTEN      0      100                                  ::1:25                                                :::*      

```
#### 本地电子邮件服务：
>smtp：simple mail transmission protocol
>pop3：Post Office Procotol
>imap4：Internet Mail Access Procotol

##### mail命令：
mailx - send and receive Internet mail（CentOS7）
MUA：Mail User Agent, 用户收发邮件的工具程序
`mailx  [-s 'SUBJECT']  username[@hostname]`

##### 邮件正文的生成：

**(1) 交互式输入；. 单独成行可以表示正文结束；Ctrl+d提交亦可；**
```
[root@promote ~]# mail -s 'hello centos' centos
how are you
.
EOT
[root@promote ~]# su - centos
[centos@promote ~]$ mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/centos": 1 message 1 new
>N  1 root                  Sun Apr 14 19:00  18/638   "hello centos"
& 1
Message  1:
From root@promote.cache-dns.local  Sun Apr 14 19:00:11 2019
Return-Path: <root@promote.cache-dns.local>
X-Original-To: centos
Delivered-To: centos@promote.cache-dns.local
Date: Sun, 14 Apr 2019 19:00:11 +0800
To: centos@promote.cache-dns.local
Subject: hello centos
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
From: root@promote.cache-dns.local (root)
Status: R

how are you

& q
Held 1 message in /var/spool/mail/centos
You have mail in /var/spool/mail/centos
```
**(2) 通过输入重定向**
```
[centos@promote ~]$ mail -s 'fatab file' root < /etc/fstab 
[centos@promote ~]$ exit
logout
You have mail in /var/spool/mail/root
[root@promote ~]# mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/root": 1 message 1 new
>N  1 centos@promote.cache  Sun Apr 14 19:04  29/1185  "fatab file"
& 1
Message  1:
From centos@promote.cache-dns.local  Sun Apr 14 19:04:19 2019
Return-Path: <centos@promote.cache-dns.local>
X-Original-To: root
Delivered-To: root@promote.cache-dns.local
Date: Sun, 14 Apr 2019 19:04:19 +0800
To: root@promote.cache-dns.local
Subject: fatab file
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
From: centos@promote.cache-dns.local
Status: R


#
# /etc/fstab
# Created by anaconda on Sun Apr 14 09:29:51 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0

& q
Held 1 message in /var/spool/mail/root
```
**(3) 通过管道**
```
[root@promote ~]# cat /etc/fstab | mail -s 'to you' centos
[root@promote ~]# su - centos
Last login: Sun Apr 14 19:00:31 CST 2019 on pts/1
[centos@promote ~]$ mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/centos": 2 messages 1 new
    1 root                  Sun Apr 14 19:00  19/649   "hello centos"
>N  2 root                  Sun Apr 14 19:05  29/1185  "to you"
& 2
```
#### 未来的某时间点执行一次某任务
#####at命令：
格式：at  [OPTION]... TIME

**TIME：**
>HH:MM [YYYY-mm-dd]
>noon，midnight, teatime
>tomorrow
>now+#
>UNIT：minutes, hours, days, weeks
```
[root@promote ~]# at now+1min
at> ls /var
at> <EOT>    #crtl+d提交
job 2 at Sun Apr 14 19:19:00 2019
[root@promote ~]# at -l
2	Sun Apr 14 19:19:00 2019 a root
[root@promote ~]# mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/root": 2 messages 1 new
    1 centos@promote.cache  Sun Apr 14 19:04  30/1196  "fatab file"
>N  2 root                  Sun Apr 14 19:18  14/527   "Output from your job        3"
```
**若未安装at命令，通过以下步骤完成**
```
[root@promote ~]# yum -y install at
[root@promote ~]# chkconfig --level 35 atd on
Note: Forwarding request to 'systemctl enable atd.service'.
[root@promote ~]# service atd start
Redirecting to /bin/systemctl start atd.service
```
**at的作业有队列，用单个字母表示，默认都使用a队列**

**常用选项：**
**-l：查看作业队列，相当于atq**
```
[root@promote ~]# at -l
2	Sun Apr 14 19:19:00 2019 a root
```
**-f /PATH/FROM/SOMEFILE：从指定文件中读取作业任务，而不用再交互式输入**
```
[root@promote ~]# at -f at.tasks now+5min
job 3 at Sun Apr 14 19:32:00 2019
[root@promote ~]# atq
3	Sun Apr 14 19:32:00 2019 a root
```
**-d：删除指定的作业，相当于atrm**
```
[root@promote ~]# at -f at.tasks now+5min
job 9 at Sun Apr 14 20:00:00 2019
[root@promote ~]# at -d 9
[root@promote ~]# at -l
```
**-c：查看指定作业的具体内容**
```
[root@promote ~]# at -f at.tasks now+5min
job 10 at Sun Apr 14 20:02:00 2019
[root@promote ~]# at -c 10
#!/bin/sh
# atrun uid=0 gid=0
# mail root 0
umask 22
XDG_SESSION_ID=7; export XDG_SESSION_ID
HOSTNAME=promote.cache-dns.local; export HOSTNAME
SELINUX_ROLE_REQUESTED=; export SELINUX_ROLE_REQUESTED
SHELL=/bin/bash; export SHELL
ls /etc/fstab
echo 'hello ljw'
marcinDELIMITER72ad1e8e
```
**-q QUEUE：指明队列**

*注意：作业执行结果是以邮件发送给提交作业的用户*

##### batch命令：
**batch会让系统自行选择在系统资源较空闲的时间去执行指定的任务；**

#### 周期性任务计划：cron
**服务程序：**
cronie：主程序包，提供了crond守护进程及相关辅助工具；

**确保crond守护进程(daemon)处于运行状态：**

CentOS 7：
```
[root@promote ~]# systemctl status crond.service
● crond.service - Command Scheduler
   Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2019-04-14 09:37:51 CST; 10h ago
 Main PID: 6415 (crond)
   CGroup: /system.slice/crond.service
           └─6415 /usr/sbin/crond -n

Apr 14 09:37:51 localhost.localdomain systemd[1]: Started Command Scheduler.
Apr 14 09:37:51 localhost.localdomain crond[6415]: (CRON) INFO (RANDOM_DELAY will be scaled with factor 96% if used.)
Apr 14 09:37:54 localhost.localdomain crond[6415]: (CRON) INFO (running with inotify support)
You have new mail in /var/spool/mail/root
```

CentOS 6：
```
[root@centos6 ~]# service crond status
crond (pid  2165) is running...
```

**向crond提交作业的方式不同于at，它需要使用专用的配置文件，此文件有固定格式，不建议使用文本编辑器直接编辑此文件；要使用crontab命令；**

##### cron任务分为两类：
**系统cron任务：主要用于实现系统自身的维护**
>手动编辑：/etc/crontab文件

**用户cron任务：用户为了完成自己某个特定作业需要而设定的**
>命令：crontab命令

##### 系统cron的配置格式：/etc/crontab
```
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

**注意：**
>(1) 每一行定义一个周期性任务，共7个字段；
>\*  *  *  *  * : 定义周期性时间
>user-name : 运行任务的用户身份
>command to be executed：任务
>(2) 此处的环境变量不同于用户登录后获得的环境，因此，建议命令使用绝对路径，或者自定义PATH环境变量；
>(3) 执行结果邮件发送给MAILTO指定的用户

##### 用户cron的配置格式：/var/spool/cron/USERNAME
```
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# For details see man 4 crontabs
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  *   command to be executed    
```
**注意：**
>(1) 每行定义一个cron任务，共6个字段；
>(2) 此处的环境变量不同于用户登录后获得的环境，因此，建议命令使用绝对路径，或者自定义PATH环境变量；
>(3) 邮件发送给当前用户；

##### 时间表示法：
>(1) 特定值
>给定时间点有效取值范围内的值；
>注意：day of week和day of month一般不同时使用；
>(2)通配符： *
>给定时间点上有效取值范围内的所有值；表“每..”
>(3) 离散取值：,
>在时间点上使用逗号分隔的多个值；
>\#,#,#
>(4) 连续取值：-
>在时间点上使用-连接开头和结束
>\#-#
>(5) 在指定时间点上，定义步长:
>/#：#即步长；

**注意：**
>(1) 指定的时间点不能被步长整除时，其意义将不复存在；
>(2) 最小时间单位为“分钟”，想完成“秒”级任务，得需要额外借助于其它机制；
>定义成每分钟任务：而在利用脚本实现在每分钟之内，循环执行多次；

**示例：**

> (1) 3 * * * *：每小时执行一次；每小时的第3分钟；
> (2) 3 4 * * 5：每周执行一次；每周5的4点3分；
> (3) 5 6 7 * *：每月执行一次；每月的7号的6点5分；
> (4) 7 8 9 10 *：每年执行一次；每年的10月9号8点7分；
> (5) 9 8 * * 3,7：每周三和周日；
> (6) 0 8,20 * * 3,7：每周三和周日的8点和20点各执行一次
> (7) 0 9-18 * * 1-5：工作时间从9点到18点每小时
> (8) */5 * * * *：每5分钟执行一次某任务；
> (9) */7 * * * *：意义不存在

##### crontab命令：
格式：crontab [-u user] [-l | -r | -e] [-i]
**-e：编辑任务**
```
[centos@promote root]$ crontab -e
*/2 * * * * /bin/echo ;howdy!'
[root@promote ~]# cat /var/spool/cron/centos
*/2 * * * * /bin/echo ;howdy!'
```
**-l：列出所有任务**
```
[centos@promote root]$ crontab -l
*/2 * * * * /bin/echo ;howdy!'
```
**-r：移除所有任务；即删除/var/spool/cron/USERNAME文件**
**-i：在使用-r选项移除所有任务时提示用户确认**
**-u user：root用户可为指定用户管理cron任务**                
```
[root@promote ~]# crontab -u centos -e
```
注意：运行结果以邮件通知给当前用户；如果拒绝接收邮件：
>(1) COMMAND > /dev/null
>(2) COMMAND &> /dev/null

注意：定义COMMAND时，如果命令需要用到%，需要对其转义；但放置于单引号中的%不用转义亦可；

**思考：某任务在指定的时间因关机未能执行，下次开机会不会自动执行？**
不会！.
如果期望某时间因故未能按时执行，下次开机后无论是否到了相应时间点都要执行一次，可使用anacron实现；


练习：
1、每12小时备份一次/etc目录至/backups目录中，保存文件 名称格式为“etc-yyyy-mm-dd-hh.tar.xz”
```
[root@localhost ~]# crontab -l
0 */12 * * * tar -JcPf /backup/`date +%y-%m-%d.tar.xz` /etc
```