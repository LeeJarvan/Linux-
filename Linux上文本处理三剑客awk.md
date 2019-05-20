### GNU awk：gawk
>文本处理三工具：grep, sed, awk
>grep, egrep, fgrep：文本过滤工具
>sed: 行编辑器
>awk：报告生成器，格式化文本输出

**AWK: Aho, Weinberger, Kernighan（作者三个人名字） --> New AWK, NAWK**

#### gawk - pattern scanning and processing language
>基本用法：`gawk [options] 'program' FILE ...`
>`program: PATTERN{ACTION STATEMENTS}`
>语句之间用分号分隔

**options：**
**-F：指明输入时用到的字段分隔符**
```
[root@promote ~]# awk -F: '{print $1}' /etc/passwd
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games
ftp
nobody
systemd-network
dbus
polkitd
sshd
postfix
centos
tcpdump
apache
tss
geoclue
```
**-v var=value: 自定义变量**

#### 常用输出命令
##### print命令
格式：`print item1, item2, ...`

>要点：
>(1) 使用逗号分隔符
>(2) 输出的各item可以字符串，也可以是数值；当前记录的字段、变量或awk的表达式
>(3) 如省略item，相当于print $0

```
[root@promote ~]# tail -5 /etc/fstab | awk '{print $2,$4}'
 
/ defaults
/boot defaults
/home defaults
swap defaults
[root@promote ~]# tail -5 /etc/fstab | awk '{print "hello",$2,$4}'
hello  
hello / defaults
hello /boot defaults
hello /home defaults
hello swap defaults
[root@promote ~]# awk -v FS=':' -v OFS=':' '{print $1,$3,$7}' /etc/passwd
root:0:/bin/bash
bin:1:/sbin/nologin
daemon:2:/sbin/nologin
adm:3:/sbin/nologin
lp:4:/sbin/nologin
sync:5:/bin/sync
shutdown:6:/sbin/shutdown
halt:7:/sbin/halt
mail:8:/sbin/nologin
operator:11:/sbin/nologin
games:12:/sbin/nologin
ftp:14:/sbin/nologin
nobody:99:/sbin/nologin
systemd-network:192:/sbin/nologin
dbus:81:/sbin/nologin
polkitd:999:/sbin/nologin
sshd:74:/sbin/nologin
postfix:89:/sbin/nologin
centos:1000:/bin/bash
tcpdump:72:/sbin/nologin
apache:48:/sbin/nologin
tss:59:/sbin/nologin
geoclue:998:/sbin/nologin
```
##### printf命令
格式化输出：`printf FORMAT, item1, item2, ...`

>(1) FORMAT必须给出;
>(2) 不会自动换行，需要显式给出换行控制符，\n
>(3) FORMAT中需要分别为后面的每个item指定一个格式化符号；

| 格式符 |              作用              |
| :----: | :----------------------------: |
|   %c   |       显示字符的ASCII码        |
| %d, %i |          示十进制整数          |
|   %c   |       显示字符的ASCII码        |
| %e, %E |       科学计数法数值显示       |
|   %f   |          显示为浮点数          |
| %g, %G | 以科学计数法或浮点形式显示数值 |
|   %s   |           显示字符串           |
|   %u   |           无符号整数           |
|   %%   |           显示%自身            |

```
[root@promote ~]# awk -F: '{printf "Username: %s, UID: %d\n",$1,$3}' /etc/passwd
Username: root, UID: 0
Username: bin, UID: 1
Username: daemon, UID: 2
Username: adm, UID: 3
Username: lp, UID: 4
Username: sync, UID: 5
Username: shutdown, UID: 6
Username: halt, UID: 7
Username: mail, UID: 8
Username: operator, UID: 11
Username: games, UID: 12
Username: ftp, UID: 14
Username: nobody, UID: 99
Username: systemd-network, UID: 192
Username: dbus, UID: 81
Username: polkitd, UID: 999
Username: sshd, UID: 74
Username: postfix, UID: 89
Username: centos, UID: 1000
Username: tcpdump, UID: 72
Username: apache, UID: 48
Username: tss, UID: 59
Username: geoclue, UID: 998
```

