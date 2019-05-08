### Linux进程
**进程(Process): 运行中的程序的一个副本**
进程存在生命周期，程序是一个静态的文件

>Linux内核存储进程信息的固定格式：task struct
>多个任务的的task struct组件的链表：task list

#### Linux进程管理的作用
① 判断服务器的健康状态：CPU使用情况、内存使用（物理内存、空闲内存）
② 查看系统中的所有进程：进程的资源占用情况（CPU、Memory）；合法or非法进程
③ 终止进程：常规方式无法关闭程序时，再使用终止进程；对于非法进程，将其对应程序完全清理，达到重启系统不再出现非法进程

#### 进程创建：
init进程：初始化进程，负责总的用户进程空间管理，不能代替内核完成系统调用等一切特权指令的执行
进程：都由其父进程创建，存在父子关系
fork(), clone()

#### 进程优先级：0-139
>1-99：实时优先级；
>100-139：静态优先级；
>数字越小，优先级越高；

>Nice值：
>（-20,19）对应（100，139）
>
>Big O标准：
>O(1)：常数，结果是相同的，输入值不影响时间（空间）
> O(logn)：对数，经典算法：二叉搜索树（AVL数）查找算法
>O(n)：线性复杂度,
>O(n^2), O(2^n)...
#### 调整进程优先级：
>可通过nice值调整的优先级范围：100-139
>分别对应于：-20, 19
>进程启动时，其nice值默认为0，其优先级是120；

##### nice命令：
以指定的nice值启动并运行命令
`nice  [OPTION]  [COMMAND [ARGU]...]`
**选项：**
-n NICE
*注意：仅管理员可调低nice值*

##### renice命令：
调整nice值
`renice  [-n]  NICE  PID...`

**查看Nice值和优先级：**
ps  axo  pid, ni, priority, comm  

#### 进程内存：
Page Frame: 页框，用存储页面数据
MMU：Memory Management Unit

>IPC: Inter Process Communication (进程间通信)
>同一主机上：
>signal
>shm: shared memory
>semerphor
>
>不同主机上：
>rpc: remote procecure call (远程过程调用)
>socket：套接字

**Linux内核：抢占式多任务**
#### 进程类型
- 守护进程（daemon）: 在系统引导过程中启动的进程，跟终端无关的进程
- 前台进程：跟终端相关，通过终端启动的进程

*注意：也可把在前台启动的进程送往后台，以守护模式运行；*

#### 进程状态：
>运行态：running
>就绪态：ready
>睡眠态：
>- 可中断：interruptable
>- 不可中断：uninterruptable
>
>停止态：暂停于内存中，但不会被调度，除非手动启动之；stopped
>僵死态：zombie

#### 僵尸进程的产生、危害及避免方法
**产生原因：**
在子进程终止后到父进程调用wait()前的时间里，子进程被称为zombie；
- 子进程结束后向父进程发出SIGCHLD信号，父进程默认忽略了它
- 父进程没有调用wait()或waitpid()函数来等待子进程的结束
- 网络原因有时会引起僵尸进程；

**危害：**
- 僵尸进程会占用系统资源，如果很多，则会严重影响服务器的性能；
- 孤儿进程不会占用系统资源，最终是由init进程托管，由init进程来释放；

**如何防止僵尸进程：**
- 让僵尸进程成为孤儿进程，由init进程回收；(手动杀死父进程)
- 调用fork()两次；
- 捕捉SIGCHLD信号，并在信号处理函数中调用wait函数；
#### 进程的分类：
- CPU-Bound
- IO-Bound
                    

#### Linux系统上的进程查看工具
CentOS 5:  SysV init
CentOS 6：upstart
CentOS 7：systemd
##### pstree命令：
pstree  - display a tree of processes
```
[root@CentOS7 ~]# pstree
systemd─┬─NetworkManager─┬─dhclient
        │                └─2*[{NetworkManager}]
        ├─VGAuthService
        ├─atd
        ├─auditd───{auditd}
        ├─crond
        ├─dbus-daemon───{dbus-daemon}
        ├─firewalld───{firewalld}
        ├─irqbalance
        ├─login───bash
        ├─lvmetad
        ├─master─┬─pickup
        │        └─qmgr
        ├─polkitd───6*[{polkitd}]
        ├─rsyslogd───2*[{rsyslogd}]
        ├─sshd───sshd───bash───pstree
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-udevd
        ├─tuned───4*[{tuned}]
        └─vmtoolsd───{vmtoolsd}
```
```
[root@CentOS6 ~]# pstree
init─┬─NetworkManager─┬─dhclient
     │                └─{NetworkManager}
     ├─abrtd
     ├─acpid
     ├─atd
     ├─auditd───{auditd}
     ├─automount───4*[{automount}]
     ├─bluetoothd
     ├─bonobo-activati───{bonobo-activat}
     ├─certmonger
     ├─clock-applet───{clock-applet}
     ├─console-kit-dae───63*[{console-kit-da}]
     ├─crond
     ├─cupsd
     ├─2*[dbus-daemon───{dbus-daemon}]
     ├─2*[dbus-launch]
     ├─devkit-power-da
     ├─gconf-im-settin
     ├─gconfd-2
     ├─gdm-binary─┬─gdm-simple-slav─┬─Xorg
```
##### ps命令：
**/proc/：内核中的状态信息**
内核参数：
- 可设置其值从而调整内核运行特性的参数；/proc/sys/
- 状态变量：其用于输出内核中统计信息或状态信息，仅用于查看；

