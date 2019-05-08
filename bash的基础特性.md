#### bash的基础特性之一：命令历史

命令历史：shell进程会在其会话中保存此前用户提交执行过的命令
```
[root@node1 ~]# history
1  poweroff
2  which
3  which --skip-alias
```
定制history的功能，可通过环境变量实现
HISTSIZE：shell进程可保留的命令历史的条数
```
[root@node1 ~]# echo $HISTSIZE
1000
```
HISTFILE：持久保存命令历史的文件
```
[root@node1 ~]# echo $HISTFILE
/root/.bash_history
```
HISTFILESIZE：命令历史文件的大小
```
[root@node1 ~]# echo $HISTFILESIZE
1000
```
命令用法：
`history [-c] [-d offset] [n]   or history -anrw [filename]   or history -ps arg [arg...]`

**-c：清空历史命令**
```
[root@promote ~]# history -c
[root@promote ~]# history
1  history
```
**-d offset：删除指定历史命令**

    [root@promote ~]# history
    1  history -d 1
    2  ls
    3  ll
    4  history
    [root@promote ~]# history -d 2
    [root@promote ~]# history 
    1  history -d 1
    2  ll
    3  history
    4  history -d 2
    5  history 


​    
**-r：从文件读取历史命令至历史列表中**
**-w：把历史列表中的命令追加至历史文件中**


**history #：显示最近的#条命令**
```
[root@promote ~]# history 2
7  history #
8  history 2
```
**调用历史命令列表中的命令：**

！#：再一次执行历史列表中的第#条命令
```
[root@promote ~]# !31
pwd
/root
```

！！：再一次执行上一条命令
```
[root@promote ~]# ls
anaconda-ks.cfg
[root@promote ~]# !!
ls
anaconda-ks.cfg
```
！STRING：再一次执行历史命令列表中最近一个以STRING开头的命令

*注意：命令的重复执行有时候需要依赖于幂等性*

**调用上一条命令的最后一个参数：**

快捷键：ESC+ .

字符串：！$

**控制命令历史记录的方式：**
环境变量：HISTCONTROL
|字符名称|含义|
|:---:|:---:|
| ignoredups|忽略重复命令|
| ignorespace|忽略以空白字符开头的命令|
|ignoreboth|以上两者同时生效|

修改变量的值：NAME='VALUE'    （仅对当前shell进程有效）

#### bash的基础特性之二：命令补全

**命令补全：
shell程序在接收到用户执行命令的要求，分析完成之后，最左侧的字符串会被当作命令**

命令处理机制：
*  查找内部命令
*  根据PATH环境变量中设定的目录，自左而右逐个搜索目录下的文件名

给定的打头字符串如果能唯一标识某命令程序文件，则直接补全
不能唯一标识的某命令程序文件，再击tab键一次，会给出列表

**路径补全：
在给定的起始路径下，以对应路径下的打头字串来逐一匹配起始路径下的每个文件**
tab：如果能唯一标识，则直接补全
否则，再按一次tab，给出列表

#### bash的基础特性之三：命令行展开（可看第二周作业）
|字符名称|含义|
|:---:|:---:|
|~|自动展开为用户的家目录，或指定的用户的家目录|
|｛｝|可承载一以逗号分割的路径列表，并能将其展开为多个路径|

例如：/tmp/{a,b}相当于 /tmp/a  /tmp/b

例题1：如何创建/tmp/x/y1，/tmp/x/y2，/tmp/x/y1/a，/tmp/x/y1/b
```
 [root@promote ~]# mkdir -pv /tmp/x/{y1/{a,b},y2}
```
例题2：如何创建a_c，a_d，b_c，b_d
```
 [root@promote ~]# mkdir -v {a,b}_{c,d}
```
例题3：创建如下目录结构

    /tmp/mysysroot/
    
    ├── bin
    
    ├── etc
    
    │   └── sysconfig
    
    │       └── network-scripts
    
    ├    ── sbin
    
    ├── usr
    
    │   ├── bin
    
    │   ├── lib
    
    │   ├── lib64
    
    │   ├── local
    
    │   │   ├── bin
    
    │   │   ├── lib
    
    │   │   └── sbin
    
    │   └── sbin
    
    └── var
    
    ├── cache
    
    ├── log
    
    └── run
```
[root@promote ~]# mkdir -pv /tmp/mysysroot/{bin,sbin,etc/sysconfig/network-scripts,usr/{bin,sbin,local/{bin,sbin,lib},lib,lib64},var/{cache,log,run}}
```
#### bash的基础特性之四：命令的执行状态结果

命令执行的状态结果：
bash通过状态返回值来输出此结果

>   **成功：0**
>
>   **失败：1-255**

命令执行完成之后，其状态返回值保存于bash的特殊变量$？中

    [root@promote ~]# lss
    -bash: lss: command not found
    [root@promote ~]# echo $?
    127

命令正常执行时，有的还会有命令返回值
根据命令及其功能不同，结果各不相同
引用命令的执行结果：

