#### Linux上文本处理三剑客：
* grep, egrep, fgrep：文本过滤工具，通过（模式：pattern）过滤；
   grep：基本正则表达式，-E（支持扩展正则表达式），-F
   egrep：扩展正则表达式， -G（支持基本正则表达式），-F
   fgrep：不支持正则表达式
* sed：stream editor, 流编辑器；文本编辑工具；
* awk：Linux上的实现为gawk，文本报告生成器（格式化文本）；

#### 正则表达式：Regual Expression, REGEXP
由一类特殊字符及文本字符所编写的模式，其中有些字符不表示其字面意义，而是用于表示控制或通配的功能；

分两类：
* 基本正则表达式：BRE
* 扩展正则表达式：ERE

元字符：指那些在正则表达式中具有特殊意义的专用字符，可以用来规定其前导字符（即位于元字符前面的字符）在目标对象中的出现模式。如：\(hello[[:space:]]\+\)\+
#### grep： Global search Regular expression and Print out the line.

作用：文本搜索工具，根据用户指定的“模式（过滤条件）”对目标文本逐行进行匹配检查；打印匹配到的行；        

模式：由正则表达式的元字符及文本字符所编写出的过滤条件；

*每一个正则表达式的实现都要用到正则表达式引擎（处理器）*

格式： 
`grep  [OPTIONS]  PATTERN  [FILE...]`
`grep  [OPTIONS]  [-e PATTERN | -f FILE]  [FILE...]`

    [root@node1 ~]# grep "UUID" /etc/fstab
    UUID=331961eb-c3a9-4422-b1f4-d6bd132c1bec /boot                   xfs     defaults        0 0

`UUID`四个字母为高亮显示

OPTIONS：
**--color=auto：对匹配到的文本着色后高亮显示**

    [root@node1 ~]# alias
    alias grep='grep --color=auto'
    grep自动显示
**-i：ignorecase，忽略字符的大小写**
**-o：仅显示匹配到的字符串本身**

    [root@node1 ~]# grep -o "UUID" /etc/fstab
    UUID

**-v, --invert-match：显示不能被模式匹配到的行**

    [root@node1 ~]# grep -v "UUID" /etc/fstab
    #
    # /etc/fstab
    # Created by anaconda on Sun Mar 24 23:04:51 2019
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    /dev/mapper/centos_node1-root /                       xfs     defaults        0 0
    /dev/mapper/centos_node1-home /home                   xfs     defaults        0 0
    /dev/mapper/centos_node1-swap swap                    swap    defaults        0 0

**-E：支持使用扩展的正则表达式元字符**
**-q, --quiet, --silent：静默模式，即不输出任何信息**

    [root@node1 ~]# grep -q UUID /etc/fstab
    [root@node1 ~]# echo $?
    0
    [root@node1 ~]# grep -q UUIID /etc/fstab
    [root@node1 ~]# echo $?
    1

**-A #：after, 后#行**

    [root@node1 ~]# grep -A2 UUID /etc/fstab
    UUID=331961eb-c3a9-4422-b1f4-d6bd132c1bec /boot                   xfs     defaults        0 0
    /dev/mapper/centos_node1-home /home                   xfs     defaults        0 0
    /dev/mapper/centos_node1-swap swap                    swap    defaults        0 0

**-B #：before，前#行**

    [root@node1 ~]# grep -B2 UUID /etc/fstab
    #
    /dev/mapper/centos_node1-root /                       xfs     defaults        0 0
    UUID=331961eb-c3a9-4422-b1f4-d6bd132c1bec /boot                   xfs     defaults        0 0

**-C #：context，前后各#行**

    [root@node1 ~]# grep -C2 UUID /etc/fstab
    #
    /dev/mapper/centos_node1-root /                       xfs     defaults        0 0
    UUID=331961eb-c3a9-4422-b1f4-d6bd132c1bec /boot                   xfs     defaults        0 0
    /dev/mapper/centos_node1-home /home                   xfs     defaults        0 0
    /dev/mapper/centos_node1-swap swap                    swap    defaults        0 0


#### 基本正则表达式元字符

##### 字符匹配：
. ：匹配任意单个字符；

    [root@node1 ~]# grep  "r..t" /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    operator:x:11:0:operator:/root:/sbin/nologin
    ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin

[ ]：匹配指定范围内的任意单个字符；
[^]：匹配指定范围外的任意单个字符；