参数：模拟成文件系统类型；

**进程：**
>/proc/#：proc进程参数
>\#：PID（进程号）

**启动进程的方式：**
>系统启动过程中自动启动：与终端无关的进程
>用户通过终端启动：与终端相关的进程

格式：ps [options]

**options有三种风格：**
>1   UNIX options, which may be grouped and must be preceded by a dash.
>2   BSD options, which may be grouped and must not be used with a dash.
>3   GNU long options, which are preceded by two dashes.

**Options：**
**a：所有与终端相关的进程**
```
[root@promote 1]# ps a
   PID TTY      STAT   TIME COMMAND
  7514 tty1     Ss+    0:00 -bash
  7540 pts/0    Ss     0:00 -bash
  7831 pts/0    R+     0:00 ps a
```
**x：所有与终端无关的进程**
**u：以用户为中心组织进程状态信息显示**
**常用组合之一：`ps aux`**
```
[root@promote 1]# ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.2  0.3 128240  6872 ?        Ss   11:55   0:03 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
root          2  0.0  0.0      0     0 ?        S    11:55   0:00 [kthreadd]
root          3  0.0  0.0      0     0 ?        S    11:55   0:00 [ksoftirqd/0]
root          5  0.0  0.0      0     0 ?        S<   11:55   0:00 [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        S    11:55   0:00 [kworker/u256:0]
root          7  0.0  0.0      0     0 ?        S    11:55   0:00 [migration/0]
root          8  0.0  0.0      0     0 ?        S    11:55   0:00 [rcu_bh]
root          9  0.0  0.0      0     0 ?        S    11:55   0:01 [rcu_sched]
root         10  0.0  0.0      0     0 ?        S<   11:55   0:00 [lru-add-drain]
root         11  0.0  0.0      0     0 ?        S    11:55   0:00 [watchdog/0]
```
>VSZ：虚拟内存集
>RSS：Resident Size，常驻内存集
>STAT：状态
>- R：running
>- S：interruptable sleeping
>- D：uninterruptable sleeping
>- T：Stopped
>- Z：zombie
>- +：前台进程
>- l：多线程进程
>- N：低优先级进程
>- <：高优先级进程
>- s：session leader

**-e：显示所有进程**
```
[root@promote ~]# ps -e
   PID TTY          TIME CMD
     1 ?        00:00:03 systemd
     2 ?        00:00:00 kthreadd
     3 ?        00:00:00 ksoftirqd/0
```
**-f：显示完整格式的进程信息**
```
[root@promote ~]# ps -f
UID         PID   PPID  C STIME TTY          TIME CMD
root       7905   7901  0 15:46 pts/1    00:00:00 -bash
root       7923   7905  0 15:54 pts/1    00:00:00 ps -f
```
**常用组合之二：-ef**
```
[root@promote ~]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 14:38 ?        00:00:03 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
root          2      0  0 14:38 ?        00:00:00 [kthreadd]
root          3      2  0 14:38 ?        00:00:00 [ksoftirqd/0]
```
**-F：显示完整格式的进程信息**
```
[root@promote ~]# ps -F
UID         PID   PPID  C    SZ   RSS PSR STIME TTY          TIME CMD
root       7905   7901  0 28860  2060   4 15:46 pts/1    00:00:00 -bash
root       7925   7905  0 38840  1864   6 15:55 pts/1    00:00:00 ps -F
```
>C： cpu utilization
>PSR：运行于哪颗CPU之上

