### Sysmted：
>POST --> Boot Sequeue(BIOS) --> Bootloader(MBR) --> Kernel(ramdisk) --> rootfs --> /sbin/init
>init：
>CentOS 5: SysV init
>CentOS 6：Upstart
> CentOS 7：Systemd

#### Systemd的新特性：
- ①系统引导时实现服务并行启动
- ②按需激活进程
- ③系统状态快照
- ④基于依赖关系定义服务控制逻辑
            
#### 核心概念：unit
>unit由其相关配置文件进行标识、识别和配置；
>文件中主要包含了系统服务、监听的socket、保存的快照以及其它与init相关的信息；
>
>这些配置文件主要保存在：
>/usr/lib/systemd/system
>/run/systemd/system
>/etc/systemd/system

##### unit的常见类型：

|常见类型|拓展名|含义|
|:--:|:--:|:--:|
|Service unit|.service|用于定义系统服务|
|Target unit|.target|用于模拟实现“运行级别”|
|Device unit| .device|用于定义内核识别的设备|
|Mount unit|.mount|定义文件系统挂载点|
|Socket unit|.socket|用于标识进程间通信用到的socket文件|
|Snapshot unit|.snapshot|管理系统快照|
|Swap unit|.swap| 用于标识swap设备|
|Automount unit|.automount|文件系统自动点设备|
|Path unit| .path|用于定义文件系统中的一文件或目录|

##### 关键特性：
- 基于socket的激活机制：socket与程序分离；
- 基于bus的激活机制；
- 基于device的激活机制；
- 基于Path的激活机制；
- 系统快照：保存各unit的当前状态信息于持久存储设备中；
- 向后兼容sysv init脚本；
  /etc/init.d/               
- 不兼容：
  systemctl的命令是固定不变的；
  非由systemd启动的服务，systemctl无法与之通信；
            

**管理系统服务：**
>CentOS 7： service类型的unit文件；

#### syscemctl命令：
>Control the systemd system and service manager

格式：`systemctl  [OPTIONS...]  COMMAND  [NAME...]`
                    
**启动： service  NAME  start  ==>  systemctl  start  NAME.service**
**停止： service  NAME  stop  ==> systemctl  stop  NAME.service**
**重启： service  NAME  restart  ==>  systemctl  restart  NAME.service**

**状态： service  NAME  status  ==>  systemctl  status  NAME.service**
```
[root@promote ~]# systemctl status httpd.service
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:httpd(8)
           man:apachectl(8)
```
```
[root@promote ~]# systemctl start httpd.service
[root@promote ~]# systemctl status httpd.service
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2019-05-06 19:44:14 CST; 2s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 8471 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─8471 /usr/sbin/httpd -DFOREGROUND
           ├─8472 /usr/sbin/httpd -DFOREGROUND
           ├─8473 /usr/sbin/httpd -DFOREGROUND
           ├─8474 /usr/sbin/httpd -DFOREGROUND
           ├─8475 /usr/sbin/httpd -DFOREGROUND
           └─8476 /usr/sbin/httpd -DFOREGROUND

May 06 19:44:14 promote.cache-dns.local systemd[1]: Starting The Apache HTTP Server...
May 06 19:44:14 promote.cache-dns.local systemd[1]: Started The Apache HTTP Server.
```
**条件式重启：service  NAME  condrestart  ==>  systemctl  try-restart  NAME.service**
**重载或重启服务： systemctl  reload-or-restart  NAME.servcie**
**重载或条件式重启服务：systemctl  reload-or-try-restart  NAME.service**
                        
**查看某服务当前激活与否的状态： systemctl  is-active  NAME.service**
```
[root@promote ~]# systemctl is-active httpd.service
active
[root@promote ~]# systemctl stop httpd.service
[root@promote ~]# systemctl is-active httpd.service
unknown
```
**查看所有已激活的服务：systemctl  list-units  --type  service**
```
[root@promote ~]# systemctl list-units --t  service
UNIT                               LOAD   ACTIVE SUB     DESCRIPTION
atd.service                        loaded active running Job spooling tools
auditd.service                     loaded active running Security Auditing Service
crond.service                      loaded active running Command Scheduler
dbus.service                       loaded active running D-Bus System Message Bus
firewalld.service                  loaded active running firewalld - dynamic firewall daemon
getty@tty1.service                 loaded active running Getty on tty1
irqbalance.service                 loaded active running irqbalance daemon
·······
```
**查看所有服务（已激活及未激活）: chkconfig --lsit  ==>  systemctl  list-units  -t  service  --all**
```
[root@promote ~]# systemctl list-units --t  service --all
  UNIT                                         LOAD      ACTIVE   SUB     DESCRIPTION
  atd.service                                  loaded    active   running Job spooling tools
  auditd.service                               loaded    active   running Security Auditing Service
  cpupower.service                             loaded    inactive dead    Configure CPU power related settings
  crond.service                                loaded    active   running Command Scheduler
  dbus.service                                 loaded    active   running D-Bus System Message Bus
● display-manager.service                      not-found inactive dead    display-manager.service
·······
```
**设置服务开机自启： chkconfig  NAME  on  ==>  systemctl  enable  NAME.service**
**禁止服务开机自启： chkconfig  NAME  off  ==>  systemctl  disable  NAME.service**
**查看某服务是否能开机自启： chkconfig  --list  NAME  ==>  systemctl  is-enabled  NAME.service**
```
[root@promote ~]# systemctl is-enabled httpd.service
disabled
```
**禁止某服务设定为开机自启： systemctl  mask  NAME.service**
**取消此禁止： systemctl  unmask  NAME.servcie**
                        