| 修饰符 |                        作用                         |
| :----: | :-------------------------------------------------: |
| #[.#]  | 第一个数字控制显示的宽度；第二个#表示小数点后的精度 |
|   -    |                       左对齐                        |
|   +    |                   显示数值的符号                    |

```
[root@promote ~]# awk -F: '{printf "Username: %-15s, UID: %d\n",$1,$3}' /etc/passwd
Username: root           , UID: 0
Username: bin            , UID: 1
Username: daemon         , UID: 2
Username: adm            , UID: 3
Username: lp             , UID: 4
Username: sync           , UID: 5
Username: shutdown       , UID: 6
Username: halt           , UID: 7
Username: mail           , UID: 8
Username: operator       , UID: 11
Username: games          , UID: 12
Username: ftp            , UID: 14
Username: nobody         , UID: 99
Username: systemd-network, UID: 192
Username: dbus           , UID: 81
Username: polkitd        , UID: 999
Username: sshd           , UID: 74
Username: postfix        , UID: 89
Username: centos         , UID: 1000
```

#### 变量
##### 内建变量

| 变量名 |          全称          |      作用      |
| :----: | :--------------------: | :------------: |
|   FS   | input field seperator  | 默认为空白字符 |
|  OFS   | output field seperator | 默认为空白字符 |
```
[root@promote ~]# awk -v FS=':' '{print $1}' /etc/passwd
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games
ftp
nobody
systemd-network
dbus
polkitd
sshd
postfix
centos
tcpdump
apache
tss
geoclue
```
| 变量名 |          全称           |      作用      |
| :----: | :---------------------: | :------------: |
|   RS   | input record seperator  | 输入时的换行符 |
|  ORS   | output record seperator | 输出时的换行符 |
```
[root@promote ~]# awk -v rs=' ' '{print}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
centos:x:1000:1000::/home/centos:/bin/bash
tcpdump:x:72:72::/:/sbin/nologin
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
geoclue:x:998:996:User for geoclue:/var/lib/geoclue:/sbin/nologin
```
| 变量名 |       全称       |         作用         |
| :----: | :--------------: | :------------------: |
|   NF   | number of field  |     每行字段数量     |
|   NR   | number of record | 行数（对行统一计数） |
|  FNR   |                  | 各文件分别计数；行数 |
注意：{print NF}, {print $NF}区别

```
[root@promote ~]# awk '{print NF}' /etc/fstab 
0
1
2
10
1
9
12
1
6
6
6
6
[root@promote ~]# awk '{print NR}' /etc/fstab 
1
2
3
4
5
6
7
8
9
10
11
12
```
|  变量名  |    作用    |
| :------: | :--------: |
| FILENAME | 当前文件名 |

```
[root@promote ~]# awk '{print FILENAME}' /etc/fstab 
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
/etc/fstab
```
| 变量名 |                作用                |
| :----: | :--------------------------------: |
|  ARGC  |          命令行参数的个数          |
|  ARGV  | 数组，保存的是命令行所给定的各参数 |
```
[root@promote ~]# awk 'BEGIN{print ARGC}' /etc/fstab 
2
[root@promote ~]# awk 'BEGIN{print ARGV[0]}' /etc/fstab 
awk
[root@promote ~]# awk 'BEGIN{print ARGV[1]}' /etc/fstab 
/etc/fstab
```

##### 自定义变量
>(1) -v var=value
>变量名区分字符大小写；

>(2) 在program中直接定义

```
[root@promote ~]# awk 'BEGIN{test="hello gawk";print test}'
hello gawk
```


#### 操作符

|   操作符名   |               操作符               |
| :----------: | :--------------------------------: |
|  算术操作符  | x+y, x-y, x*y, x/y, x^y, x%y,-x,+x |
| 字符串操作符 |    没有符号的操作符，字符串连接    |
|  赋值操作符  | >=, +=, -=, *=, /=, %=, ^=,++, --  |
|  比较操作符  |        >, >=, <, <=, !=, ==        |
|  模式匹配符  |    \~：是否匹配，!~：是否不匹配    |
|  逻辑操作符  |             &&,\|\|,!              |
|   函数调用   |  function_name(argu1, argu2, ...)  |