**-H：以层级结构显示进程的相关信息**
```
[root@promote ~]# ps -H
   PID TTY          TIME CMD
  7905 pts/1    00:00:00 bash
  7927 pts/1    00:00:00   ps
```
**常用组合之三：-eFH**
```
[root@promote ~]# ps -eFH
UID         PID   PPID  C    SZ   RSS PSR STIME TTY          TIME CMD
root          2      0  0     0     0   0 14:38 ?        00:00:00 [kthreadd]
root          3      2  0     0     0   0 14:38 ?        00:00:00   [ksoftirqd/0]
root          5      2  0     0     0   0 14:38 ?        00:00:00   [kworker/0:0H]
root          6      2  0     0     0   1 14:38 ?        00:00:00   [kworker/u256:0]
```
**常用组合之四：-eo, axo**
o  field1, field2,...：自定义要显示的字段列表，以逗号分隔；
常用的field：`pid, ni, pri, psr, pcpu, stat, comm, tty, ppid, rtprio`
>ni：nice值；
>priority：priority, 优先级；
>rtprio：real time priority，实时优先级；
```
[root@promote ~]# ps axo pid,ni,pri,pcpu,stat,comm,tty,ppid,rtprio
   PID  NI PRI %CPU STAT COMMAND         TT         PPID RTPRIO
     1   0  19  0.0 Ss   systemd         ?             0      -
     2   0  19  0.0 S    kthreadd        ?             0      -
     3   0  19  0.0 S    ksoftirqd/0     ?             2      -
     5 -20  39  0.0 S<   kworker/0:0H    ?             2      -
     6   0  19  0.0 S    kworker/u256:0  ?             2      -
     7   - 139  0.0 S    migration/0     ?             2     99
```

##### pgrep, pkill命令：
>look up or signal processes based on name and other attributes

`pgrep [options] pattern`
`pkill [options] pattern`
**-u uid：effective user**
**-U uid：read user**
```
[root@promote ~]# pgrep -U postfix
7221
7899
```
**-t  TERMINAL：与指定的终端相关的进程**
**-l：显示进程名**
```
[root@promote ~]# pgrep -U postfix -l
7221 qmgr
7899 pickup
```
**-a：显示完整格式的进程名**
```
[root@promote ~]# pgrep -U postfix -a
7221 qmgr -l -t unix -u
7899 pickup -l -t unix -u
```
**-P pid：显示此进程的子进程**
```
[root@promote ~]# pgrep -P 7004
7536
7901
[root@promote ~]# pgrep ssh
7004
7536
7901
```
##### pidof命令：
根据进程名，取其pid
```
[root@promote ~]# pidof sshd
7901 7536 7004
```
##### top命令：
```
[root@promote ~]# top
top - 16:16:50 up  1:38,  3 users,  load average: 0.00, 0.01, 0.05
Tasks: 167 total,   1 running, 166 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1863252 total,  1398828 free,   161300 used,   303124 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used.  1497152 avail Mem 
   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                        
  6439 root      20   0  300720   6336   4976 S   0.7  0.3   0:12.26 vmtoolsd                                                       
    60 root      20   0       0      0      0 S   0.3  0.0   0:05.76 kworker/0:1                                                    
   129 root      20   0       0      0      0 S   0.3  0.0   0:01.61 kworker/1:2  
```
>top 命令输出中的第一行是系统的平均负载，这和 uptime 命令的输出是一样的
>16:16:50 表示系统当前时间
>up  1:38 表示系统最后一次启动后总的运行时间
>3 user 表示当前系统中有3个登录用户
>load average: 0.00, 0.01, 0.05 表示系统的平均负载，最后的三个数字分别表示最后一分钟的系统平均负载，最后五分钟的系统平均负载，最后十五分钟的系统平均负载
>
>Tasks:167 total 表示当前系统的进程总数
>1 running 表示当前系统中有 1 个正在运行的进程
>166 sleeping 表示当前系统中有 166 个休眠的进程
>0 stopped 表示停止状态的进程数为 0
>0 zombie 表示处于僵死状态的进程数为 0
>
>us：进程在用户地址空间中消耗 CPU 时间的百分比
>sy：进程在内核地址空间中消耗 CPU 时间的百分比
>ni：ni 是 nice 的缩写，可以通过 nice 值调整进程用户态的优先级
>id：CPU 处于 idle 状态的百分比。一般情况下， us + ni + id 应该接近 100%
>wa：CPU 等待磁盘 IO 操作的时间
>hi & si：这两个值表示系统处理中断消耗的时间。中断分为硬中断和软中断，hi 表示处理硬中断消耗的时间，si 表示处理软中断消耗的时间
>st：只有 Linux 在作为虚拟机运行时 st 才是有意义的。它表示虚机等待 CPU 资源的时间(虚机分到的是虚拟 CPU，当需要真实的 CPU 时，可能真实的 CPU 正在运行其它虚机的任务，所以需要等待)

