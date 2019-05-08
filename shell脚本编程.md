#### bash的配置文件
>用户登录系统后，会启动一个接口程序，也称shell程序，一般默认是bash，而这个程序的启动，需要读取配置文件，完成环境的配置，以便用户在该接口程序下方便的执行一系列操作

#### 配置文件的种类：
根据配置文件中定义的内容，将配置文件分为两大类
##### profile类：为交互式登录的shell进程提供配置

用于定义环境变量，启动需要执行的命令或者脚本

*   /etc/profile： 全局设置

*   /etc/profile.d/*.sh

*   ~/.bash_profile: 个人设置（仅对当前用户有效）

##### bashrc类：为非交互式登录的shell进程提供配置

用于定义命令别名或本地变量

*   /etc/bashrc：全局设置
*   ~/.bashrc：个人设置

#### 配置文件的加载顺序
##### 交互式登录

直接通过某终端输入账号和密码后登录打开的shell进程；
使用su命令：su - USERNAME, 或者使用 su -l USERNAME执行的登录切换；

>顺序：/etc/profile-->/etc/profile.d/*.sh-->/.bash_profile-->~/.bashrc-->/etc/bashrc
##### 非交互式登录

su USERNAME执行的登录切换；
      图形界面下打开的终端；
      运行脚本

>顺序：~/.bashrc-->/etc/bashrc-->/etc/profile.d/*.sh
* 命令行中定义的特性，例如变量和别名作用域为当前shell进程的生命周期；
* 配置文件定义的特性，只对随后新启动的shell进程有效；
            

让通过配置文件定义的特性立即生效：
(1) 通过命令行重复定义一次；
(2) 让shell进程重读配置文件；
>~]# source /PATH/FROM/CONF_FILE
>~]# .  /PATH/FROM/CONF_FILE

* * *

#### shell脚本编程
**过程式编程，解释运行，依赖于外部程序文件运行**

编程语言的分类：根据运行方式
* 编译运行：源代码 --> 编译器 （编译）--> 程序文件；
* 解释运行：源代码 --> 运行时启动解释器，由解释器边解释边运行；
            

根据其编程过程中功能的实现是调用库还是调用外部的程序文件：

**编程模型：过程式编程语言，面向对象的编程语言**

>过程式：以指令为中心来组织代码，数据是服务于代码；
>*  顺序执行
>*  选择执行
>*  循环执行
>     代表：C，bash

>对象式：以数据为中心来组织代码，围绕数据来组织指令；
>     类(class)：实例化对象，method；
>     代表：Java, C++, Python

**数据类型：字符型，数值型
     弱类型：字符型**

#####　shell脚本格式：
>脚本文件的第一行，顶格：给出shebang（解释器路径），用于指明解释执行当前脚本的解释器程序文件
>
>常见的解释器：
>\#!/bin/bash
>\#!/usr/bin/python
>\#!/usr/bin/perl

*注意：代码需要加注释，如空白行，缩进，所有#开头的为注释行*                 

>文本编程器：nano
>行编辑器：sed
>全屏幕编程器：nano, vi, vim

**shell脚本是命令的堆积，但很多命令不具有幂等性，需要用程序逻辑来判断运行条件是否满足，以避免其运行中发生错误；**
                
#### 运行脚本：
* 赋予执行权限，并直接运行此程序文件；
     chmod +x /PATH/TO/SCRIPT_FILE
     /PATH/TO/SCRIPT_FILE

* 直接运行解释器，将脚本以命令行参数传递给解释器程序；
     bash /PATH/TO/SCRIPT_FILE
                    

注意：脚本中的空白行会被解释器忽略；
脚本中，除了shebang，余下所有以#开头的行，都会被视作注释行而被忽略；此即为注释行；
shell脚本的运行是通过运行一个子shell进程实现的；
                    
**练习1：写一个脚本，实现如下功能；
(1) 显示/etc目录下所有以大写p或小写p开头的文件或目录本身
(2) 显示/var目录下的所有文件或目录本身，并将显示结果中的小写字母转换为大写后显示
(3) 创建临时文件/tmp/myfile.XXXX**            

    #!/bin/bash
    ls -d /rtc/[pP]*
    ls -d /var/* | 'a-z' 'A-Z'
    mktemp /tmp/myfile.XXXX

>ctrl+o：保存
>ctrl+x：退出

#### 脚本的状态返回值：
**默认是脚本中执行的最后一条件命令的状态返回值**

    [root@centos7 ~]# id usr3 &> /dev/null && exit 0 || useradd usr3
    logout
    There are stopped jobs.
    useradd: user 'usr3' already exists

自定义状态退出状态码：
exit  [n]：n为自己指定的状态码；
注意：shell进程遇到exit时，即会终止，因此，整个脚本执行即为结束；
#### shell脚本的参数传递
##### 位置参数变量
**执行脚本和执行命令一样，在脚本后加上多个参数，用空格隔开即可，例如`myscript.sh argu1 argu2`**
##### 引用方式：
`\$1,  \$2, ..., \${10}, ${11}, ...`

例如：

    ~]#vim sum.sh
    #!/bin/bash
    #
    echo $[$1+$2]

##### 轮替：
`shift  [n]`：位置参数轮替；

    ~]#vim shift.sh
    #!/bin/bash
    #
    echo "First pos argu: $1"
    shift
    echo "Second pos argu: $1"
    ~]# bash shift.sh one two


    ~]#vim shift.sh
    #!/bin/bash
    #
    echo "First and second pos argu: $1, $2"
    shift 2
    echo "Third pos argu: $1"
    ~]# bash shift.sh one two three              

**练习：写一脚本，通过命令传递两个文本文件路径给脚本，计算其空白行数之和**         

    ~]# vim lines.sh
       #!/bin/bash
       #
       file1_lines=$(grep "^$" $1 | wc -l)
       file2_lines=$(grep "^$" $2 | wc -l)
    
       echo "Total blank lines: $[$file1_lines+$file2_lines]"    
    ~]# bash lines.sh /etc.fstab /etc/issue
#### 特殊变量
|符号|含义|
|:---:|:---:|
|0|脚本文件路径本身|
|$#|脚本参数的个数|
|$*|所有参数|
|$@|所有参数|
#### shell脚本的用户交互
用户交互：通过键盘输入数据，从而完成变量赋值操作；
`read[option] [name...]`

**-p“提示字符”**

**-t # 限定输入时间**

```
#!/bin/bash
#
read -p "Enter a username: " name
[ -z "$name" ] && echo "a username is needed." && exit 2

read -p "Enter password for $name, [password]: " password
[ -z "$password" ] && password="password"

if id $name &> /dev/null; then
   echo "$name exists."
else
   useradd $name
   echo "$password" | passwd --stdin $name &> /dev/null
   echo "Add user $name finished."
fi            
```
#### shell脚本的语法检测

*   bash -n FILE，智能检测出语法错误，不能检测出逻辑错误
*   bash -x FILE 调试执行
#### bash脚本编程之算术运算： +，-，*，/,  **, %
算术运算格式：
* let  VAR=算术运算表达式
* VAR=$[算术运算表达式]
* VAR=$((算术运算表达式))
* VAR=\$(expr \$ARG1 \$OP \$ARG2)
        

注意：乘法符号在有些场景中需要使用转义符；

>变量赋值
>增强型赋值：
>
>>变量做某种算术运算后回存至此变量中；
>>                let i=$i+#
>>                let i+=#
>>如：+=，-=，*=, /=, %=

##### 自增：

    VAR=$[$VAR+1]
    let  VAR+=1
    let  VAR++

##### 自减：

    VAR=$[$VAR-1]
    let  VAR-=1
    let  VAR--

**练习：**
1、写一个脚本
计算/etc/passwd文件中的第10个用户和第20个用户的id号之和；        
```    
id1=$(head -10  /etc/passwd | tail -1  | cut  -d:  -f3)
id2=$(head -20   /etc/passwd | tail -1  | cut  -d:  -f3)
```

2、写一个脚本
计算/etc/rc.d/init.d/functions和/etc/inittab文件的空白行数之和；     

    grep "^[[:space:]]*$"   /etc/rc.d/init.d/functions | wc -l

3、编写脚本，实现自动添加三个用户，并计算三个用户的UID之和

    #!/bin/bash
    #
    id_sum=0
    [ $# -lt 3 ] && echo "请输入三个用户:" && exit 1
    for i in $* ;do
    useradd $i && echo "${i}创建成功"
    id_num=$(id -u $i)
    id_sum=$[${id_sum}+${id_num}]
    done
    echo "三个用户uid之和为: ${id_sum}"
    
    [root@centos7 ~]# bash useradd.sh 1 2 3
    useradd: invalid user name '1'
    useradd: invalid user name '2'
    useradd: invalid user name '3'
    三个用户uid之和为: 6

#### 常用测试条件及控制语句

##### 条件测试语句
判断某需求是否满足，需要由测试机制来实现

####如何编写测试表达式以实现所需的测试：
* 执行命令，并利用命令状态返回值来判断；

    0：成功
    1-255：失败
* 测试表达式

    test  EXPRESSION
    [ EXPRESSION ]
    [[ EXPRESSION ]]
                

注意：EXPRESSION两端必须有空白字符，否则为语法错误；





#### bash的测试类型

##### 数值测试：
|符号|含义|
|:---:|:---:|
|-eq|是否等于|
|-ne|是否不等于|
|-gt|是否大于|
|-ge|是否大于等于|
|-lt | 是否小于|
| -le |是否小于等于|


##### 字符串测试
|符号|含义|
|:---:|:---:|
|== |是否等于|
|> |是否大于|
|< |是否小于|
|!= |是否不等于|
|=~ |左侧的字符串是否被右侧的patter所匹配|
|-z "string" |判定指定的字符是否为空|
|-n "string" |判定指定的字符是否不空|

**注意**

1.  字符创比较尽量定义在双括号中
2.  比较对象是变量时，最好加双引号（变量为空时，该测试会出现问题）

##### 文件测试：
* 存在性测试
* -a FILE
* -e FILE

**文件存在性测试**
存在则为真，否则则为假

|符号|含义|
|:---:|:---:|
|-b FILE |文件是否存在，且为块设备文件|
|-c FILE|文件是否存在，且为字符设备|
|-d FILE|文件是否存在，且为目录文件|
|-f FILE| 文件是否存在，且为普通文件|
| -h FILE|是否存在，且为符号链接|
| -p FILE|是否存在，且为命名管道文件|
|-S FILE |是否存在，且为套接字文件|



**文件权限测试**

|符号|含义|
|:---:|:---:|
|-r FILE |是否存在，且对当前用户可读|
|-w FILE|是否存在，且对当前用户可写|
| -x FILE|是否存在，且对当前用户可执行|

**特殊权限测试**

|符号|含义|
|:---:|:---:|
|-g FILE |是否存在，且拥有sgid权限|
|-u FILE|是否存在，且拥有suid权限|
|-k FILE|是否存在，且拥有sticky权限|

##### 双目测试
|符号|含义|
|:---:|:---:|
| FILE1 -ef FILE2 |file1与file2是否为指向同一个文件系统的相同inode的硬链接|
| FILE1 -ne FILE2|file1是否新于file2|
|FILE1 -ot FILE2| file1是否旧于file2|

##### 文件是否有内容：
>-s  FILE：是否有内容；
> 时间戳：
>-N FILE：文件自从上一次读操作后是否被修改过；

##### 从属关系测试：
|符号|含义|
|:---:|:---:|
| -O  FILE |当前用户是否为文件的属主|
|-G  FILE|当前用户是否属于文件的属组|

##### 组合测试条件

逻辑运算

* 命令的逻辑运算

    COMMAND1 && COMMAND2

    COMMAND1 || COMMAND2

    !COMMAND

* 表达式的逻辑运算

    EXPRESSION1 -a EXPRESSION2

    EXPRESSION1 -o EXPRESSION2

    !EXPRESSION

**练习：将当前主机名称保存至hostName变量中；主机名如果为空，或者为localhost.localdomain，则将其设置为www.magedu.com**

    hostName=$(hostname)
    
    [ -z "$hostName" -o "$hostName" == "localhost.localdomain" -o "$hostName" == "localhost" ] && 
    hostname [www.magedu.com](http://www.magedu.com/)


#### 控制语句

##### if选择执行语句

*   单分支
```
if  测试条件; then
    代码分支
fi
```
例如：测试/tmp目录下是否有test文件，如果有则告知用户，已存在
```
source-shell
if [[ -a /tmp/test ]];then
    echo "test is existing"
fi
```

*   双分支
```
if  测试条件; then
       条件为真时执行的分
else
       条件为假时执行的分支
fi
```
**例如:检查/tmp目录下是否有名为test的普通文件，没有则创建**
```
source-shell
if [[ -f /tmp/test ]];then
    	echo "test is existing"
    	else
    		touch /tmp/test
fi
```
* 多分支
```
 if  测试条件1；then
       条件1为真时执行的分支
elif  测试条件2；then
       条件2为真时执行的分支
......
else
       上述条件都不满足时的执行分支
fi
```
注意：即便多个条件可能同时都能满足，分支只会执行中其中一个，首先测试为“真”

**示例：脚本参数传递一个文件路径给脚本，判断此文件的类型；**
```            
#!/bin/bash
#
if [ $# -lt 1 ]; then
     echo "At least on path."
     exit 1
fi

if ! [ -e $1 ]; then
     echo "No such file."
     exit 2
fi

if [ -f $1 ]; then
      echo "Common file."
elif [ -d $1 ]; then
      echo "Directory."
elif [ -L $1 ]; then
      echo "Symbolic link."
elif [ -b $1 ]; then
      echo "block special file."
elif [ -c $1 ]; then
      echo "character special file."
elif [ -S $1 ]; then
      echo "Socket file."
else
      echo "Unkown."
fi    
```
注意：if语句可嵌套；
                    
**练习1：通过命令行参数给定一个用户名，判断其ID号是偶数还是奇数**

    #!/bin.bash
    if [ $# -lt 1 ];then
    echo "Must give a username."
    fi
    num=$(id -u $1)
    let num2=$num%2
    #echo "num is $num"
    #echo "num2 is $num2"
    if [ $num2 -eq 1 ];then
    echo "the uid is "odd number
    else
    echo "the uid is even number"
    fi

**练习2：写一个脚本
(1) 传递一个参数给脚本，此参数为用户名；
(2) 根据其ID号来判断用户类型：
0： 管理员
1-999：系统用户
1000+：登录用户**
```                    
#!/bin/bash
#
[ $# -lt 1 ] && echo "At least on user name." && exit 1

! id $1 &> /dev/null && echo "No such user." && exit 2

userid=$(id -u $1)

if [ $userid -eq 0 ]; then
     echo "root"
elif [ $userid -ge 1000 ]; then
     echo "login user."
else
     echo "System user."
fi                                        
```
**练习3：写一个脚本
(1) 列出如下菜单给用户：
disk: show disks info;
mem: show memory info;
cpu: show cpu info;
( * ) quit;
(2)  (2) 正确的选择则给出相应的信息；否则，则提示重新选择正确的选项；**
```         
#!/bin/bash
#
cat << EOF
(disk) show disks info
(mem) show memory info
(cpu) show cpu info
(*) QUIT
EOF

read -p "Your choice: " option

while [ "$option" != "cpu" -a "$option" != "mem" -a "$option" != "disk" -a "$option" != "quit" ]; do
                    echo "cpu, mem, disk, quit"
                    read -p "Enter your option again: " option
done

if [[ "$option" == "disk" ]]; then
      fdisk -l /dev/[sh]d[a-z]
elif [[ "$option" == "mem" ]]; then
      free -m
elif [[ "$option" == "cpu" ]];then
      lscpu
else
      echo "Unkown option."
      exit 3
fi
```
##### case语句
```
case  $VARAIBLE（变量引用）  in  
PAT1)
     分支1
      ;;
PAT2)
     分支2
      ;;
...
*)
     分支n
      ;;
esac
```
**case支持glob风格的通配符：**
>*：任意长度的任意字符；
>?：任意单个字符；
>[]：范围内任意单个字符；
> a|b：a或b；

**示例：写一个服务框架脚本**
$lockfile,  值/var/lock/subsys/SCRIPT_NAME
>(1) 此脚本可接受start, stop, restart, status四个参数之一；
>(2) 如果参数非此四者，则提示使用帮助后退出；
>(3) start，则创建lockfile，并显示启动；stop，则删除lockfile，并显示停止；restart，则先删除此文件再创建此文件，而后显示重启完成；status，如果lockfile存在，则显示running，否则，则显示为stopped.

```                
#!/bin/bash
#
# chkconfig: - 50 50
# description: test service script
#
prog=$(basename $0)
lockfile=/var/lock/subsys/$prog

case $1  in
start)
      if [ -f $lockfile ]; then
          echo "$prog is running yet."
      else
         touch $lockfile
         [ $? -eq 0 ] && echo "start $prog finshed."
      fi
      ;;
stop)
      if [ -f $lockfile ]; then
           rm -f $lockfile
           [ $? -eq 0 ] && echo "stop $prog finished."
      else
         echo "$prog is not running."
      fi
      ;;
restart)
      if [ -f $lockfile ]; then
           rm -f $lockfile
           touch $lockfile
           echo "restart $prog finished."
     else
           touch -f $lockfile
           echo "start $prog finished."
      fi
      ;;
status)
      if [ -f $lockfile ]; then
         echo "$prog is running"
     else
         echo "$prog is stopped."
      fi
      ;;
*)
     echo "Usage: $prog {start|stop|restart|status}"
     exit 1
esac
```
#### 循环执行： 将一段代码重复执行0、1或多次；
进入条件：
 >for：列表元素非空；
 >            while：条件测试结果为“真”
 >            unitl：条件测试结果为“假”

退出条件：每个循环都应该有退出条件，以有机会退出循环；
> for：列表元素遍历完成；
>             while：条件测试结果为“假”
>             until：条件测试结果为“真

##### for循环：
两种格式：
- 遍历列表
- 控制变量
                

**遍历列表：**
```
for  VARAIBLE  in  LIST; do
     循环体
done
```
进入条件：只要列表有元素，即可进入循环；
退出条件：列表中的元素遍历完成；

**示例：**
```
#!/bin/bash
#
for username in user21 user22 user23; do
    if id $username &> /dev/null; then
        echo "$username exists."
    else
        useradd $username && echo "Add user $username finished."
    fi
done
```
**LISTT的生成方式：**
>(1) 直接给出；
>(2) 整数列表
>(a) {start..end}
>(b) seq [start  [incremtal]] last
>(3) 返回列表的命令
>(4) glob
>(5) 变量引用
>$@, $*
>...

**示例：求100以内所有正整数之和；**
```
#!/bin/bash
#
declare -i sum=0

for i in {1..100}; do
            echo "\$sum is $sum, \$i is $i"
            sum=$[$sum+$i]
done

echo $sum
```
**示例：判断/var/log目录下的每一个文件的内容类型**
```                    
#!/bin/bash
#
for filename in /var/log/*; do
     if [ -f $filename ]; then
        echo "Common file."
elif [ -d $filename ]; then
        echo "Directory."
elif [ -L $filename ]; then
        echo "Symbolic link."
elif [ -b $filename ]; then
        echo "block special file."
elif [ -c $filename ]; then
        echo "character special file."
elif [ -S $filename ]; then
        echo "Socket file."
else
        echo "Unkown."
     fi                    
done
```
##### for循环的特殊用法：
```
for  ((控制变量初始化;条件判断表达式;控制变量的修正语句)); do
                循环体
            done
```
>控制变量初始化：仅在循环代码开始运行时执行一次；
>控制变量的修正语句：每轮循环结束会先进行控制变量修正运算，而后再做条件判断；

**示例：求100以内所有正整数之和**
```                
#!/bin/bash
#
declare -i sum=0

for ((i=1;i<=100;i++)); do
    let sum+=$i
done

echo "Sum: $sum."
```
**示例：用for循环检测10.0.0.1/24网段存活的IP地址**
```
#!/bin/bash
#
declare -i a=0
for a in {1..255};do
if ping -w 2 -c 2 10.0.0.$a &>/dev/null;then
echo "10.0.0.$a is up"
else
echo "10.0.0.$a is down"
fi
done
```
##### while循环：
```
while  CONDITION; do
     循环体
     循环控制变量修正表达式
done
```
>进入条件：CONDITION测试为”真“
>退出条件：CONDITION测试为”假“

**示例：求100以内所有正整数之和**
``` 
#!/bin/bash
#
declare -i sum=0
declare -i i=1

while [ $i -le 100 ]; do
     let sum+=$i
     let i++
done

echo $sum
```
###### 循环控制语句：
**continue：提前结束本轮循环，而直接进入下一轮循环判断**
```
while  CONDITION1; do
          CMD1
                    ...
      if  CONDITION2; then
          continue
      fi
          CMDn
         ...
done
```
**示例：求100以内所有偶数之和**                        

``` #!/bin/bash
#
declare -i evensum=0
declare -i i=0

while [ $i -le 100 ]; do
    let i++
    if [ $[$i%2] -eq 1 ]; then
            continue
    fi
    let evensum+=$i
done

echo "Even sum: $evensum"   
```
**break：提前跳出循环**
```
while  CONDITION1; do
          CMD1
          ..
     if  CONDITION2; then
          break
     fi
done
```
**创建死循环：**
```
while true; do
          循环体
done
```
>退出方式：
>某个测试条件满足时，让循环体执行break命令；

**示例：求100以内所奇数之和**
```
#!/bin/bash
#
declare -i oddsum=0
declare -i i=1

while true; do
     let oddsum+=$i
     let i+=2
     if [ $i -gt 100 ]; then
           break
    fi

done
```
###### sleep命令：
>delay for a specified amount of time
>`sleep NUMBER`

**练习：每隔3秒钟到系统上获取已经登录用户的用户的信息；其中，如果logstash用户登录了系统，则记录于日志中，并退出；**
```
#!/bin/bash
#
while true; do
      if who | grep "^logstash\>" &> /dev/null; then
          break
      fi
          sleep 3
done

echo "$(date +"%F %T") logstash logged on" >> /tmp/users.log    
```
```                
#!/bin/bash
#
until who | grep "^logstash\>" &> /dev/null; do
         sleep 3
         done

echo "$(date +"%F %T") logstash logged on" >> /tmp/users.log   
```

##### while循环的特殊用法（遍历文件的行）：
```
while  read  VARIABLE; do
          循环体；
done  <  /PATH/FROM/SOMEFILE
```
>依次读取/PATH/FROM/SOMEFILE文件中的每一行，且将基赋值给VARIABLE变量；

**示例：找出ID号为偶数的用户，显示其用户名、ID及默认shell**
```                
#!/bin/bash
#
while read line; do
    userid=$(echo $line | cut -d: -f3)
    username=$(echo $line | cut -d: -f1)
    usershell=$(echo $line | cut -d: -f7)

    if [ $[$userid%2] -eq 0 ]; then
          echo "$username, $userid, $usershell."
    fi
done < /etc/passwd        
```
**示例：用while循环检测10.0.0.1/24网段存活的IP地址**
```
#!/bin/bash
#
declare -i a=0
declare -i b=0
declare -i c=0

while [ $a -le 255 ];do
if ping -w 2 -c 2 10.0.0.$a &>/dev/null;then
echo "10.0.0.$a is up"
else
echo "10.0.0.$a is down"
fi
let a++
done
```
##### until循环：
```
until  CONDITION; do
     循环体
     循环控制变量修正表达式
done
```
>进入条件：CONDITION测试为”假“
>退出条件：CONDITION测试为”真“        

**示例：求100以内所有正整数之和**
 ```          
#!/bin/bash
#
declare -i sum=0
declare -i i=1

until [ $i -gt 100 ]; do
      let sum+=$i
      let i++
done

echo $sum            
 ```
**练习：打印九九乘法表**
1X1=1
1X2=2  2X2=4
1X3=3  2X3=6  3X3=9
外循环控制乘数，内循环控制被乘数；
```      
#!/bin/bash
#
for j in {1..9}; do
      for i in $(seq 1 $j); do
            echo -n -e "${i}X${j}=$[${i}*${j}]\t"
      done
      echo
done    
```
```
#!/bin/bash
#
for ((j=1;j<=9;j++)); do
         for ((i=1;i<=j;i++)); do
                 echo -e -n "${i}X${j}=$[${i}*${j}]\t"
         done
         echo
done              
```
**写一个脚本：
ping命令去查看172.16.1.1-172.16.67.1范围内的所有主机是否在线；在线的显示为up, 不在线的显示down，分别统计在线主机，及不在线主机数；
分别使用for, while循环实现。**
```
#!/bin/bash
#
declare -i uphosts=0
declare -i downhosts=0

for i in {1..67}; do
     if ping -W 1 -c 1 172.16.$i.1 &> /dev/null; then
             echo "172.16.$i.1 is up."
             let uphosts+=1
     else
             echo "172.16.$i.1 is down."
             let downhosts+=1
      fi
done

echo "Up hosts: $uphosts, Down hosts: $downhosts."        
```
```
#!/bin/bash
#
declare -i uphosts=0
declare -i downhosts=0
declare -i i=1

hostping() {
          if ping -W 1 -c 1 $1 &> /dev/null; then
                   echo "$1 is up."
                   return 0
          else
                   echo "$1 is down."
                   return 1
          fi
}
          while [ $i -le 67 ]; do
                hostping 172.16.$i.1
                [ $? -eq 0 ] && let uphosts++ || let downhosts++
                let i++
done

echo "Up hosts: $uphosts, Down hosts: $downhosts."                    
```
**写一个脚本，实现：
能探测C类、B类或A类网络中的所有主机是否在线；**
```
#!/bin/bash
#
cping() {
        local i=1
        while [ $i -le 254 ]; do
                if ping -W 1 -c 1 $1.$i &> /dev/null; then
                        echo "$1.$i is up"
                else
                        echo "$1.$i is down."
                fi
                let i++
done
}
bping() {
        local j=0
        while [ $j -le 255 ]; do
                cping $1.$j
                let j++
done
}
aping() {
        local x=0
        while [ $x -le 255 ]; do
                bping $1.$x
                let x++
done
}
```

#### 函数：function
过程式编程：代码重用
- 模块化编程
- 结构化编程
            
>把一段独立功能的代码当作一个整体，并为之一个名字；命名的代码段，此即为函数；

注意：定义函数的代码段不会自动执行，在调用时执行；所谓调用函数，在代码中给定函数名即可；
函数名出现的任何位置，在代码执行时，都会被自动替换为函数代码；

**语法风格一：**
```
function  f_name  {
         ...函数体...
}
```
**语法风格二：**
```
f_name()  {
         ...函数体...
}
```
**函数的生命周期：每次被调用时创建，返回时终止；
其状态返回结果为函数体中运行的最后一条命令的状态结果；**
>自定义状态返回值，需要使用：return
>return [0-255]
>0: 成功
>1-255: 失败

**示例：给定一个用户名，取得用户的id号和默认shell**
```           
#!/bin/bash
#
userinfo() {
       if id "$username" &> /dev/null; then
             grep "^$username\>" /etc/passwd | cut -d: -f3,7
       else
             echo "No such user."
       fi
}

username=$1
userinfo

username=$2
userinfo        
```

**示例2：服务脚本框架（函数形式）**
```                
#!/bin/bash
#
# chkconfig: - 50 50
# description: test service script
#
prog=$(basename $0)
lockfile=/var/lock/subsys/$prog

start() {
        if [ -f $lockfile ]; then
               echo "$prog is running yet."
        else
               touch $lockfile
               [ $? -eq 0 ] && echo "start $prog finshed."
        fi
}

stop() {
        if [ -f $lockfile ]; then
               rm -f $lockfile
               [ $? -eq 0 ] && echo "stop $prog finished."
        else
               echo "$prog is not running."
        fi
}
status() {
        if [ -f $lockfile ]; then
               echo "$prog is running"
        else
               echo "$prog is stopped."
        fi
}

usage() {
               echo "Usage: $prog {start|stop|restart|status}"
}

case $1 in
start)
        start ;;
stop)
        stop ;;
restart)
        stop
        start ;;
status)
        status ;;
*)
        usage
        exit 1 ;;
esac
```
##### 函数返回值：
>函数的执行结果返回值：
>(1) 使用echo或printf命令进行输出；
>(2) 函数体中调用的命令的执行结果；
>
>函数的退出状态码：
>(1) 默认取决于函数体中执行的最后一条命令的退出状态码；
>(2) 自定义：return

##### 函数可以接受参数：
>传递参数给函数：
>在函数体中当中，可以使用\$1，\$2, ...引用传递给函数的参数；还可以函数中使用\$*或\$@引用所有参数，$#引用传递的参数的个数；
>在调用函数时，在函数名后面以空白符分隔给定参数列表即可，例如，testfunc  arg1 arg2 arg3 ...

**示例：添加10个用户，添加用户的功能使用函数实现，用户名做为参数传递给函数**
```           
#!/bin/bash
#
# 5: user exists

addusers() {
         if id $1 &> /dev/null; then
                return 5
         else
                useradd $1
                retval=$?
                return $retval
         fi
}

     for i in {1..10}; do
                addusers ${1}${i}
                retval=$?
          if [ $retval -eq 0 ]; then
                echo "Add user ${1}${i} finished."
          elif [ $retval -eq 5 ]; then
                echo "user ${1}${i} exists."
          else
                echo "Unkown Error."
          fi
done
```
##### 变量作用域：
>局部变量：作用域是函数的生命周期；在函数结束时被自动销毁；
>定义局部变量的方法：local VARIABLE=VALUE
>本地变量：作用域是运行脚本的shell进程的生命周期；因此，其作用范围为当前shell脚本程序文件；

**示例程序：**
```
#!/bin/bash
#
name=tom

setname() {
       local name=jerry
       echo "Function: $name"
}

setname
echo "Shell: $name"
```
##### 函数递归：
>函数直接或间接调用自身

```                
n*(n-1)!=n*(n-1)*(n-2)!=
```
```
#!/bin/bash
#
fact() {
       if [ $1 -eq 0 -o $1 -eq 1 ]; then
            echo 1
       else
            echo $[$1*$(fact $[$1-1])]
       fi
}

fact $1                    
```
```                    
斐波那契数列：1,1,2,3,5,8,13,21,...
```
```                                    
#!/bin/bash
#
fab() {
      if [ $1 -eq 1 ]; then
              echo -n "1 "
      elif [ $1 -eq 2 ]; then
              echo -n "1 "
      else
              echo -n "$[$(fab $[$1-1])+$(fab $[$1-2])] "
       fi
}

       for i in $(seq 1 $1); do
              fab $i
      done
 echo
```

#### 数组：
>程序=指令+数据
>数据：变量、文件

变量：存储单个元素的内存空间

>**数组：存储多个元素的连续的内存空间**
>数组名：整个数组只有一个名字；
>数组索引：编号从0开始；
>`数组名[索引]`
>`${ARRAY_NAME[INDEX]}`

>注意：bash-4及之后的版本，支持自定义索引格式，而不仅仅是0，1,2，...数字格式；
>此类数组称之为“关联数组”
```
[root@promote ~]# world[us]="america"
[root@promote ~]# echo ${world[us]}
america
[root@promote ~]# world[uk]="united kingdom"
[root@promote ~]# echo ${world[uk]}
united kingdom
```
##### 声明数组：
- declare  -a  NAME：声明索引数组
- declare  -A  NAME：声明关联数组

##### 数组中元素的赋值方式：
(1) 一次只赋值一个元素
ARRAY_NAME[INDEX]=value
```
[root@promote ~]# animal[0]=pig
[root@promote ~]# animal[1]=dog
[root@promote ~]# echo $animal
pig
[root@promote ~]# echo ${animal[0]}
pig
[root@promote ~]# echo ${animal[1]}
dog
```
(2) 一次赋值全部元素
ARRAY_NAME=("VAL1"  "VAL2"  "VAL3"  ...)
```
[root@promote ~]# weekday=("Monday" "Tuesday" "Wednesday")
[root@promote ~]# echo ${weekday[1]}
Tuesday
```
(3) 只赋值特定元素；
ARRAY_NAME=([0]="VAL1"  [3]="VAL4" ...)
注意：bash支持稀疏格式的数组
```
[root@promote ~]# sword=([0]="Yitianjian" [3]="Longquan")
[root@promote ~]# echo ${sword[3]}
Longquan
```
(4) read  -a  ARRAY_NAME
```
[root@promote ~]# read -a Jiangsu
ljiawen wangchenlu lalala
[root@promote ~]# echo ${Jiangsu[2]}
lalala
```
>引用数组中的元素：${ARRAY_NAME[INDEX]}
>注意：引用时，只给数组名，表示引用下标为0的元素；

##### 数组的长度（数组中元素的个数）:
>`${#ARRAY_NAME[*]}`
>`${#ARRAY_NAME[@]}`
```
[root@promote ~]# echo ${#Jiangsu[*]}
3
[root@promote ~]# echo ${#animal[@]}
2
```
**示例：生成10个随机数，并找出其中的最大值**
```
#!/bin/bash
#
declare -a  rand
declare -i max=0

for i in {0..9}; do
       rand[$i]=$RANDOM
       echo ${rand[$i]}
       [ ${rand[$i]} -gt $max ] && max=${rand[$i]}
done

echo "MAX: $max"            
```
**练习：写一个脚本
定义一个数组，数组中的元素是/var/log目录下所有以.log结尾的文件；统计其下标为偶数的文件中的行数之和**
```
#!/bin/bash
#
declare -a files
files=(/var/log/*.log)
declare -i lines=0
for i in $(seq 0 $[${#files[*]}-1]); do
    if [ $[$i%2] -eq 0 ]; then
          let lines+=$(wc -l ${files[$i]} | cut -d' ' -f1)
    fi
done

echo "Lines: $lines."
```
##### 引用数组中的所有元素：
`${ARRAY_NAME[*]}`
`${ARRAY_NAME[@]}`

##### 数组元素切片： 
`${ARRAY_NAME[@]:offset:number}`
offset：要路过的元素个数；
number：要取出的元素个数；省略number时，表示取偏移量之后的所有元素；
```
[root@promote ~]# files=(/etc/[Pp]*)
[root@promote ~]# echo ${files[*]}
/etc/pam.d /etc/passwd /etc/passwd- /etc/pkcs11 /etc/pki /etc/plymouth /etc/pm /etc/polkit-1 /etc/popt.d /etc/postfix /etc/ppp /etc/prelink.conf.d /etc/printcap /etc/profile /etc/profile.d /etc/protocols /etc/python
[root@promote ~]# echo ${files[@]:2:3}
/etc/passwd- /etc/pkcs11 /etc/pki
```
**向非稀疏格式数组中追加元素：**
` ARRAY_NAME[${#ARRAY_NAME[*]}]=`

**删除数组中的某元素：**
`unset  ARRAY[INDEX]`

**关联数组：**
`declare  -A  ARRAY_NAME`
`ARRAY_NAME=([index_name1]="value1"  [index_name2]="value2" ...)`

##### bash的内置字符串处理工具：
**字符串切片：**
>`${var:offset:number}`取字符串的子串；
>取字符趾的最右侧的几个字符：${var:  -length}
>
>注意：冒号后必须有一个空白字符
```
[root@promote ~]# name=jerry
[root@promote ~]# echo ${name: 2: 2}
rr
[root@promote ~]# echo ${name: 2}
rry
[root@promote ~]# echo ${name: -4}
erry
```
**基于模式取子串：**
>\${var#\*word}：其中word是指定的分隔符；
>功能：**自左而右**，查找var变量所存储的字符串中，**第一次**出现的word分隔符，删除字符串开头至此分隔符之间的所有字符；
```
[root@promote ~]# mypath="/etc/init.d/functions"
[root@promote ~]# echo ${mypath#*/}
etc/init.d/functions
```
>\${var##\*word}：其中word是指定的分隔符；
>功能：**自左而右**，查找var变量所存储的字符串中，**最后一次**出现的word分隔符，删除字符串开头至此分隔符之间的所有字符；
```
[root@promote ~]# mypath="/etc/init.d/functions"
[root@promote ~]# echo ${mypath##*/}
functions
```
>\${var%word\*}：其中word是指定的分隔符；
>功能：**自右而左**，查找var变量所存储的字符串中，**第一次**出现的word分隔符，删除此分隔符至字符串尾部之间的所有字符；
```
[root@promote ~]# mypath="/etc/init.d/functions"
[root@promote ~]# echo ${mypath%/*}
/etc/init.d
```
>\${var%%word\*}：其中word是指定的分隔符；
>功能：**自右而左**，查找var变量所存储的字符串中，**最后一次**出现的word分隔符，删除此分隔符至字符串尾部之间的所有字符；
```
[root@promote ~]# mypath="/etc/init.d/functions"
[root@promote ~]# echo ${mypath%%/*}
[root@promote ~]# mypath="etc/init.d/functions"
[root@promote ~]# echo ${mypath%%/*}
etc
```
**示例：**
```
[root@promote ~]# url=http://www.magedu.com:80
[root@promote ~]# echo ${url##*:}
80
[root@promote ~]# echo ${url%%:*}
http
```

**查找替换：**
>\${var/PATTERN/SUBSTI}：查找var所表示的字符串中，第一次被PATTERN所匹配到的字符串，将其替换为SUBSTI所表示的字符串；
>
>\${var//PATTERN/SUBSTI}：查找var所表示的字符串中，所有被PATTERN所匹配到的字符串，并将其全部替换为SUBSTI所表示的字符串；
>
```
[root@promote ~]# userinfo="root:x:0:0:root admin:/root:/bin/chroot"
[root@promote ~]# echo ${userinfo/root/ROOT}
ROOT:x:0:0:root admin:/root:/bin/chroot
[root@promote ~]# echo ${userinfo//root/ROOT}
ROOT:x:0:0:ROOT admin:/ROOT:/bin/chROOT
```
>\${var/#PATTERN/SUBSTI}：查找var所表示的字符串中，行首被PATTERN所匹配到的字符串，将其替换为SUBSTI所表示的字符串；
>
>\${var/%PATTERN/SUBSTI}：查找var所表示的字符串中，行尾被PATTERN所匹配到的字符串，将其替换为SUBSTI所表示的字符串；

注意：PATTERN中使用glob风格和通配符；
```
[root@promote ~]# userinfo="root:x:0:0:root admin:/root:/bin/chroot"
[root@promote ~]# echo ${userinfo/%r??t/ROOT}
root:x:0:0:root admin:/root:/bin/chROOT
```
**查找删除：**
>\${var/PATTERN}：以PATTERN为模式查找var字符串中第一次的匹配，并删除
>\${var//PATERN}：以PATTERN为模式查找var字符串中所有的匹配，并删除
>\${var/#PATTERN：以PATTERN为模式查找var字符串中所有被行首匹配的，并删除
>${var/%PATTERN}：以PATTERN为模式查找var字符串中所有被行尾匹配的，并删除

**字符大小写转换：**
>\${var^^}：把var中的所有小写字符转换为大写
>\${var,,}：把var中的所有大写字符转换为小写
```
[root@promote ~]# url=http://www.magedu.com:80
[root@promote ~]# echo ${url^^}
HTTP://WWW.MAGEDU.COM:80
[root@promote ~]# myurl=${url^^}
[root@promote ~]# echo $myurl
HTTP://WWW.MAGEDU.COM:80
[root@promote ~]# echo ${myurl,,}
http://www.magedu.com:80
```
**变量赋值：**
>\${var:-VALUE}：如果var变量为空，或未设置，那么返回VALUE；否则，则返回var变量的值；
```
[root@promote ~]# echo hi
hi
[root@promote ~]# echo $hi

