**内核设计体系：单内核、微内核**
**Linux：单内核设计，但充分借鉴了微内核体系的设计的优点；为内核引入了模块化机制；**

#### 内核的组成部分：
- kernel：内核核心，一般为bzImage，通常位于/boot目录，名称为vmlinuz-VERSION-release
- kernel object：内核对象，即内核模块，一般放置于/lib/modules/VERSION-release/
  **内核模块与内核核心版本一定要严格匹配**
>[   ]：N，不要此功能
>[M]：Module 编译成内核模块
>[*]：Y，编译进内核核心

**内核模块支持动态装载和卸载**
- ramdisk：辅助性文件，一个简装版的根文件系统，这取决于内核是否能直接驱动rootfs所在的设备；
  目标设备驱动，例如SCSI设备的驱动；
  逻辑设备驱动，例如LVM设备的驱动；
  文件系统，例如xfs文件系统；

#### 内核信息获取
##### uname命令：
>print system information

格式：uname [OPTION]...
**-v：编辑版本号**
```
[root@promote ~]# uname -v
#1 SMP Thu Jul 23 15:44:03 UTC 2015
```
**-r：内核的release号**
```
[root@promote ~]# uname -r 
2.6.32-573.el6.x86_64
```
**-n：主机名**
```
[root@promote ~]# uname -n
promote.cache-dns.local
[root@promote ~]# hostname
promote.cache-dns.local
```

**-a：显示所有信息**
```
[root@promote ~]# uname -a
Linux promote.cache-dns.local 2.6.32-573.el6.x86_64 #1 SMP Thu Jul 23 15:44:03 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
```
**文件：/boot/vmlinuz-VERSION-release**

#### 模块信息获取和管理：
#####lsmod命令：
>Show the status of modules in the Linux Kernel

```
[root@promote ~]# lsmod
Module                  Size  Used by
nls_utf8                1455  1 
fuse                   79892  2 
rfcomm                 71079  4 
sco                    17493  2 
bridge                 84537  0 
bnep                   16370  2 
l2cap                  54306  16 rfcomm,bnep
autofs4                27000  3 
8021q                  20362  0 
garp                    7152  1 8021q
stp                     2218  2 bridge,garp
llc                     5418  3 bridge,garp,stp
ipt_REJECT              2351  2 
nf_conntrack_ipv4       9154  2 
nf_defrag_ipv4          1483  1 nf_conntrack_ipv4
iptable_filter          2793  1 
ip_tables              17831  1 iptable_filter
ip6t_REJECT             4340  2 
nf_conntrack_ipv6       7985  3 
nf_defrag_ipv6         26468  1 nf_conntrack_ipv6
xt_state                1492  5 
nf_conntrack           79206  3 nf_conntrack_ipv4,nf_conntrack_ipv6,xt_state
ip6table_filter         2889  1 
ip6_tables             18732  1 ip6table_filter
ipv6                  335589  286 bridge,ip6t_REJECT,nf_conntrack_ipv6,nf_defrag_ipv6
uinput                  8120  0 
sg                     29318  0 
btusb                  16915  2 
bluetooth              97895  9 rfcomm,sco,bnep,l2cap,btusb
rfkill                 19255  3 bluetooth
microcode             112205  0 
serio_raw               4626  0 
e1000                 134876  0 
vmware_balloon          7199  0 
i2c_piix4              11232  0 
i2c_core               29132  1 i2c_piix4
shpchp                 29130  0 
ext4                  378683  3 
jbd2                   93252  1 ext4
mbcache                 8193  1 ext4
sd_mod                 37030  3 
crc_t10dif              1209  1 sd_mod
sr_mod                 15049  1 
cdrom                  39085  1 sr_mod
mptspi                 16411  2 
mptscsih               36638  1 mptspi
mptbase                93615  2 mptspi,mptscsih
scsi_transport_spi     25447  1 mptspi
pata_acpi               3701  0 
ata_generic             3837  0 
ata_piix               24409  1 
dm_mirror              14384  0 
dm_region_hash         12085  1 dm_mirror
dm_log                  9930  2 dm_mirror,dm_region_hash
dm_mod                 99200  11 dm_mirror,dm_log
```
**显示的内核来自于/proc/modules**
##### modinfo命令：
>Show information about a Linux Kernel module