**查看服务的依赖关系：systemctl  list-dependencies  NAME.service**
```
[root@promote ~]# systemctl list-dependencies httpd.service
httpd.service
● ├─-.mount
● ├─system.slice
● └─basic.target
●   ├─microcode.service
●   ├─rhel-dmesg.service
●   ├─selinux-policy-migrate-local-changes@targeted.service
●   ├─paths.target
●   ├─slices.target
●   │ ├─-.slice
●   │ └─system.slice
●   ├─sockets.target
●   │ ├─dbus.socket
●   │ ├─dm-event.socket
●   │ ├─systemd-initctl.socket
●   │ ├─systemd-journald.socket
●   │ ├─systemd-shutdownd.socket
●   │ ├─systemd-udevd-control.socket
●   │ └─systemd-udevd-kernel.socket
●   ├─sysinit.target
●   │ ├─dev-hugepages.mount
●   │ ├─dev-mqueue.mount
●   │ ├─kmod-static-nodes.service
●   │ ├─lvm2-lvmetad.socket
●   │ ├─lvm2-lvmpolld.socket
●   │ ├─lvm2-monitor.service
●   │ ├─plymouth-read-write.service
●   │ ├─plymouth-start.service
●   │ ├─proc-sys-fs-binfmt_misc.automount
●   │ ├─rhel-autorelabel.service
●   │ ├─rhel-domainname.service
······
```

##### 管理target units：
>运行级别：
>0  ==>  runlevel0.target,  poweroff.target
>1  ==>  runlevel1.target,  rescue.target
>2  ==>  runlevel2.tartet,  multi-user.target
>3  ==>  runlevel3.tartet,  multi-user.target
>4  ==>  runlevel4.tartet,  multi-user.target
>5  ==>  runlevel5.target,  graphical.target
>6  ==>  runlevel6.target,  reboot.target

>级别切换： init  N  ==>  systemctl  isolate  NAME.target
>查看级别： runlevel  ==>  systemctl  list-units  --type  target
>查看所有级别： systemctl  list-units  -t  target  -a
>获取默认运行级别：systemctl  get-default  
>修改默认运行级别： systemctl  set-default   NAME.target
>切换至紧急救援模式： systemctl  rescue
>切换至emergency模式： systemctl  emergency

**其它常用命令：**
>关机： systemctl  halt,  systemctl  poweroff
>重启： systemctl  reboot
>挂起： systemctl  suspend
>快照： systemctl  hibernate
>快照并挂起： systemctl  hybrid-sleep

#### service unit file：
```
[root@promote system]# less httpd.service
[Unit]
Description=The Apache HTTP Server
After=network.target remote-fs.target nss-lookup.target
Documentation=man:httpd(8)
Documentation=man:apachectl(8)

[Service]
Type=notify
EnvironmentFile=/etc/sysconfig/httpd
ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND
ExecReload=/usr/sbin/httpd $OPTIONS -k graceful
ExecStop=/bin/kill -WINCH ${MAINPID}
# We want systemd to give httpd some time to finish gracefully, but still want
# it to kill httpd after TimeoutStopSec if something went wrong during the
# graceful stop. Normally, Systemd sends SIGTERM signal right after the
# ExecStop, which would kill httpd. We are sending useless SIGCONT here to give
# httpd time to finish.
KillSignal=SIGCONT
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```
>文件通常由三部分组成：
>[Unit]：定义与Unit类型无关的通用选项；用于提供unit的描述信息、unit行为及依赖关系等；
>[Service]：与特定类型相关的专用选项；此处为Service类型；
>[Install]：定义由“systemctl  enable”以及"systemctl  disable“命令在实现服务启用或禁用时用到的一些选项；

##### Unit段的常用选项：
>Description：描述信息； 意义性描述
>After：定义unit的启动次序；表示当前unit应该晚于哪些unit启动；其功能与Before相反
>Requies：依赖到的其它units；强依赖，被依赖的units无法激活时，当前unit即无法激活
>Wants：依赖到的其它units；弱依赖；被依赖的units无法激活时，不妨碍当前units激活
>Conflicts：定义units间的冲突关系

##### Service段的常用选项：
>Type：用于定义影响ExecStart及相关参数的功能的unit进程启动类型；

>类型：
>simple：默认值，表示由ExecStart指明的进程所启动起来进程为主进程
>forking：由ExecStart所启动的进程生成的一个子进程为主，父进程退出
>oneshot：一次性的启动，后续的unit进程启动后，该进程退出
>dbus：仅在得到dbus之后才推出
>notify：发送通知以后才能运行
>idle：类似于simple
>EnvironmentFile：环境配置文件
>ExecStart：指明启动unit要运行命令或脚本； ExecStartPre, ExecStartPost
>ExecStop：指明停止unit要运行的命令或脚本
>Restart：启动此项，意外终止会自动重启脚本

##### Install段的常用选项：
>Alias：当前unit的别名
>RequiredBy：被哪些units所依赖；
>WantedBy：被哪些units所依赖；

**注意：对于新创建的unit文件或，修改了的unit文件，要通知systemd重载此配置文件**
`systemctl  daemon-reload`

**练习1：systemd查看日志文件有隐藏该如何处理？**

```
systemctl status SERVICE -l  # -l选项显示完整选项
journalctl -u  SERVICE # 使用journalct命令查看
```
**练习2：自己动手写一个systemd的配置文件， 让nginx服务可以开机启动**
```
[root@promote ~]# vim /lib/systemd/system/nginx-test.service
[Unit]
Description=Test Service

[Service]
Type=forking
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target

[root@promote ~]# systemctl enable nginx-test
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx-test.service to /usr/lib/systemd/system/nginx-test.service.
```