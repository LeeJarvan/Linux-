**文本处理三剑客：**

>grep, egrep, fgrep：文本过滤器
>sed：Stream EDitor，流编辑器，行
>awk：文本格式化工具，报告生成器


`sed [OPTION]...  'script'  [input-file] ...`
**script组成格式：**
地址定界编辑命令
            
##### OPTION：
>**-n：不输出模式空间中的内容至屏幕**
>**-e script, --expression=script：多点编辑**
>-f  /PATH/TO/SED_SCRIPT_FILE
>SED_SCRIPT：每行一个编辑命令
>**-r, --regexp-extended：支持使用扩展正则表达式**
>**-i[SUFFIX], --in-place[=SUFFIX]：直接编辑原文件**

```        
[root@promote ~]# sed  -e  's@^#[[:space:]]*@@'   -e  '/^UUID/d'  /etc/fstab


/etc/fstab
Created by anaconda on Sun Apr 14 09:29:51 2019

Accessible filesystems, by reference, are maintained under '/dev/disk'
See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info

/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
##### 地址定界：
>(1) 空地址：对全文进行处理；
>
>(2) 单地址：
>\#：指定行；
>/pattern/：被此模式所匹配到的每一行；
>
>(3) 地址范围
>\#,#：
>\#,+#：
>\#，/pat1/
>/pat1/,/pat2/
>$：最后一行；
>
>(4) 步进：~
>1~2：所有奇数行
>2~2：所有偶数行

##### 编辑命令：
**d：删除**
```
[root@promote ~]# cat /etc/fstab 

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
[root@promote ~]# sed '/^#/d' /etc/fstab 

/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
**p：显示模式空间中的内容**
```
[root@promote ~]# sed  '2~2p' /etc/fstab   #偶数行会显示两遍

#
#
# /etc/fstab
# Created by anaconda on Sun Apr 14 09:29:51 2019
# Created by anaconda on Sun Apr 14 09:29:51 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
#
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
[root@promote ~]# sed -n  '2~2p' /etc/fstab  #显示所有的偶数行
#
# Created by anaconda on Sun Apr 14 09:29:51 2019
# Accessible filesystems, by reference, are maintained under '/dev/disk'
#
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
**a  \text：在行后面追加文本“text”，支持使用\n实现多行追加**
```
[root@promote ~]# sed '3a \new line' /etc/fstab 

#
# /etc/fstab
new line
# Created by anaconda on Sun Apr 14 09:29:51 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
[root@promote ~]# sed '3a \new line\nanother new line' /etc/fstab 

#
# /etc/fstab
new line
another new line
# Created by anaconda on Sun Apr 14 09:29:51 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
**i  \text：在行前面插入文本“text”，支持使用\n实现多行插入**
```
[root@promote ~]# sed '3i \new line' /etc/fstab 

#
new line
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
```
**c  \text：把匹配到的行替换为此处指定的文本“text”**
```
[root@promote ~]# sed '4c \change line' /etc/fstab 

#
# /etc/fstab
change line
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
**w /PATH/TO/SOMEFILE：保存模式空间匹配到的行至指定的文件中**
```
[root@promote ~]# sed -n '/^[^#]/p' /etc/fstab 
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
[root@promote ~]# sed '/^[^#]/w /tmp/fstab.new' /etc/fstab 

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
[root@promote ~]# cat /tmp/fstab.new 
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
**r  /PATH/FROM/SOMEFILE：读取指定文件的内容至当前文件被模式匹配到的行后面；文件合并；**
```
[root@promote ~]# sed '3r /etc/issue' /etc/fstab 

#
# /etc/fstab
\S
Kernel \r on an \m

# Created by anaconda on Sun Apr 14 09:29:51 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
**=：为模式匹配到的行打印行号**
```
[root@promote ~]# sed '/^UUID/=' /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Sun Apr 14 09:29:51 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
10
/dev/mapper/centos_promote-root /                       xfs     defaults        0 0
11
UUID=273deb66-d03c-457f-8b29-5df019b3e53a /boot                   xfs     defaults        0 0
/dev/mapper/centos_promote-home /home                   xfs     defaults        0 0
/dev/mapper/centos_promote-swap swap                    swap    defaults        0 0
```
**!：条件取反**
格式：地址定界!编辑命令
```
[root@promote ~]# sed '/^#/!d' /etc/fstab 
#
# /etc/fstab
# Created by anaconda on Sun Apr 14 09:29:51 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
```

**s///：查找替换，其分隔符可自行指定，常用的有s@@@, s###等**
替换标记：
>**g：全局替换**
>**w /PATH/TO/SOMEFILE：将替换成功的结果保存至指定文件中**
>**p：显示替换成功的行**

练习1：删除/boot/grub/grub2.cfg文件中所有以空白字符开头的行的行首的所有空白字符；

    [root@promote ~]# sed  's@^[[:space:]]\+@@' /etc/grub2.cfg
练习2：删除/etc/fstab文件中所有以#开头的行的行首的#号及#后面的所有空白字符；
    
    [root@promote ~]# sed  's@^#[[:space:]]*@@'  /etc/fstab
练习3：输出一个绝对路径给sed命令，取出其目录，其行为类似于dirname；
    
    [root@promote ~]# echo "/var/log/messages/" | sed 's@[^/]\+/\?$@@'
    [root@promote ~]# echo "/var/log/messages" | sed -r 's@[^/]+/?$@@'

**高级编辑命令：**
>h：把模式空间中的内容覆盖至保持空间中；
>H：把模式空间中的内容追加至保持空间中；
>g：把保持空间中的内容覆盖至模式空间中；
>G：把保持空间中的内容追加至模式空间中；      
>x：把模式空间中的内容与保持空间中的内容互换；         
>n：覆盖读取匹配到的行的下一行至模式空间中；         
>N：追加读取匹配到的行的下一行至模式空间中；          
>d：删除模式空间中的行；     
>D：删除多行模式空间中的所有行；

**示例：**
>sed  -n  'n;p'  FILE：显示偶数行；
>sed  '1!G;h;\$!d'  FILE：逆序显示文件的内容；
>sed  ’\$!d'  FILE：取出最后一行；
>sed  '\$!N;\$!D' FILE：取出文件后两行；
>sed '/^$/d;G' FILE：删除原有的所有空白行，而后为所有的非空白行后添加一个空白行；
>sed  'n;d'  FILE：显示奇数行；
>sed 'G' FILE：在原有的每行后方添加一个空白行；

**练习：用bash实现统计访问日志文件中状态码大于等于400的IP数量并排序**
```
[root@localhost ~]# cat nginx_access_log | cut -d' ' -f1,9 | grep '\<[4-9][0-9][0-9]\>' | cut -d' ' -f1 | sort | uniq -c | sort -r
      7 100.109.195.28
      4 100.109.195.26
      2 100.109.195.27
```