**排序：**
>P：以占据CPU百分比排序；
>M：以占据内存百分比排序；
>T：累积占用CPU时间排序；

**首部信息：**
>uptime信息：l命令
>tasks及cpu信息：t命令
>内存信息：m命令
>退出命令：q
>修改刷新时间间隔：s
>终止指定的进程：k

**选项：**
>-d #：指定刷新时间间隔，默认为3秒；
>-b：以批次方式显示；
>-n #：显示多少批次；

##### uptime命令：显示系统时间、运行时长及平均负载；
过去1分钟、5分钟和15分钟的平均负载；
等待运行的进程队列的长度；
```
[root@promote ~]# uptime
 16:16:57 up  1:38,  3 users,  load average: 0.00, 0.01, 0.05
```
#### linux进程管理类命令：
以下常用命令一般系统都没有自带，需要提前配置epel源，再通过yum install安装命令
`yum install epel-release -y`
##### htop命令：
![](https://upload-images.jianshu.io/upload_images/16547068-259ffea935179b9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/900)

**选项：**
>-d #：指定延迟时间间隔
>-u UserName：仅显示指定用户的进程
>-s COLUME：以指定字段进行排序

**子命令：**
>l：显示选定的进程打开的文件列表
>s：跟踪选定的进程的系统调用
>t：以层级关系显示各进程状态
>a：将选定的进程绑定至某指定的CPU核心

##### vmstat命令：
>Report virtual memory statistics

格式：vmstat  [options]  [delay [count]]
```
[root@promote ~]# vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 1318008   2232 384676    0    0     2     1    7    9  0  0 100  0  0
```
**procs：**
>r：等待运行的进程的个数；CPU上等待运行的任务的队列长度
>b：处于不可中断睡眠态的进程个数；被阻塞的任务队列的长度

**memory：**
>swpd：交换内存使用总量
>free：空闲的物理内存总量
>buffer：用于buffer的内存总量
>cache：用于cache的内存总量

**swap**
>si：数据进入swap中的数据速率（kb/s）
>so：数据离开swap的速率(kb/s)

**io**
>bi：从块设备读入数据到系统的速度（kb/s）
>bo：保存数据至块设备的速率（kb/s）

**system**
>in：interrupts，中断速率
>cs：context switch, 上下文 切换的速率

**cpu**
>us： user space
>sy：system
>id：idle
>wa：wait
>st: stolen

**选项：**
-s：显示内存统计数据
```
[root@promote ~]# vmstat -s
      1863252 K total memory
       158648 K used memory
       244444 K active memory
       105680 K inactive memory
      1317696 K free memory
         2232 K buffer memory
       384676 K swap cache
      2097148 K total swap
            0 K used swap
      2097148 K free swap
         3626 non-nice user cpu ticks
           13 nice user cpu ticks
         8834 system cpu ticks
     15175239 idle cpu ticks
         1331 IO-wait cpu ticks
            0 IRQ cpu ticks
          228 softirq cpu ticks
            0 stolen cpu ticks
       258842 pages paged in
       136325 pages paged out
            0 pages swapped in
            0 pages swapped out
      1022214 interrupts
      1274230 CPU context switches
   1556262293 boot time
         8550 forks
```

##### pmap命令：
>report memory map of a process

格式：pmap [options] pid [...]
**-x：显示详细格式的信息**
```
[root@promote ~]# pmap -x 1
1:   /usr/lib/systemd/systemd --system --deserialize 7
Address           Kbytes     RSS   Dirty Mode  Mapping
0000555d074b0000    1412    1132       0 r-x-- systemd
0000555d07810000     140     132     132 r---- systemd
0000555d07833000       4       4       4 rw--- systemd
0000555d09046000    1236    1096    1096 rw---   [ anon ]
00007fd8b3926000     832     832     832 rw---   [ anon ]
```
**另一种查看方式：cat  /proc/PID/maps**

##### glances命令：
>A cross-platform curses-based system monitoring tool

支持C/S架构模式，可以远程查看
![](https://upload-images.jianshu.io/upload_images/16547068-9d94804f771c2430.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/900)

**内建命令：**
```
Glances 2.5.1 with PSutil 2.2.1

Configuration file: None

 a  Sort processes automatically          b  Bytes or bits for network I/O      
 c  Sort processes by CPU%                l  Show/hide alert logs               
 m  Sort processes by MEM%                w  Delete warning alerts              
 u  Sort processes by USER                x  Delete warning and critical alerts 
 p  Sort processes by name                1  Global CPU or per-CPU stats        
 i  Sort processes by I/O rate            I  Show/hide IP module                
 t  Sort processes by TIME                D  Enable/disable Docker stats        
 d  Show/hide disk I/O stats              T  View network I/O as combination    
 f  Show/hide filesystem stats            U  View cumulative network I/O        
 n  Show/hide network stats               F  Show filesystem free space         
 s  Show/hide sensors stats               g  Generate graphs for current history
 2  Show/hide left sidebar                r  Reset history                      
 z  Enable/disable processes stats        h  Show/hide this help screen         
 3  Enable/disable quick look plugin      q  Quit (Esc and Ctrl-C also work)    
 e  Enable/disable top extended stats  
 /  Enable/disable short processes name
 0  Enable/disable Irix process CPU    
```
**常用选项：**
>-b：以Byte为单位显示网上数据速率；
>-d：关闭磁盘I/O模块；
>-m：关闭mount模块；
>-n：关闭network模块；
>-t #：刷新时间间隔；
>-1：每个cpu的相关数据单独显示；
>-o {HTML|CSV}：输出格式；
>-f  /PATH/TO/SOMEDIR：设定输出文件的位置；

**C/S模式下运行glances命令：**
- 服务模式：
  glances  -s  -B  IPADDR
  IPADDR：本机的某地址，用于监听；
- 客户端模式：
  glances  -c  IPADDR
  IPADDR：是远程服务器的地址；

##### dstat命令：
versatile tool for generating system resource statistics
格式：dstat [-afv] [options..] [delay [count]]
![](https://upload-images.jianshu.io/upload_images/16547068-a8a66ded2b111fbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**常用选项：**
> -c， --cpu：显示cpu相关信息
> -C #,#,...,total
>
> -d, --disk：显示磁盘的相关信息
> -D sda,sdb,...,tobal
>
> -g：显示page相关的速率数据
> -m：Memory的相关统计数据
> -n：Interface的相关统计数据
> -p：显示process的相关统计数据
> -r：显示io请求的相关的统计数据
> -s：显示swapped的相关统计数据
>
> 网络相关的
> --tcp
> --udp
> --raw
> --socket
>
> --ipc：进程间通信
>
> --top-cpu：显示最占用CPU的进程
> --top-io：最占用io的进程
> --top-mem：最占用内存的进程
> --top-lantency：延迟最大的进程

##### kill命令：
terminate a process（终止一个进程）
用于向进程发送信号，以实现对进程的管理；

**显示当前系统可用信号：**
`kill -l [signal]`
```
[root@promote ~]# kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX	
```
**每个信号的标识方法有三种：**
1) 信号的数字标识；
2) 信号的完整名称；
3) 信号的简写名称；