>$（COMMAND）
>或‘COMMAND’

    [root@promote ~]# date +%T
    18:42:37
    [root@promote ~]# mkdir $(date +%T)
    [root@promote ~]# ls
    18:43:03  anaconda-ks.cfg


#### bash的基础特性之五：引用

|字符名称|含义|
|:---:|:---:|
|'  '|强引用|
|'' ''|弱引用|
|'' ''|命令引用|

#### bash的基础特性之六：快捷键
|字符名称|含义|
|:---:|:---:|
|ctrl+a|跳转至命令行首部|
|ctrl+e|跳转至命令行尾部|
|ctrl+u|删除行首至光标所在处之间的所有字符|
|ctrl+k|删除光标所在处至行尾的所有字符|
|ctrl+l|清屏，相当于clear|


#### bash的基础特性之七：globbing：文件名通配（整理文件名匹配，而非部分）
**匹配模式：元字符**

*：匹配任意长度的任意字符

`pa*，*pa*，*pa，*p*a`

？：匹配任意单个字符
`pa？，？？pa，p？a，p?a?`

[ ]：匹配指定范围内的任意单个字符

特殊格式：
`[a-z]，[A-Z]，[0-9]，[a-z0-9]，[abcxyz]`

|字符名称|含义|
|:---:|:---:|
| [[:upper:]]|所有大写字母|
| [[:lower:]]|所有小写字母|
|  [[:alpha:]]|所有字母|
| [[:digit:]]|所有数字|
|  [[:alnum]]|清所有字母和数字|
|  [[:space:]]|所有空白字符|
| [[:punct:]]|所有标点符号|
|[^]|匹配指定范围外的任意单个字符|
| [^[:upper:]]|非大写字母的字符|
| [^0-9] |非数字的字符|
| [^[alnum]] |非字母和数字的字符|

练习1：显示/var目录下所有以l开头，以一个小写字母结尾，且中间出现一位任意字符的文件或目录
```
[root@promote ~]# ls -d /var/l?[[:lower:]]
```
练习2：显示/etc目录下，以任意一位数字开头，且以非数字结尾的文件或目录
```
[root@promote ~]# ls -d /etc/[0-9]*[^0-9]
```
练习3：显示/etc目录下，以非字母开头，后面跟一个字母及其他任意长度任意字符的文件或目录
```
[root@promote ~]# ls -d /etc/[^a-z][a-z]*
```
练习4：复制/etc目录下，所有以m开头，以非数字结尾的文件或目录至/tmp/megedu.com目录
```
[root@promote ~]# cp -r /etc/m*[^0-9] /tmp/mgedu.com/
```
练习5：复制/usr/share/man目录下，所有以man开头，后跟一个数字结尾的文件或目录至/tmp/man/目录下
```
[root@promote ~]# cp -r /usr/share/man/man[0-9] /tmp/man/
```
练习6：复制/etc目录下，所有以.conf结尾，且以m，n，r，p开头的文件或目录至/tmp/conf.d/目录下
```
[root@promote ~]# cp -r /etc/[mnrp]*.conf /tmp/conf.d/
```
#### bash的基础特性之八：IO重定向及管道
**程序：指令+数据，IO**

>可用输入的设备：文件
>键盘设备，文件系统上的常规文件，网卡等
>
>可用输出的设备：文件 
>显示器，文件系统上的常规文件，网卡等

**程序的数据流**
* 输入的数据流：<-- 标准输入（stdin），键盘
* 输出的数据流：--> 标准输出（stdout），显示器
* 错误输出流：    --> 标准输出（stderr），显示器

**fd：file descriptor，文件描述符**

    标准输入：0
    标准输出：1
    错误输出：2

**IO重定向：**

* 输出重定向：>
   特性：覆盖输出（覆盖原有文件，慎用）
* 输出重定向：>>
   特性：追加输出（保留原有文件的内容）
>\# set：设置或者撤销shell选项的值
>-C：禁止覆盖输出重定向至已存在的文件
>此时可使用强制覆盖输出：>|
>
>\# set +C：关闭上述特性
>错误输出流重定向：2>，2>>

**合并正常输出流和错误输出流：**
>- &>,&>>
>- COMMAND > /path/to/somefile 2>&1
>    COMMAND >> /path/to/somefile 2>&1

