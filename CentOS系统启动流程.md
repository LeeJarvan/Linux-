#### Linux系统的组成部分：内核+根文件系统

**运行中的系统环境可分为两层：内核空间、用户空间**
- 用户空间：应用程序（进程或线程）
- 内核空间：内核代码（系统调用）
        

**内核设计流派：**
- 单内核设计：把所有功能集成于同一个程序；
  Linux
- 微内核设计：每种功能使用一个单独的子系统实现；
  Windows, Solaris

##### Linux内核特点：
支持模块化：  .ko (kernel object)
支持模块运行时动态装载或卸载；
            
**组成部分：**
>核心文件：/boot/vmlinuz-VERSION-release
```
[root@promote ~]# ls /boot
config-3.10.0-957.el7.x86_64                             initramfs-3.10.0-957.el7.x86_64kdump.img
efi                                                      symvers-3.10.0-957.el7.x86_64.gz
grub                                                     System.map-3.10.0-957.el7.x86_64
grub2                                                    vmlinuz-0-rescue-6f14150e17f24a19917ff162dd467b32
initramfs-0-rescue-6f14150e17f24a19917ff162dd467b32.img  vmlinuz-3.10.0-957.el7.x86_64
initramfs-3.10.0-957.el7.x86_64.img
```
>ramdisk：把内存当磁盘用
>CentOS 5：/boot/initrd-VERSION-release.img
>CentOS 6,7：/boot/initramfs-VERSION-release.img

>模块文件：/lib/modules/VERSION-release
 ```
[root@promote ~]# ls /lib/modules
3.10.0-957.el7.x86_64
 ```
### CentOS 系统的启动流程：
#### POST：加电自检
- ROM：CMOS
- BIOS：Basic Input and Output System
                

CPU可访问地址空间：ROM+RAM
            
#### Boot Sequence：
按次序查找各引导设备，第一个有引导程序的设备即为本次启动要用到的设备；
            
>bootloader：引导加载器，程序；
>- Windows：ntloader
>- Linux：
>  LILO：LIinux  LOader
>  GRUB：Grand Uniform Bootloader
>  GRUB 0.X：Grub Legacy（CentOS5，6）
>  GRUB 1.X：Grub2（CentOS7）

**功能：提供一个菜单，允许用户选择要启动的系统或不同的内核版本； 把用户选定的内核装载到RAM中的特定空间中，解压、展开，而后把系统控制权移交给内核；**
                    
**MBR：Master Boot Record**
>512bytes：
>446bytes：bootloader
>64bytes：fat（文件系统分区表）
>2bytes：55AA（有效MBR）

**GRUB：**
>bootloader：1st stage
>Disk Partition：filesystem driver, 1.5 stage
>Disk Partition：/boot/grub, 2nd stage
>注意：UEFI，GPT

#### 加载Kernel：
**自身初始化：**
>探测可识别到的所有硬件设备；
>加载硬件驱动程序；（有可能会借助于ramdisk加载驱动）
>以只读方式挂载根文件系统；
>运行用户空间的第一个应用程序：/sbin/init

**init程序的类型：**
>CentOS 5-：SysV init
>配置文件：/etc/inittab
>                 
>CentOS 6：Upstart
>配置文件：/etc/inittab，/etc/init/*.conf
>                       
>CentOS 7：Systemd
>配置文件：/usr/lib/systemd/system/, /etc/systemd/system/

**ramdisk：**
拟内存盘是通过软件将一部分内存（RAM）模拟为硬盘来使用的一种技术
Linux内核的特性之一：使用缓冲和缓存来加速对磁盘上的文件访问；
ramdisk --> ramfs
ramfs是一个简单的文件系统，它是基于ram的动态文件系统的一种Linux硬盘缓冲机制；


>CentOS 5: initrd
>工具程序：mkinitrd
>
>CentOS 6,7: initramfs
>工具程序：dracut, mkinitrd

**initrd作用**
>Initrd ramdisk或者initrd是指一个临时文件系统，它在启动阶段被Linux内核调用。initrd主要用于当“根”文件系统被挂载之前，进行准备工作。
>同其他Unix系统一样，Linux操作系统首先要将内核引导入内存。内核驻留于操作系统与应用程序的整个活动周期，其中应用程序（软件）在“用户空间”内运行，位于内核控制之下。
>为了使加载存储器最小化，一些核心 Linux 程序转化成模块形式，可以动态加载系统中。
>initrd 系统中的文件在引导阶段可以被核心访问，里面的内容会被挂载成一个 loop 类型的文件，早期是将 initrd 放在小的软盘片内。initrd 通常被压缩成 gzip 类型，在引导的时候由 bootloader(LILO, GRUB) 来告知核心 initrd 的位置。不过在2.6版本内核之后出现了initramfs，它和initrd实现同样的功能，但是它基于一种cpio档，无须挂载就可以展开成一个文件系统，因此省去了各种相关的权限，在自动化方面更方便了。

**系统初始化流程（内核级别）： POST --> BootSequence(BIOS) --> BootLoader（MBR）--> Kernel（ramdisk）--> rootfs（readonly）--> /sbin/init ()**
            
### /sbin/init：
#### CentOS 5： SysV init*    

**运行级别：为了系统的运行或维护等目的而设定的机制**
>0-6：7个级别
>0：关机, shutdown
>1：单用户模式(single user)，root用户，无须认证；维护模式；
>2、多用户模式(multi user)，会启动网络功能，但不会启动NFS（网络文件系统）；维护模式；
>3、多用户模式(mutli user)，完全功能模式；文本界面；
>4、预留级别：目前无特别使用目的，但习惯以同3级别功能使用；
>5、多用户模式(multi user)， 完全功能模式，图形界面；
>6、重启，reboot
>
>默认级别：3, 5

**级别切换：`init #`**
**级别查看：**
```
[root@promote ~]# who -r
         run-level 3  2019-04-29 11:49
[root@promote ~]# runlevel
N 3
```
**配置文件：/etc/inittab**
>每行定义一种action以及与之对应的process
>格式：id:runlevels:action:process

>id：一个任务的标识符
>runlevels：在哪些级别启动此任务；#，###，也可以为空，表示所有级别
>action：在什么条件下启动此任务
>process：任务

>action：
>wait：等待切换至此任务所在的级别时执行一次；
>respawn：一旦此任务终止，就自动重新启动之；
>initdefault：设定默认运行级别；此时，process省略；
>sysinit：设定系统初始化方式，此处一般为指定/etc/rc.d/rc.sysinit脚本；

**例如：**
>id:3:initdefault: 设定默认运行级别为3
>si::sysinit:/etc/rc.d/rc.sysinit 完成系统初始化
>                     
>l0:0:wait:/etc/rc.d/rc  0
>l1:1:wait:/etc/rc.d/rc  1
>... ...
>l6:6:wait:/etc/rc.d/rc  6
>意味着去启动或关闭/etc/rc.d/rc3.d/目录下的服务脚本所控制服务；

>K\*：要停止的服务；K##\*，优先级，数字越小，越是优先关闭；依赖的服务先关闭，而后关闭被依赖的；
>S\*：要启动的服务；S##\*，优先级，数字越小，越是优先启动；被依赖的服务先启动，而依赖的服务后启动；

##### rc脚本：接受一个运行级别数字为参数
**脚本框架：**
```
or  srv  in  /etc/rc.d/rc#.d/K*; do
     $srv  stop
done
                                        
for  srv  in  /etc/rc.d/rc#.d/S*; do
      $srv  start
done
```
**/etc/init.d/\* (/etc/rc.d/init.d/\*)脚本执行方式：**
>\# /etc/init.d/SRV_SCRIPT  {start|stop|restart|status}
>\# service  SRV_SCRIPT   {start|stop|restart|status}