##### 条件表达式：
>selector?if-true-expression:if-false-expression

**示例：如果 id号大于1000，用户为普通用户，否则就为系统管理员**

```
[root@promote ~]# awk -F: '{$3>=1000?usertype="Common User":usertype="Sysadmin or SysUser";printf "%15s:%-s\n",$1,usertype}' /etc/passwd
```
#### PATTERN
>(1) empty：空模式，匹配每一行；
>(2) /regular expression/：仅处理能够被此处的模式匹配到的行；
>
>(3) relational expression: 关系表达式；结果有“真”有“假”；结果为“真”才会被处理；
>真：结果为非0值，非空字符串；
```
[root@promote ~]# awk -F: '$3<1000{print $1,$3}' /etc/passwd
root 0
bin 1
daemon 2
adm 3
lp 4
sync 5
shutdown 6
halt 7
mail 8
operator 11
games 12
ftp 14
nobody 99
systemd-network 192
dbus 81
polkitd 999
sshd 74
postfix 89
tcpdump 72
apache 48
tss 59
geoclue 998
[root@promote ~]# awk -F: '$NF=="/bin/bash"{print $1,$NF}' /etc/passwd
root /bin/bash
centos /bin/bash
```

>(4) line ranges：行范围
>startline,endline：/pat1/,/pat2/
>注意： 不支持直接给出数字的格式

```
[root@promote ~]# awk -F: '/^b/,/^g/{print $1}' /etc/passwd
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games
[root@promote ~]# awk -F: '(NR>=2&&NR<=10){print $1}' /etc/passwd
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
```

>(5) BEGIN/END模式
>BEGIN{}: 仅在开始处理文件中的文本之前执行一次；
>END{}：仅在文本处理完成之后执行一次；

```
[root@promote ~]# awk -F: 'BEGIN{print" username  uid   \n----------------"}{print $1,$3}END{print" ==============\n  end         "}' /etc/passwd
 username  uid   
----------------
root 0
bin 1
daemon 2
adm 3
lp 4
sync 5
shutdown 6
halt 7
mail 8
operator 11
games 12
ftp 14
nobody 99
systemd-network 192
dbus 81
polkitd 999
sshd 74
postfix 89
centos 1000
tcpdump 72
apache 48
tss 59
geoclue 998
 ==============
  end         
```
#### 常用的action
>(1) Expressions
>(2) Control statements：if, while等；
>(3) Compound statements：组合语句；
>(4) input statements
>(5) output statements

#### 控制语句
##### if-else
语法：`if(condition) statement [else statement]`

>使用场景：对awk取得的整行或某个字段做条件判断

```
[root@promote ~]# awk -F: '{if($3>=1000) {printf "Common user: %s\n",$1} else {printf "root or Sysuser: %s\n",$1}}' /etc/passwd
root or Sysuser: root
root or Sysuser: bin
root or Sysuser: daemon
root or Sysuser: adm
root or Sysuser: lp
root or Sysuser: sync
root or Sysuser: shutdown
root or Sysuser: halt
root or Sysuser: mail
root or Sysuser: operator
root or Sysuser: games
root or Sysuser: ftp
root or Sysuser: nobody
root or Sysuser: systemd-network
root or Sysuser: dbus
root or Sysuser: polkitd
root or Sysuser: sshd
root or Sysuser: postfix
Common user: centos
root or Sysuser: tcpdump
root or Sysuser: apache
root or Sysuser: tss
root or Sysuser: geoclue

[root@promote ~]# awk -F: '{if($NF=="/bin/bash") print $1}' /etc/passwd
root
centos

[root@promote ~]# awk '{if(NF>5) print $0}' /etc/fstab
# Created by anaconda on Sun Apr 14 09:29:51 2019
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0

[root@promote ~]# df -h
Filesystem                       Size  Used Avail Use% Mounted on
/dev/mapper/centos_promote-root   50G  1.7G   49G   4% /
devtmpfs                         898M     0  898M   0% /dev
tmpfs                            910M     0  910M   0% /dev/shm
tmpfs                            910M  9.6M  901M   2% /run
tmpfs                            910M     0  910M   0% /sys/fs/cgroup
/dev/mapper/centos_promote-home   67G   33M   67G   1% /home
/dev/sda1                       1014M  146M  869M  15% /boot
tmpfs                            182M     0  182M   0% /run/user/0
[root@promote ~]# df -h | awk -F[%] '/^\/dev/{print $1}' | awk '{if($NF>=10) print $1}'
/dev/sda1
```
##### while循环
语法：`while(condition) statement`
条件“真”，进入循环；条件“假”，退出循环；