**特殊设备：/dev/null/**

**输入重定向：<**

 **tr：把输入数据当中的数据，凡是在SET1定义范围内出现的，通通对位转换为SET2出现的字符**
`tr SET1 SET2 < /PATH/FROM/SOMEFILE`
```
[root@promote ~]# tr [a-z] [A-Z]
how are you
HOW ARE YOU
```

**tr -d：删除输出到屏幕上的指定字符，不会影响原文件**

`tr -d SET1 < /PATH/FROM/SOMEFILE`
`Here Document：<<`
`cat << EOF`
`cat > /PARH/TO/SOMEFILE << EOF`


    [root@promote ~]# cat > /tmp/cat.out << END
    > how are you
    > END
    [root@promote ~]# cat /tmp/cat.out
    how are you


**管道：连接程序，实现将前一个命令的输出直接定向后一个程序当作输入**
`COMMAND1 | COMMAND2 | COMMAND3 . . .`
```
[root@promote ~]# echo how are you | tr [a-z] [A-Z]
HOW ARE YOU
```

**tee命令：实现将数据分方向发送**
`COMMAND | tee /PATH/TO /SOMEFILE`

**练习：把/etc/passwd文件的前6行的信息转换为大写字符后输出**

    [root@promote ~]# head -6 /etc/passwd | tr [a-z] [A-Z]
    ROOT:X:0:0:ROOT:/ROOT:/BIN/BASH
    BIN:X:1:1:BIN:/BIN:/SBIN/NOLOGIN
    DAEMON:X:2:2:DAEMON:/SBIN:/SBIN/NOLOGIN
    ADM:X:3:4:ADM:/VAR/ADM:/SBIN/NOLOGIN
    LP:X:4:7:LP:/VAR/SPOOL/LPD:/SBIN/NOLOGIN
    SYNC:X:5:0:SYNC:/SBIN:/BIN/SYNC


#### bash的基础特性之九：命令hash

**缓存此前命令的查找结果：key-value（数据格式）**
>key：搜索键
>value：值

**hash命令：**
>hash：列出
>hash -d COMMAND：删除
>hash -r：清空

#### bash的基础特性之十：变量
**程序：指令+数据**
* 指令：由程序文件提供；
* 数据：IO设备、文件、管道、变量

**另一个角度：**
程序：算法+数据结构

>变量=变量名+指向的内存空间
>变量赋值：name=value
>变量类型：存储格式、表示数据范围、参与的运算

>编程语言：
>* 强类型变量：C语言，python（无需事先声明）
>* 弱类型变量：

bash把所有变量统统视作字符型，不支持浮点类型
bash中的变量无需事先声明；相当于，把声明和赋值过程同时实现
声明：说明变量类型，声明变量名称

**变量替换：把变量名出现的位置替换为其所指向的内存空间中数据**
```
[root@promote ~]# first_name=haha
[root@promote ~]# echo $first_name
haha
```
**变量引用：$var_name，一般情况下可以省略括号**
>变量名：变量名只能包含数字、字母和下划线，而且不能以数字开头
>变量名要做到见名知义，命名机制遵循某种法则；不能够使用程序的保留字，例如if, else, then, while等等

bash变量类型：
* 本地变量：作用域仅为当前shell进程
  变量赋值：name=value
  变量引用： $name
```
" "：变量名会替换为其值
' '：变量名不会替换为其值
查看变量：set
撤销变量：unset name
注意：此处非变量引用
```
增加PATH路径

    [root@centos7 ~]# echo $PATH
    /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
    [root@centos7 ~]# PATH="$PATH:/usr/local/apapche/bin"
    [root@centos7 ~]# echo $PATH
    /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:/usr/local/apapche/bin

* 环境变量：作用域为当前shell进程及其子进程


     变量赋值：
      (1) export name=value
      (2) name=value
          export name
      (3) declare -x name=value  声明环境变量
      (4) name=value
          declare -x name

     变量引用：${name}, $name
      注意：bash内嵌了许多环境变量(通常为全大写字符)，用于定义bash的工作环境
     PATH, HISTFILE, HISTSIZE, HISTFILESIZE, HISTCONTROL, SHELL, HOME, UID, PWD, OLDPWD

     查看环境变量：export, declare -x, printenv, env
     撤销环境变量：unset name

只读变量（常量）：
只读变量无法重新赋值，并且不支持撤销；存活时间为当前shell进程的生命周期，随shell进程终止而终止

    (1) declare -r name
    (2) readonly name
* 局部变量：作用域仅为某代码片断(函数上下文)

* 位置参数变量：当执行脚本的shell进程传递的参数

* 特殊变量：shell内置的有特殊功用的变量


    $?：
    0：成功
    1-255：失败      

#### bash的基础特性之十一：多命令执行
`COMMAND1; COMMAND2; COMMAND3; ...`

**逻辑运算：
运算数：真(true, yes, on, 1)，假(false, no, off, 0)**

与：
>1 && 1 = 1
>1 && 0 = 0
>0 && 1 = 0
>0 && 0 = 0

或：
>1 || 1 = 1
>1 || 0 = 1
>0 || 1 = 1
>0 || 0 = 0

非：
>! 1 = 0
>! 0 = 1

**短路法则：**
>`COMMAND1 && COMMAND2`    实现控制功能 
>COMMAND1为“假”，则COMMAND2不会再执行；
>否则，COMMAND1为“真”，则COMMAND2必须执行；
>`COMMAND1 || COMMAND2`
>COMMAND1为“真”，则COMMAND2不会再执行；
>否则，COMMAND1为“假”，则COMMAND2必须执行；
>
>例：`id username || useradd username`



