#### 编译内核：
> 前提：开发环境（开发工具，开发库），头文件：/usr/include
> 开源：源代码 --> 可执行格式
> 发行版：以“通用”的目标编译应用程序

#### 前提：
>(1) 准备好开发环境；
>(2) 获取目标主机上硬件设备的相关信息；
>(3) 获取到目标主机系统功能的相关信息，例如要启用的文件系统；
>(4) 获取内核源代码包：[www.kernel.org](http://www.kernel.org/)

#### 准备开发环境：
**CentOS 6.7：**
>包组：
>Development Tools
>Server Platform Development

**CentOS 7：**
>包组：
>Development Tools
>Server Platform Development

包：
ncurses-devel
#### 获取目标主机上硬件设备的相关信息：
**CPU：**
>`cat  /proc/info`
>`lscpu`
>`x86info -a`

**PCI设备：**
>`lspci`
>-v：查看每个设备的详细信息
>-vv：更详细的信息
>
>`lsusb`
>-v：查看每个设备的详细信息
>-vv：更详细的信息
>
>`lsblk`
>
>了解全部硬件设备信息：
>`hal-device`

#### 内核编译过程：
**步骤：**
```
[root@promote ~]# tar  xf  linux-3.10.67.tar.xz  -C  /usr/src   解压安装包
[root@promote ~]# cd  /usr/src
[root@promote ~]# ln  -s  linux-3.10.67  linux  创建符号链接
[root@promote ~]# cd  linux
[root@promote ~]# make menuconfig           配置内核选项
[root@promote ~]# make  [-j #]            编译内核，可使用-j指定编译线程数量
[root@promote ~]# make modules_install     安装内核模块
[root@promote ~]# make install            安装内核核心
```
**重启系统，选择使用新内核**
**screen命令：**
>打开screen：`screen`
>拆除screen： Ctrl+a, d
>列出screen： `screen  -ls`
>连接至screen：`screen  -r   SCREEN_ID`
>关闭screen:  `exit`

#### 过程的详细说明：
**(1)  配置内核选项**
>支持“更新”模式进行配置：在已有的.config文件的基础之上进行“修改”配置；
>(a) make config：基于命令行以遍历的方式去配置内核中可配置的每个选项；
>(b) make  menuconfig：基于cureses的文本配置窗口；
>(c) make  gconfig：基于GTK开发环境的窗口界面；  包组“桌面平台开发”
>(d) make  xonfig：基于QT开发环境的窗口界面；

>支持“全新配置”模式进行配置：
>(a) make  defconfig：基于内核为目标平台提供的“默认”配置为模板进行配置；
>(b) make   allnoconfig：所有选项均为“no”；

**(2) 编译**
>(a) 多线程编译：make  [-j #]
>(b) 编译内核中的一部分代码：
>(i) 只编译某子目录中的相关代码：
>`cd  /usr/src/linux`
>`make  path/to/dir/`
>(ii)只编译一个特定的模块
>` cd  /usr/src/linux`
>`make  path/to/dir/file.ko`
>(c) 如何交叉编译：
>目标平台与当前编译操作所在的平台不同；
>`make  ARCH=arch_name`
>
>要获取特定目标平台的使用帮助：                    
>`make  ARCH=arch_name help`

**(3) 如何在执行过编译操作的内核源码树上做重新编译：**
>事先清理操作：
>`make clean`：清理编译生成的绝大多数文件，但会保留config，及编译外部模块所需要的文件；
>`make mrproper`：清理编译生成的所有文件，包括配置生成的config文件及某些备份文件；
>`make distclean`：相当于mrproper，额外清理各种patches以及编辑器备份文件；