**[:digit:]、[:lower:]、[:upper:]、[:alpha:]、[:alnum:]、[:punct:]、[:space:]**

    [root@node1 ~]# grep "r[[:alpha:]][[:alpha:]]t" /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    operator:x:11:0:operator:/root:/sbin/nologin



##### 匹配次数：
用在要指定其出现的次数的字符的后面，用于限制其前面字符出现的次数；默认工作于贪婪模式；

*：匹配其前面的字符任意次（0,1,多次）

     [root@centos7 ~]# grep "x*y" grep.txt
    abxy
    aby
    xxxxy
    yabwq

.*：匹配任意长度的任意字符

    [root@centos7 ~]# grep "r.*" /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    adm:x:3:4:adm:/var/adm:/sbin/nologin
    lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
    mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
    operator:x:11:0:operator:/root:/sbin/nologin

\ ?：匹配其前面的字符0次或1次；即其前面的字符是可有可无的；

    [root@centos7 ~]# grep "x\?y" grep.txt
    abxy
    aby
    xxxxy
    yabwq

\ +：匹配其前面的字符1次或多次；即其面的字符要出现至少1次；

    [root@centos7 ~]# grep "x\+y" grep.txt
    abxy
    xxxxy

\{m\}：匹配其前面的字符m次；

    [root@centos7 ~]# grep "x\{2\}y" grep.txt
    xxxxy

\{m,n\}：匹配其前面的字符至少m次，至多n次；

     [root@centos7 ~]# grep "x\{2,5\}y" grep.txt
     xxxxy

\{0,n\}：至多n次
\{m,\}：至少m次

##### 位置锚定：

^：行首锚定；用于模式的最左侧；

    [root@centos7 ~]# grep "^root" /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    rooter:x:10000:10000::/home/rooter:/bin/bash
    rootkit:x:10001:10001::/home/rootkit:/bin/bash


$：行尾锚定；用于模式的最右侧

    [root@centos7 ~]# grep "root$" /etc/passwd
    user4:x:10003:10003::/home/user4:/bin/chroot

^PATTERN$：用于PATTERN来匹配整行

    [root@centos7 ~]# grep "^root$" /etc/passwd


^$：空白行

^[[:space:]]*$：空行或包含空白字符的行；
     
**单词：非特殊字符组成的连续字符（字符串）都称为单词**
\< 或 \b：词首锚定，用于单词模式的左侧；

    [root@centos7 ~]# grep "\<root"  /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    operator:x:11:0:operator:/root:/sbin/nologin
    rooter:x:10000:10000::/home/rooter:/bin/bash
    rootkit:x:10001:10001::/home/rootkit:/bin/bash

\> 或 \b：词尾锚定，用于单词模式的右侧；

    [root@centos7 ~]# grep "root\>"  /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    operator:x:11:0:operator:/root:/sbin/nologin
    user4:x:10003:10003::/home/user4:/bin/chroot

\<PATTERN\>：匹配完整单词；

    [root@centos7 ~]# grep "\<root\>"  /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    operator:x:11:0:operator:/root:/sbin/nologin

**练习：**
1、显示/etc/passwd文件中不以/bin/bash结尾的行；

    [root@centos7 ~]# grep -v "/bin/bash$" /etc/passwd       
2、找出/etc/passwd文件中的两位数或三位数；

    [root@centos7 ~]# grep -E  "\<[0-9]{2,3}\>" /etc/passwd
      
    [root@centos7 ~]# grep  "\<[0-9]\{2,3\}\>"  /etc/passwd

3、找出/etc/rc.d/rc.sysinit或/etc/grub2.cfg文件中，以至少一个空白字符开头，且后面非空白字符的行；

    [root@centos7 ~]# grep  "^[[:space:]]\+[^[:space:]]"  /etc/grub2.cfg
    
    [root@centos7 ~]# grep  "^[[:space:]][^[:space:]]"  /etc/grub2.cfg

4、找出"netstat -tan"命令的结果中以'LISTEN'后跟0、1或多个空白字符结尾的行；

    [root@centos7 ~]# netstat -tan | grep  "LISTEN[[:space:]]*$"

#### 分组及引用
    \(\)：将一个或多个字符捆绑在一起，当作一个整体进行处理；
Note：分组括号中的模式匹配到的内容会被正则表达式引擎自动记录于内部的变量中，这些变量为：

