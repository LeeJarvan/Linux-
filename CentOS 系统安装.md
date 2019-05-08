#### 安装程序：anaconda
>bootloader --> kernel(initrd(rootfs)) --> anaconda

**anaconda：**
>Anaconda是RedHat、CentOS、Fedora等Linux的安装管理程序。它可以提供文本、图形等安装管理方式，并支持Kickstart等脚本提供自动安装的功能。此外，其还支持许多启动参数，熟悉这些参数可为安装带来很多方便。该程序的功能是把位于光盘或其他源上的数据包，根据设置安装到主机上。为实现该定制安装，它提供一个定制界面，可以实现交互式界面供用户选择配置（如选择语言，键盘，时区等信息）。Anaconda的大部分模块用Python编写，有少许的载入模块用C编写。

>tui：基于cureses的文本配置窗口
>gui：图形界面

#### CentOS的安装过程启动流程：
>MBR：cdrom/isolinux/boot.cat光盘上的引导加载器，相当于Bootloader
```
[root@promote ~]# ls /media/cdrom/isolinux/
boot.cat  grub.conf   isolinux.bin  memtest     TRANS.TBL     vmlinuz
boot.msg  initrd.img  isolinux.cfg  splash.png  vesamenu.c32
```
>Stage2：isolinux/isolinux.bin，配置文件：isolinux/isolinux.cfg
>每个对应的菜单选项：
>加载内核：isolinux/vmlinuz
>向内核传递参数：append  initrd=initrd.img

>装载根文件系统，并启动anaconda
>默认界面是图形界面：512MB+内存空间；
>若需要显示指定启动TUI接口：向启动内核传递一个参数“text”即可；
>(1)按tab键,在后面增加text
>(2)按ESC键：boot（提示符下）: linux text

**注意：上述内容一般位于引导设备，例如可通过光盘、U盘或网络等；
后续的anacona及其安装用到的程序包等可以来自于程序包仓库，此仓库的位置可以为：本地光盘、本地硬盘、ftp server、http server、nfs server
如果想手动指定安装仓库：按 ESC键：boot（提示符下）：linux method**
```
[root@promote isolinux]# less isolinux.cfg
······
default vesamenu.c32
timeout 600

display boot.msg

# Clear the screen when exiting the menu, instead of leaving the menu displayed.
# For vesamenu, this means the graphical background is still displayed without
# the menu itself for as long as the screen remains in graphics mode.
menu clear
menu background splash.png
menu title CentOS 7
menu vshift 8
menu rows 18
menu margin 8
#menu hidden
menu helpmsgrow 15
menu tabmsgrow 13

# Border Area
menu color border * #00000000 #00000000 none

# Selected item
menu color sel 0 #ffffffff #00000000 none

# Title bar
menu color title 0 #ff7ba3d0 #00000000 none

# Press [Tab] message
menu color tabmsg 0 #ff3a6496 #00000000 none

# Unselected menu item
```

#### anaconda的工作过程：
##### 安装前配置阶段
>安装过程使用的语言
>键盘类型
>安装目标存储设备
>- Basic Storage：本地磁盘
>- Special Storage： iSCSI
>
>设定主机名
>配置网络接口
>时区
>管理员密码
>设定分区方式及MBR的安装位置
>创建一个普通用户
>选定要安装的程序包

##### 安装阶段
>在目标磁盘创建分区并执行格式化；
>将选定的程序包安装至目标位置；
>安装bootloader；

##### 首次启动
>iptables 防火墙
>selinux
>core dump 核心转储，内核崩溃时将内存中所有数据创建映像文件保存在磁盘上可取出分析崩溃原因

#### anaconda的配置方式：
>(1) 交互式配置方式；
>(2) 支持通过读取配置文件中事先定义好的配置项自动完成配置；遵循特定的语法格式，此文件即为**kickstart**文件；

#### 安装引导选项：
**boot**:
>text：文本安装方式
>method：手动指定使用的安装方法

**与网络相关的引导选项：**
>ip=IPADDR
> netmask=MASK
>gateway=GW（网关）
>dns=DNS_SERVER_IP

**远程访问功能相关的引导选项：**
>vnc
>vncpassword='PASSWORD'

**启动紧急救援模式：**

>rescue
>装载额外驱动：
>dd

**参考：[www.redhat.com/docs](http://www.redhat.com/docs) , 《installation guide》官方文档**