##### chkconfig命令：管控/etc/init.d/每个服务脚本在各级别下的启动或关闭状态；
>查看：chkconfig  --list   [name]
>添加：chkconfig  --add  name
>删除：chkconfig  --del  name                                        

**能被添加的服务的脚本定义格式之一：**
```
#!/bin/bash
#
# chkconfig: 级别 数字 数字
# description:  
```
**例如：**
```
[root@promote ~]# cat /etc/init.d/crond
#!/bin/sh
#
# crond          Start/Stop the cron clock daemon.
#
# chkconfig: 2345 90 60
# description: cron is a standard UNIX program that runs user-specified \
#              programs at periodic scheduled times. vixie cron adds a \
#              number of features to the basic UNIX cron, including better \
#              security and more powerful configuration options.
```
**修改指定的链接类型：**
>chkconfig  [--level  LEVELS]  name  <on|off|reset>
>--level LEVELS：指定要控制的级别；默认为2345；

注意：正常级别下，最后启动的一个服务S99local没有链接至/etc/init.d下的某脚本，而是链接至了/etc/rc.d/rc.local （/etc/rc.local）脚本；因此，不便或不需写为服务脚本的程序期望能开机自动运行时，直接放置于此脚本文件中即可。
                                
**启动虚拟终端：**
>tty1:2345:respawn:/usr/sbin/mingetty tty1
>... ...
>tty6:2345:respawn:/usr/sbin/mingetty tty6    
>（1）mingetty会调用login程序；
>（2）打开虚拟终端的程序除了mingetty之外，还有诸如getty等；


##### 系统初始化脚本：/etc/rc.d/rc.sysinit
>(1) 设置主机名；
>(2) 设置欢迎信息；
>(3) 激活udev和selinux；
>(4) 挂载/etc/fstab文件中定义的所有文件系统；
>(5) 检测根文件系统，并以读写方式重新挂载根文件系统；
>(6) 设置系统时钟；
>(7) 根据/etc/sysctl.conf文件来设置内核参数；
>(8) 激活lvm及软raid设备；
>(9) 激活swap设备；
>(10) 加载额外设备的驱动程序；
>(11) 清理操作；

**总结（用户空间的启动流程）： /sbin/init (/etc/inittab)
设置默认运行级别 --> 运行系统初始化脚本，完成系统初始化 --> 关闭对应级别下需要停止的服务，启动对应级别下需要开启的服务--> 设置登录终端 [--> 启动图形终端]**
                    
#### CentOS 6：
>init程序：upstart，但依然命名为/sbin/init，
>配置文件：/etc/init/*.conf, /etc/inittab（仅用于定义默认运行级别）
```
[root@promote ~]# ls /etc/init
control-alt-delete.conf  prefdm.conf         rcS-emergency.conf        readahead-disable-services.conf  tty.conf
init-system-dbus.conf    quit-plymouth.conf  rcS-sulogin.conf          serial.conf
kexec-disable.conf       rc.conf             readahead-collector.conf  splash-manager.conf
plymouth-shutdown.conf   rcS.conf            readahead.conf            start-ttys.conf
```
*注意：\*.conf为upstart风格的配置文件*                        
#### CentOS 7：
>init程序：systemd
>配置文件：/usr/lib/systemd/system/*,  /etc/systemd/system/*
>完全兼容SysV脚本机制；因此，service命令依然可用；不过，建议使用systemctl命令来控制服务；
>\# systemctl  {start|stop|restart|status}  name[.service]