**向进程发信号：**
`kill  [-s signal|-SIGNAL]  pid...`
**常用信号：**
>1）SIGHUP：无须关闭进程而让其重读配置文件；
>2）SIGINT：终止正在运行的进程，相当于Ctrl+c
>9）SIGKILL：杀死运行中的进程；
>15）SIGTERM：终止运行中的进程；
>18）SIGCONT：继续运行
>19）SIGSTOP：停止

###### killall命令：
kill processes by name
`killall  [-SIGNAL]  program`
```
[root@promote ~]# killall httpd
[root@promote ~]# ps aux | grep httpd
root       8838  0.0  0.0 112708   976 pts/1    S+   21:21   0:00 grep --color=auto httpd
```
### Linux系统作业控制：
#### job：
>前台作业(foregroud)：通过终端启动，且启动后会一直占据终端；
>后台作业(backgroud)：可以通过终端启动，但启动后即转入后台运行（释放终端）；

**如何让作业运行于后台？**
>(1) 运行中的作业
>Ctrl+z
>注意：送往后台后，作业会转为停止态；
```
[root@promote ~]# htop

[1]+  Stopped                 htop
```
>(2) 尚未启动的作业
>[root@promote ~] # COMMAND  &
>注意：此类作业虽然被送往后台，但其依然与终端相关；
>如果希望把送往后台的作业剥离与终端的关系：
>[root@promote ~] # nohup  COMMAND  &

**查看所有的作业：**
```
[root@promote ~]# jobs
[1]-  Stopped                 htop
[2]+  Stopped                 vim /proc
```
**可实现作业控制的常用命令：**
>[root@promote ~] # fg  [[%]JOB_NUM]：把指定的作业调回前台；
>[root@promote ~] # bg  [[%]JOB_NUM]：让送往后台的作业在后台继续运行；
>[root@promote ~] # kill  %JOB_NUM：终止指定的作业；