##### 文本编辑器：
文本：纯文本，ASCII text   非丰富格式文本
        
##### 文本编辑种类：
* 行编辑器：sed
* 全屏编辑器：nano, vi
        

**VI**：Visual Interface，可视化接口。是一个文本编辑器，主要在Unix及类Unix环境中使用。
**Vim**：Vim=Vi+IMproved。Vim在Vi的基础之上进行了功能提升，相当于Vi的增强版，主要特点为：
>①支持多级撤销。Vi中通过字母u撤销上一级操作，Vim则可撤销多级操作
>②支持语法高亮
>③可跨平台使用。Vim可运行在Windows环境，安装支持Vim的组件，如：git-bash
>④可编辑压缩格式文件（gzip、zip等）


#### vim：模式化的编辑器
**基本模式：**
* 编辑模式，命令模式
* 输入模式
* 末行模式：内置的命令行接口
                    

**打开文件：**
`vim [options] [file ..]`
|   命令    |                           作用                            |
| :-------: | :-------------------------------------------------------: |
|    +#     |           打开文件后，直接让光标处于第#行的行首           |
| +/PATTERN | 打开文件后，直接让光标处于第一个被PATTERN匹配到的行的行首 |


```
[root@node1 ~]# vim +/if functions
匹配if字符的行首
```

**模式转换：**
>编辑模式：默认模式
>编辑模式 --> 输入模式：

| 命令 |              作用              |
| :--: | :----------------------------: |
|  i   |    insert, 在光标所在处输入    |
|  a   |   append，在光标在处后方输入   |
|  o   | 在光标所在处的下方打开一个新行 |
|  I   |     在光标所在行的行首输入     |
|  A   |     在光标所在行的行尾输入     |
|  O   | 在光标所在处的上方打开一个新行 |

>输入模式 --> 编辑模式
>            ESC
>编辑模式 --> 末行模式
>             :
>末行模式 --> 编辑模式
>            ESC

**关闭文件：**

>编辑模式下：
>ZZ：保存并退出；

末行模式：           
|         命令         |              作用              |
| :------------------: | :----------------------------: |
|          :q          |              退出              |
|          q!          | 强制退出，不保存此前的编辑操作 |
|         :wq          |           保存并退出           |
|          :x          |           保存并退出           |
| :w /PATH/TO/SOMEFILE |        保存至指定文件中        |

**光标跳转：**
* 字符间跳转

| 命令 | 作用 |
| :--: | :--: |
|  h   |  左  |
|  j   |  下  |
|  k   |  上  |
|  l   |  右  |

>记忆方法：日本在韩国的下面
>
>\#COMMAND：跳转由#指定的个数的字符，如向右跳转10个，10L

* 单词间跳转

|   命令   |          作用           |
| :------: | :---------------------: |
|    w     |    下一个单词的词首     |
|    e     | 当前或后一个单词的词尾  |
|    b     | 当前或前一个单词的词首  |
| #COMMAND | 跳转由#指定的个数的单词 |

* 行首行尾跳转

| 命令 |             作用             |
| :--: | :--------------------------: |
|  ^   | 跳转至行首的第一个非空白字符 |
|  0   |          跳转至行首          |
|  $   |          跳转至行尾          |


* 行间跳转

|  命令  |       作用        |
| :----: | :---------------: |
|   #G   | 跳转至由#指定的行 |
| 1G, gg |      第一行       |
|   G    |     最后一行      |