[root@promote ~]# echo ${hi:-world}
world
[root@promote ~]# hi="america"
[root@promote ~]# echo ${hi:-world}
america
```
>\${var:=VALUE}：如果var变量为空，或未设置，那么返回VALUE，并将VALUE赋值给var变量；否则，则返回var变量的值；
```
[root@promote ~]# hi="america"
[root@promote ~]# echo ${hi:=world}
america
[root@promote ~]# unset hi
[root@promote ~]# echo ${hi:=world}
world
[root@promote ~]# echo $hi
world
```
>\${var:+VALUE}：如果var变量不空，则返回VALUE；
>${var:?ERROR_INFO}：如果var为空，或未设置，那么返回ERROR_INFO为错误提示；否则，返回var值；


#### 信号捕捉：
**trap命令**
格式：`trap  'COMMAND'  SIGNALS`
>列出信号：
>`trap  -l`
>`kill  -l`
```
[root@promote ~]# trap -l
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
>`man  7  signal`
>解释每个信号的功用

**常可以进行捕捉的信号：**
>HUP， INT

**示例：**
``` 
#!/bin/bash
#
declare -a hosttmpfiles
trap  'mytrap'  INT

mytrap()  {
         echo "Quit"
         rm -f ${hosttmpfiles[@]}
         exit 1
}

for i in {1..50}; do
        tmpfile=$(mktemp /tmp/ping.XXXXXX)
        if ping -W 1 -c 1 172.16.$i.1 &> /dev/null; then
              echo "172.16.$i.1 is up" | tee $tmpfile
        else
              echo "172.16.$i.1 is down" | tee $tmpfile
        fi
        hosttmpfiles[${#hosttmpfiles[*]}]=$tmpfile
        done

rm -f ${hosttmpfiles[@]}
```

#### 在bash中使用ACSII颜色
`\033[31m hello \033[0m`
![](https://upload-images.jianshu.io/upload_images/16547068-884a88c4985ee87c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>\##m：
>左侧#：
>3：前景色
>4：背景色
>右侧#：颜色种类
>1, 2, 3, 4, 5, 6, 7
>
>\#m：
>加粗、闪烁等功能；           
>
>多种控制符，可组合使用，彼此间用分号隔开

**示例：**
![](https://upload-images.jianshu.io/upload_images/16547068-230048a152d76817.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### dialog命令：可实现窗口化编程
**各窗体控件使用方式**

`[root@promote ~]# dialog --msgbox hello 17 30`
![](https://upload-images.jianshu.io/upload_images/16547068-60cf2c39ae55befc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>如何获取用户选择或键入的内容？
>默认，其输出信息被定向到了错误输出流；