>使用场景：对一行内的多个字段逐一类似处理时使用；对数组中的各元素逐一处理时使用；

```
[root@promote ~]# awk '/^[[:space:]]*linux16/{i=1;while(i<=NF) {print $i,length($i); i++}}' /etc/grub2.cfg
linux16 7
/vmlinuz-3.10.0-957.el7.x86_64 30
root=/dev/mapper/centos_promote-root 36
ro 2
crashkernel=auto 16
rd.lvm.lv=centos_promote/root 29
rd.lvm.lv=centos_promote/swap 29
rhgb 4
quiet 5
LANG=en_US.UTF-8 16
linux16 7
/vmlinuz-0-rescue-6f14150e17f24a19917ff162dd467b32 50
root=/dev/mapper/centos_promote-root 36
ro 2
crashkernel=auto 16
rd.lvm.lv=centos_promote/root 29
rd.lvm.lv=centos_promote/swap 29
rhgb 4
quiet 5

[root@promote ~]# awk '/^[[:space:]]*linux16/{i=1;while(i<=NF) {if(length($i)>=7) {print $i,length($i)}; i++}}' /etc/grub2.cfg
linux16 7
/vmlinuz-3.10.0-957.el7.x86_64 30
root=/dev/mapper/centos_promote-root 36
crashkernel=auto 16
rd.lvm.lv=centos_promote/root 29
rd.lvm.lv=centos_promote/swap 29
LANG=en_US.UTF-8 16
linux16 7
/vmlinuz-0-rescue-6f14150e17f24a19917ff162dd467b32 50
root=/dev/mapper/centos_promote-root 36
crashkernel=auto 16
rd.lvm.lv=centos_promote/root 29
rd.lvm.lv=centos_promote/swap 29
```
##### do-while循环
语法：`do statement while(condition)`
意义：至少执行一次循环体

##### for循环
语法：`for(expr1;expr2;expr3) statement`
`for(variable assignment;condition;iteration process) {for-body}`
```
[root@promote ~]# awk '/^[[:space:]]*linux16/{for(i=1;i<=NF;i++) {print $i,length($i)}}' /etc/grub2.cfg
linux16 7
/vmlinuz-3.10.0-957.el7.x86_64 30
root=/dev/mapper/centos_promote-root 36
ro 2
crashkernel=auto 16
rd.lvm.lv=centos_promote/root 29
rd.lvm.lv=centos_promote/swap 29
rhgb 4
quiet 5
LANG=en_US.UTF-8 16
linux16 7
/vmlinuz-0-rescue-6f14150e17f24a19917ff162dd467b32 50
root=/dev/mapper/centos_promote-root 36
ro 2
crashkernel=auto 16
rd.lvm.lv=centos_promote/root 29
rd.lvm.lv=centos_promote/swap 29
rhgb 4
quiet 5
```

**特殊用法：**
能够遍历数组中的元素；
语法：`for(var in array) {for-body}`

##### switch语句
语法：`switch(expression) {case VALUE1 or /REGEXP/: statement; case VALUE2 or /REGEXP2/: statement; ...; default: statement}`

##### break和continue
`break [n]`
`continue`

##### next
提前结束对本行的处理而直接进入下一行