格式：modinfo [-F field] [-k kernel] [modulename|filename...]
```
[root@promote ~]# modinfo ext4
filename:       /lib/modules/2.6.32-573.el6.x86_64/kernel/fs/ext4/ext4.ko
license:        GPL
description:    Fourth Extended Filesystem
author:         Remy Card, Stephen Tweedie, Andrew Morton, Andreas Dilger, Theodore Ts'o and others
srcversion:     CB1B990F5A758DFB0FB12F1
depends:        mbcache,jbd2
vermagic:       2.6.32-573.el6.x86_64 SMP mod_unload modversions 
```
**-F field： 仅显示指定字段的信息**
```
[root@promote ~]# modinfo -F license ext4
GPL
```
**-n：显示文件路径**
```
[root@promote ~]# modinfo -n ext4
/lib/modules/2.6.32-573.el6.x86_64/kernel/fs/ext4/ext4.ko
```
##### modprobe命令：
>Add and remove modules from the Linux Kernel

格式：modprobe  [-r]  module_name
>模块的动态装载：modprobe  module_name
>模块的动态卸载：modprobe  -r  module_name
##### depmod命令：
>Generate modules.dep and map files.
>内核模块依赖关系文件的生成工具；

##### 模块的装载和卸载的另一组命令：
>insmod命令：
>格式：insmod  [filename]  [module options...]
>filename：模块文件的文件路径；
>
>rmmod命令：
>格式：rmmod  [module_name]

#### ramdisk文件的管理：
##### mkinitrd命令
>为当前使用中的内核重新制作ramdisk文件

格式：mkinitrd [OPTION...] [<initrd-image>] <kernel-version>
>--with=<module>：除了默认的模块之外需要装载至initramfs中的模块；
>--preload=<module>：initramfs所提供的模块需要预先装载的模块；

示例： `[root@promote ~]# mkinitrd  /boot/initramfs-$(uname -r).img     $(uname -r)`

##### dracut命令
low-level tool for generating an initramfs image
格式：dracut [OPTION...] [<image> [<kernel version>]]
示例：` [root@promote ~]# dracut /boot/initramfs-$(uname -r).img  $(uname -r)`

#### 内核信息输出的伪文件系统
##### /proc：内核状态和统计信息的输出接口；同时，还提供一个配置接口，/proc/sys；
>
>**参数：**
>只读：信息输出；例如/proc/#/*
>可写：可接受用户指定一个“新值”来实现对内核某功能或特性的配置；/proc/sys/
>/proc/sys:
>net/ipv4/ip_forward  相当于  net.ipv4.ip_forward

###### sysctl命令
专用于查看或设定/proc/sys目录下参数的值
格式：sysctl [options]  [variable[=value]]
**查看：**
>sysctl  -a
>sysctl  variable        

**修改其值：**
>sysctl  -w  variable=value
```
[root@promote ~]# sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 0
[root@promote ~]# sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
```
```
[root@promote ~]# uname -n
promote.cache-dns.local
[root@promote ~]# sysctl -w kernel.hostname=leejarvan
kernel.hostname = leejarvan
[root@promote ~]# uname -n
leejarvan
```
###### 文件系统命令（cat, echo)
**查看：**
cat  /proc/sys/PATH/TO/SOME_KERNEL_FILE
```
[root@promote ~]# cat /proc/sys/kernel/hostname 
leejarvan
```
**设定：**
echo  "VALUE"  > /proc/sys/PATH/TO/SOME_KERNEL_FILE
```
[root@promote ~]# echo "centos6.7" > /proc/sys/kernel/hostname 
[root@promote ~]# uname -n
centos6.7
```
*注意：上述两种方式的设定仅当前运行内核有效*

###### 配置文件：/etc/sysctl.conf,  /etc/sysctl.d/*.conf（centos7）
>立即生效的方式：sysctl  -p  [/PATH/TO/CONFIG_FILE]
```
[root@promote ~]# vim /etc/sysctl.conf 
net.ipv4.ip_forward = 1
[root@promote ~]# cat /proc/sys/net/ipv4/ip_forward
0
[root@promote ~]# sysctl -p
net.ipv4.ip_forward = 1
[root@promote ~]# cat /proc/sys/net/ipv4/ip_forward
1
```
###### 内核参数：
>net.ipv4.ip_forward：核心转发；
>vm.drop_caches：手动回守内存；
>kernel.hostname：主机名；
>net.ipv4.icmp_echo_ignore_all：忽略所有ping操作；

##### /sys目录：
>sysfs：输出内核识别出的各硬件设备的相关属性信息，也有内核对硬件特性的可设置参数；对此些参数的修改，即可定制硬件设备工作特性；
>
>udev：通过读取/sys目录下的硬件设备信息按需为各硬件设备创建设备文件；udev是用户空间程序；
>专用工具：devadmin, hotplug；
>udev为设备创建设备文件时，会读取其事先定义好的规则文件，一般在/etc/udev/rules.d/目录下，以及/usr/lib/udev/rules.d/目录下；