\1：模式从左侧起，第一个左括号以及与之匹配的右括号之间的模式所匹配到的字符；
\2：模式从左侧起，第二个左括号以及与之匹配的右括号之间的模式所匹配到的字符；
\3
...    

    [root@centos7 ~]# cat lovers.txt
    He loves his lover.
    He likes his lover.
    She likes her liker.
    She loves her liker.
    [root@centos7 ~]# grep "\(l..e\).*\1" lovers.txt
    He loves his lover.
    She likes her liker.


后向引用：引用前面的分组括号中的模式所匹配到的字符；

    [root@centos7 ~]# grep "^\(r..t\).*\1" /etc/passwd
    root:x:0:0:root:/root:/bin/bash
    rooter:x:10000:10000::/home/rooter:/bin/bash
    rootkit:x:10001:10001::/home/rootkit:/bin/bash


#### egrep：
支持扩展的正则表达式实现类似于grep文本过滤功能；grep -E
格式：egrep [OPTIONS] PATTERN [FILE...]
选项：和grep差不多
**-i, -o, -v, -q, -A, -B, -C**

**-G：支持基本正则表达式**

#### 扩展正则表达式的元字符
##### 字符匹配：
|元字符|含义|
|:---:|:---:|
|.|任意单个字符|
|[ ]|指定范围内的任意单个字符|
|[^]|指定范围外的任意单个字符|

##### 次数匹配：

|元字符|含义|
|:---:|:---:|
|*|任意次，0,1或多次|
|?|0次或1次，其前的字符是可有可无的|
|+|其前字符至少1次|
|{m}|其前的字符m次|
|{m,n}|至少m次，至多n次|


##### 位置锚定
|元字符|含义|
|:---:|:---:|
|^|行首锚定|
|$|行尾锚定|
|<, \b|词首锚定|
|>, \b|词尾锚定|
##### 分组及引用：

()：分组；括号内的模式匹配到的字符会被记录于正则表达式引擎的内部变量中；
后向引用：\1, \2, ...

或：
a|b：a或者b；
C|cat：C或cat
(c|C)at：cat或Cat

**练习：**
1、找出/proc/meminfo文件中，所有以大写或小写S开头的行；至少有三种实现方式；

    [root@centos7 ~]# grep -i "^s" /proc/meminfo
    
    [root@centos7 ~]# grep "^[sS]" /proc/meminfo
    
    [root@centos7 ~]# grep -E "^(s|S)" /proc/meminfo

2、显示肖前系统上root、centos或user1用户的相关信息；

    [root@centos7 ~]# grep -E "^(root|centos|user1)\>" /etc/passwd

3、找出/etc/rc.d/init.d/functions文件中某单词后面跟一个小括号的行；

    [root@centos7 ~]# grep  -E  -o  "[_[:alnum:]]+\(\)"  /etc/rc.d/init.d/functions

4、使用echo命令输出一绝对路径，使用egrep取出基名；

    [root@centos7 ~]# echo /etc/sysconfig/ | grep  -E  -o  "[^/]+/?$"               
进一步：取出其路径名；类似于对其执行dirname命令的结果；     

    [root@centos7 ~]# echo /etc/sysconfig | egrep -o "^/.*(/[[:alnum:]])" | egrep -o "^/.*/" | egrep -o  "^.* 
    [-[:alnum:]]"
    /etc


5、找出ifconfig命令结果中的1-255之间的数值；

    [root@centos7 ~]# ifconfig | grep  -E  -o  "\<([1-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\>"

6、找出ifconfig命令结果中的IP地址，要求结果中只显示IP地址

    [root@centos7 ~]#  ifconfig |egrep "(\<([0,1]?[0-9]?[0-9]|2[0-4][0-9]|25[0-5])\>\.){3}\<([0,1]?[0-9]?[0- 
    9]|2[0-4][0-9]|25[0-5])\>"
        inet 192.168.0.107  netmask 255.255.255.0  broadcast 192.168.0.255
        inet 127.0.0.1  netmask 255.0.0.0
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255

7、添加用户bash, testbash, basher以及nologin(其shell为/sbin/nologin)；而后找出/etc/passwd文件中用户名同shell名的行；

    [root@centos7 ~]# grep  -E  "^([^:]+\>).*\1$"  /etc/passwd            
#### fgrep：不支持正则表达式元字符；
当无需要用到元字符去编写模式时，使用fgrep必能更好；

正则表达式30分钟教程：https://www.cnblogs.com/deerchao/archive/2006/08/24/zhengzhe30fengzhongjiaocheng.html