**示例：**
取出id号为偶数的用户
```
[root@promote ~]# awk -F: '{if($3%2!=0) next; print $1,$3}' /etc/passwd
root 0
daemon 2
lp 4
shutdown 6
mail 8
games 12
ftp 14
systemd-network 192
sshd 74
centos 1000
tcpdump 72
apache 48
geoclue 998
```
#### array
关联数组：`array[index-expression]`
>index-expression:
>(1) 可使用任意字符串；字符串要使用双引号；
>(2) 如果某数组元素事先不存在，在引用时，awk会自动创建此元素，并将其值初始化为“空串”；
>若要判断数组中是否存在某元素，要使用"index in array"格式进行；

**若要遍历数组中的每个元素，要使用for循环**
`for(var in array) {for-body}`
```
[root@promote ~]# awk 'BEGIN{weekdays["mon"]="Monday";weekdays["tue"]="Tuesday";for(i in weekdays) {print weekdays[i]}}'
Tuesday
Monday
```
注意：var会遍历array的每个索引；

**state["LISTEN"]++
state["ESTABLISHED"]++**
```
[root@promote ~]# netstat -tan | awk '/^tcp\>/{state[$NF]++}END{for(i in state) { print i,state[i]}}'
LISTEN 2
ESTABLISHED 2

[root@promote ~]# awk '{ip[$1]++}END{for(i in ip) {print i,ip[i]}}' /var/log/httpd/access_log
```
**示例：用awk查看tcp连接处于TIMEOUT的连接个数**
```
[root@promote ~]# netstat -tan | awk '/TIMEOUT/{state[$NF]}END{for(i in state){print i,state[i]}}'
```

练习1：统计/etc/fstab文件中每个文件系统类型出现的次数；

```
[root@promote ~]# awk '/^UUID/{fs[$4]++}END{for(i in fs) {print i,fs[i]}}' /etc/fstab
defaults 1
```

练习2：统计指定文件中每个单词出现的次数；

```
[root@promote ~]#  awk '{for(i=1;i<=NF;i++){count[$i]++}}END{for(i in count) {print i,count[i]}}' /etc/fstab
man 1
and/or 1
maintained 1
xfs 3
14 1
Accessible 1
# 7
Apr 1
are 1
defaults 4
blkid(8) 1
/ 1
0 8
See 1
Created 1
/dev/mapper/centos_promote-root 1
on 1
mount(8) 1
anaconda 1
fstab(5), 1
/dev/mapper/centos_promote-home 1
/boot 1
findfs(8), 1
/home 1
2019 1
'/dev/disk' 1
by 2
/etc/fstab 1
/dev/mapper/centos_promote-swap 1
09:29:51 1
pages 1
more 1
UUID=273deb66-d03c-457f-8b29-5df019b3e53a 1
info 1
swap 2
Sun 1
filesystems, 1
reference, 1
for 1
under 1
```

#### 函数
##### 内置函数
**数值处理：**
rand()：返回0和1之间一个随机数；

```
[root@promote ~]# awk 'BEGIN{print rand()}'
0.237788
```

##### 字符串处理：

|     字符串     |                             作用                             |
| :------------: | :----------------------------------------------------------: |
|  length([s])   |                     返回指定字符串的长度                     |
|  sub(r,s,[t])  | 以r表示的模式来查找t所表示的字符中的匹配的内容，并将其第一次出现替换为s所表示的内容 |
| gsub(r,s,[t])  | 以r表示的模式来查找t所表示的字符中的匹配的内容，并将其所有出现均替换为s所表示的内容 |
| split(s,a[,r]) | 以r为分隔符切割字符s，并将切割后的结果保存至a所表示的数组中  |


**示例1：把每行的第1字段中，第一次出现的小写o替换为大写O；注意：仅替换每行一次出现的**

```
[root@wujunjie ~]# awk -F: '{sub(o,O,$1)}' /etc/passwd
```
**示例2：显示来访的主机地址连接的次数**

```
[root@promote ~]#  netstat -tan | awk '/^tcp\>/{split($5,ip,":");count[ip[1]]++}END{for (i in count) {print i,count[i]}}'
192.168.0.101 1
0.0.0.0 2
```

##### 自定义函数