* 句间跳转：
         )
         (

* 段间跳转
         }
         {


**翻屏：**
|  命令  |       作用       |
| :----: | :--------------: |
| Ctrl+f | 向文件尾部翻一屏 |
| Ctrl+b | 向文件首部翻一屏 |
| Ctrl+d | 向文件尾部翻半屏 |
| Ctrl+u | 向文件首部翻半屏 |
| Enter  |    按行向后翻    |

#### vim的编辑命令：

**字符编辑：**
| 命令 |                   作用                   |
| :--: | :--------------------------------------: |
|  x   |           删除光标所在处的字符           |
|  #x  |       删除光标所在处起始的#个字符        |
|  xp  | 交换光标所在处的字符与其后面的字符的位置 |

**替换命令(replace)：**
                
    r：替换光标所在处的字符；
    rCHAR

**删除命令：**
| 命令 |                             作用                             |
| :--: | :----------------------------------------------------------: |
|  d   |          删除命令，可结合光标跳转字符，实现范围删除          |
|  d$  |                删除当前光标所在处至行尾的内容                |
|  d^  |                删除当前光标所在处至行首的内容                |
|  db  | 删除当前或前一个单词的词首的内容（删除当前光标处所在的单词） |
|  de  |  删除当前或后一个单词词尾的内容（删除当前光标处所在的单词）  |
|  dw  |   删除一直到下个单词词首的内容（删除当前光标处所在的单词）   |
|  dd  |                      删除光标所在处的行                      |
| #dd  |                 删除光标所处的行起始的共#行                  |

**粘贴命令(p, put, paste)：**
|   命令    |                             作用                             |
| :-------: | :----------------------------------------------------------: |
| p（小写） | 缓冲区中的内容如果为整行，则粘贴在当前光标所在行的下方；否则，则粘贴至当前光标所在处的后方 |
| P（大写） | 缓冲区中的内容如果为整行，则粘贴在当前光标所在行的上方；否则，则粘贴至当前光标所在处的前方 |


**复制命令(yank, y)：**
| 命令 |                      作用                      |
| :--: | :--------------------------------------------: |
|  y   |           复制，工作行为相似于d命令            |
|  y$  |         复制当前光标所在处至行尾的内容         |
|  y^  | 复制当前光标所在处至行首的第一个非空白字符内容 |
|  y0  |         复制当前光标所在处至行首的内容         |
|  yb  |        复制当前或前一个单词的词首的内容        |
|  ye  |         复制当前或后一个单词词尾的内容         |
|  yw  |          复制一直到下个单词词首的内容          |
|  yy  |                   复制一整行                   |
| #yy  |                    复制#行                     |


​                        
**改变命令(change, c)：**
>编辑模式 --> 输入模式，实现删除操作

| 命令 |                      作用                      |
| :--: | :--------------------------------------------: |
|  c$  |         改变当前光标所在处至行尾的内容         |
|  c^  | 改变当前光标所在处至行首的第一个非空白字符内容 |
|  c0  |         改变当前光标所在处至行首的内容         |
|  cb  |        改变当前或前一个单词的词首的内容        |
|  ce  |         改变当前或后一个单词词尾的内容         |
|  cw  |          改变一直到下个单词词首的内容          |
|  cc  |       删除光标所在的行，并转换为输出模式       |
| #cc  |                    改变#行                     |

##### 其它编辑操作：

**可视化模式：**
| 命令 |    作用    |
| :--: | :--------: |
|  v   | 按字符选定 |
|  V   |  按行选定  |
结合编辑命令使用：d, c, y
                    
**撤销(undo)操作：**
| 命令 |       作用        |
| :--: | :---------------: |
|  u   |  撤销此前的操作   |
|  #u  | 撤销此前的#个操作 |

**撤销此前的撤销：恢复此前的操作**
    `Ctrl+r`
                    
**重复执行前一个编辑操作：   .   （点号）**
vim自带的练习教程：vimtutor
            
#### vim末行模式：
>内建的命令行接口

**(1) 地址定界**
`:start_pos[,end_pos]`
|   命令    |                             作用                             |
| :-------: | :----------------------------------------------------------: |
|     #     |                  特定的第#行，例如5即第5行                   |
|     .     |                            当前行                            |
|     $     |                           最后一行                           |
|    #,#    |            指定行范围，左侧为起始行，右侧为结束行            |
|   #,+#    | 指定行范围，左侧为起始行绝对编号，右侧为相对左侧行号的偏移量；例如：3,+7 |
|   .,$-1   |                     从当前行到倒数第二行                     |
|    1,$    |                             全文                             |
|     %     |                             全文                             |
| /pattern/ |      从光标所在处起始向文件尾部第一次被模式所匹配到的行      |
|/first/,$ |从第一次能被first的行到最后一行  
|   /pat1/,/pat2/|从光标所在处起始，第一次由pat1匹配到的行开始，至第一次由pat2匹配到的行结束之间的所有行|                   

**可同编辑命令一同使用，实现编辑操作：d，y，c**
>w /PATH/TO/SOMEFILE：将范围内的文本保存至指定的文件中；
> r  /PATH/FROM/SOMEFILE：将指定的文件中的文本读取并插入至指定位置；

**(2) 查找**
|   命令   |                             作用                             |
| :------: | :----------------------------------------------------------: |
| /PATTERN | 从当前光标所在处向文件尾部查找能够被当前模式匹配到的所有字符串 |
| ?PATTERN | 从当前光标所在处向文件首部查找能够被当前模式匹配到的所有字符串 |
|    n     |                    下一个，与命令方向相同                    |
|    N     |                    上一个，与命令方向相反                    |

**(3) 查找并替换**
>s：末行模式的命令
>使用格式：
>s/要查找的内容/替换为的内容/修饰符
- 要查找的内容：可使用正则表达式；
- 替换为的内容：不能使用下则表达式，但可以引用；


**如果“要查找的内容”部分在模式中使用分组符号：在“替换为的内容”中使用后向引用；
直接引用查找模式匹配到的全部文本，要使用&符号；**

**修饰符：**
| 命令 |                      作用                      |
| :--: | :--------------------------------------------: |
|  i   |                   忽略大小写                   |
|  g   | 全局替换，意味着一行中如果匹配到多次，则均替换 |

 ```   
：1，20s/this/THIS/ig
搜索第1行到第20行内所有的this全部替换为THIS
 ```
可把分隔符替换为其它非常用字符：
>s@@@
>s###

**示例：**
>**全文中所有t开头的单词通通换成T，后面保持不变**
>`%s@\<t\([[:alpha:]]\+\)\>@T\1@g`
>**全文中所有t开头的单词尾部都加上er**
>`%s@\<t[[:alpha:]]\+\>@&er@g`

**练习：**
>**1、复制/etc/grub2.cfg文件至/tmp目录中，用查找替换命令删除/tmp/grub2.cfg文件中以空白字符开头的行的行首的空白字符**
>`%s@^[[:space:]]\+@@`
>**2、复制/etc/rc.d/init.d/functions文件至/tmp目录中，用查找替换命令为/tmp/functions文件的每个以空白字符开头的行的行首加上#**
>`%s@^[[:space:]]\+[^[:space:]]@#&@g`
>**3、为/tmp/grub2.cfg文件的前三行的行首加上#号**
>`1,3s@^@#&`             
>**4、将/etc/yum.repos.d/CentOS-Base.repo文件中所有的enabled=0替换为enabled=1，所有gpgcheck=0替换为gpgcheck=1**
>`%s@\(enabled\|gpgcheck\)=0@\1=1@g`

#### vim的多文件功能：
* 多文件：
         vim FILE1 FILE2 ...
             

**在文件间切换：**
|  命令  |   作用   |
| :----: | :------: |
| :next  |  下一个  |
| :prev  |  上一个  |
| :first |  第一个  |
| :last  | 最后一个 |

**退出所有文件：**
|  命令  |        作用        |
| :----: | :----------------: |
| :wqall | 保存所有文件并退出 |
| :wall  |    保存所有文件    |
| :qall  |    退出所有文件    |

* 多窗口：

| 命令 |     作用     |
| :--: | :----------: |
|  -o  | 水平分割窗口 |
|  -O  | 垂直分割窗口 |


**在窗口间切换：Ctrl+w, ARROW**
            
注意：单个文件也可以分割为多个窗口进行查看：
|   命令    |     作用     |
| :-------: | :----------: |
| Ctrl+w, s | 水平分割窗口 |
| Ctrl+w, v | 垂直分割窗口 |


#### 定制vim的工作特性：

注意：在末行模式下的设定，仅对当前vim进程有效；

**永久有效：**
* 全局：/etc/vimrc
* 用户个人：～/.vimrc
            
|定制特性|启用|禁用|
|:---: |:---:|:---: |
|行号|set number| set nomber|
|括号匹配高亮|set showmatch|set nosm|
|自动缩进| set ai| set noai|
|高亮搜索|set  hlsearch|set nohlsearch|
|语法高亮|syntax on|syntax off|
|忽略字符大小写|set ic|set noic|

 **获取帮助：**
>:help
>:help subject

**课外作业：如何设置tab键缩进4个字符**
打开设置文件：

```
vim /etc/vim/vimrc 
```
添加如下:
```
set tabstop=4
按一次tab前进4个字符 
set softtabstop=4
用空格代替tab 
set expandtab
设置自动缩进 
set shiftwidth=4
设置多行缩进时有用
```