计算机组由CPU， Memory(RAM), I/O设备组成
>I/O: Disks, Ehtercard（以太网卡）
>Disks作用： 持久存储数据

#### 硬盘接口类型：

- IDE(ata)：并口，133MB/s
- SCSI：并口，Ultrascsi320（流行版本）, 320MB/S, UltraSCSI640（流行版本）, 640MB/S
- SATA：串口，6gbps，IDE的升级版
- SAS：串口，6gbps，SCSI升级版
- USB3.0：串口，480MB/s

**并口：同一线缆可以接多块设备**

**IDE：接两个设备，主设备，从设备**

**SCSI:**
>宽带：16-1=15
>窄带：8-1=7
>串口：同一线缆只可以接一个设备

**指标：iops：io per second**
PCI-E接口的固态硬盘可以达到数十万次iops能力（接近内存的速度）

#### 硬盘：机械硬盘（技术没有进步），固态硬盘

##### 机械硬盘：
>track：磁道
>sector：扇区，512bytes
>cylinder：柱面
>(分区划分基于柱面)

##### 平均寻道时间：
5400rpm, 7200rpm, 10000rpm, 15000rpm

##### 设备类型：
- 块(block)：随机访问，数据交换单位是“块”
- 字符(character)：线性访问，数据交换单位是“字符”

##### 设备文件：
FHS存放在/dev
设备文件用来关联至设备的驱动程序，识别设备的访问入口；

##### 设备号：
- major：主设备号，区分设备类型；用于标明设备所需要的驱动程序；
- minor：次设备号，区分同种类型下的不同的设备；是特定设备的访问入口；

#### 手动创建设备文件                       

##### mknod命令：make block or character special files

格式：mknod  [OPTION]...  NAME  TYPE  [MAJOR  MINOR]

    [root@centos7 ~]# mknod /dev/testdev c 111 1
    [root@centos7 ~]# ll /dev/testdev
    crw-r--r--. 1 root root 111, 1 Apr  8 20:14 /dev/testdev


**-m MODE：创建后的设备文件的访问权限**



##### 设备文件名：ICANN定义(互联网名称地址分配机构)

**磁盘：**
>IDE： /dev/hd[a-z]
>例如：/dev/hda, /dev/hdb
>SCSI, SATA, USB, SAS: /dev/sd[a-z]

**分区：**
 >/dev/sda#：
 >/dev/sda1, ...

*注意：CentOS 6和7统统将硬盘设备文件标识为/dev/sd[a-z]#*             

##### 引用设备的方式：
- 设备文件名
- 卷标
- UUID

##### 磁盘分区：MBR， GPT

**MBR(Master Boot Record)：0 sector**

512bytes分为三部分：

> 446bytes：bootloader, 程序，引导启动操作系统的程序；
>
> 64bytes：分区表，每16bytes标识一个分区，一共只能有4个分区；
> 最多4主分区
> 也可以划分成3主1扩展：
> 扩展中可以划分n逻辑分区
>
> 2bytes：MBR区域的有效性标识；55AA为有效；

    主分区和扩展分区的标识：1-4
    逻辑分区标识：5+



#### GPT（GUID Partition Table）
全局唯一标识分区表，是一个较新的分区机制，解决了MBR很多缺点。
支持超过2T的磁盘（64位寻址空间）。fdisk最大只能建立2TB大小的分区，创建一个大于2TB的分区使用parted。向后兼容MBR。必须在支持UEFI的硬件上才能使用（Intel提出，用于取代BIOS）。


在Linux系统中使用GPT分区格式(以CentOS 7为例)：
parted用法和常用选项：
parted [选项]... [设备 [命令 [参数]...]...] 
 将带有“参数”的命令应用于“设备”。如果没有给出“命令”，则以交互模式运行.  

**帮助选项：**
>-h, --help 显示此求助信息 
>-l, --list 列出所有设别的分区信息
>-i, --interactive 在必要时，提示用户 
>-s, --script从不提示用户 
>-v, --version显示版本

**操作命令：**
```
检查 MINOR #对文件系统进行一个简单的检查 
cp [FROM-DEVICE] FROM-MINOR TO-MINOR #将文件系统复制到另一个分区 
help [COMMAND]  #打印通用求助信息，或关于 COMMAND 的信息 
mklabel 标签类型 #创建新的磁盘标签 (分区表) 
mkfs MINOR 文件系统类型 #在 MINOR 创建类型为“文件系统类型”的文件系统 
mkpart 分区类型 [文件系统类型] 起始点 终止点    #创建一个分区 
mkpartfs 分区类型 文件系统类型 起始点 终止点  #创建一个带有文件系统的分区 
move MINOR 起始点 终止点    #移动编号为 MINOR 的分区 
name MINOR 名称     #将编号为 MINOR 的分区命名为“名称” 
print [MINOR]     #打印分区表，或者分区 
quit          #退出程序 
rescue 起始点 终止点    #挽救临近“起始点”、“终止点”的遗失的分区 
resize MINOR 起始点 终止点  #改变位于编号为 MINOR 的分区中文件系统的大小 
rm MINOR         #删除编号为 MINOR 的分区 
select 设备       #选择要编辑的设备 
set MINOR 标志 状态     #改变编号为 MINOR 的分区的标志
```
### 选择 GPT 还是 MBR

GUID Partition Table（GPT）是一种更灵活的分区方式。它正在逐步取代Master Boot Record（MBR）系统。GPT相对于诞生于MS-DOS时代的MBR而言，有许多优点。新版的*fdisk*（MBR）和*gdisk*（GPT）使得使用GPT或者MBR在可靠性和性能最大化上都非常容易。

在做出选择前，需要考虑如下内容：
*   如果使用 GRUB legacy 作为bootloader，必须使用MBR。
*   如果使用传统的BIOS，并且双启动中包含 Windows （无论是32位版还是64位版），必须使用MBR。
*   如果使用 [UEFI](https://wiki.archlinux.org/index.php/UEFI "UEFI") 而不是BIOS，并且双启动中包含 Windows 64位版，必须使用GPT。
*   非常老的机器需要使用 MBR，因为 BIOS 可能不支持 GPT.
*   如果不属于上述任何一种情况，可以随意选择使用 GPT 还是 MBR。由于 GPT 更先进，建议选择 GPT。
*   建议在使用 [UEFI](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface "Unified Extensible Firmware Interface") 的情况下选择 GPT，因为有些 UEFI firmware 不支持从 MBR 启动。
####分区管理命令：fdisk命令

1、查看磁盘的分区信息：
**fdisk -l [-u] [device...]：列出指定磁盘设备上的分区情况**

    [root@centos7 ~]# fdisk -l
    
    Disk /dev/sda: 214.7 GB, 214748364800 bytes, 419430400 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x000c31d0
    
       Device Boot      Start         End      Blocks   Id  System
    /dev/sda1   *        2048     2099199     1048576   83  Linux
    /dev/sda2         2099200   106956799    52428800   83  Linux
    /dev/sda3       106956800   169871359    31457280   83  Linux
    /dev/sda4       169871360   419430399   124779520    5  Extended
    /dev/sda5       169873408   178262015     4194304   82  Linux swap / Solaris

2、管理分区
**fdisk  device**

    [root@centos7 ~]# fdisk /dev/sda1
    Welcome to fdisk (util-linux 2.23.2).
    
    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.
    
    Device does not contain a recognized partition table
    Building a new DOS disklabel with disk identifier 0x44eace13.
    
    Command (m for help): 

fdisk提供了一个交互式接口来管理分区，它有许多子命令，分别用于不同的管理功能；所有的操作均在内存中完成，没有直接同步到磁盘；

直到使用w命令保存至磁盘上；

**常用命令：**
>n：创建新分区
>d：删除已有分区
>t：修改分区类型
>l：查看所有已经ID
>w：保存并退出
>q：不保存并退出
>m：查看帮助信息
>p：显示现有分区信息

注意：在已经分区并且已经挂载其中某个分区的磁盘设备上创建的新分区，内核可能在创建完成后无法直接识别

**查看：cat  /proc/partitions**

    [root@centos7 ~]# cat /proc/partitions 
    major minor  #blocks  name
    
      11        0   10491904 sr0
       8        0  209715200 sda
       8        1    1048576 sda1
       8        2   52428800 sda2
       8        3   31457280 sda3
       8        4          1 sda4
       8        5    4194304 sda5


##### 通知内核强制重读磁盘分区表

CentOS 5：partprobe [device]

CentOS 6,7：partx, kpartx

    partx -a [device]
    
    kpartx -af [device]

#### 分区创建工具：parted, sfdisk；

##### 创建文件系统：
>格式化：低级格式化（分区之前进行，划分磁道）、高级格式化（分区之后对分区进行，创建文件系统）

**磁盘分区划分为元数据区，数据区**

>元数据区：
>文件元数据：inode (index node)
>大小、权限、属主属组、时间戳、数据块指针
>
>符号链接文件：存储数据指针的空间当中存储的是真实文件的访问路径；
>设备文件：存储数据指针的空间当中存储的是设备号（major, minor）；

**bitmap index：位图索引**

#### VFS: Virtual File System
- Linux的文件系统: ext2(无日志功能), ext3, ext4, xfs, reiserfs, btrfs（功能非常强大，生产环境不稳定）

- 光盘：iso9660

- 网络文件系统：nfs, cifs

- 集群文件系统：gfs2, ocfs2

- 内核级分布式文件系统：ceph

- windows的文件系统：vfat, ntfs

- 伪文件系统：proc, sysfs, tmpfs, hugepagefs

- Unix的文件系统：UFS， FFS， JFS

- 交换文件系统：swap

- 用户空间的分布式文件系统：mogilefs, moosefs, glusterfs

#### 文件系统管理工具：

##### 创建文件系统的工具：mkfs
>mkfs.ext2, mkfs.ext3, mkfs.ext4, mkfs.xfs, mkfs.vfat, ...

##### 检测及修复文件系统的工具：fsck
>sck.ext2, fsck.ext3, ...

##### 查看其属性的工具
>dumpe2fs, tune2fs

##### 调整文件系统特性：
>tune2fs


#### 链接文件：访问同一个文件不同路径
##### 硬链接：指向同一个inode的多个文件路径

**特性：**
- 目录不支持硬链接
- 硬链接不能跨文件系统
- 创建硬链接会增加inode引用计数

**创建：**
>ln  src  link_file

    [root@centos7 ~]# ls -l
    -rw-r--r--. 1 root root  595 Apr  3 17:38 fstab
    [root@centos7 ~]# ln fstab fstab.2
    [root@centos7 ~]# ls -l
    -rw-r--r--. 2 root root  595 Apr  3 17:38 fstab    源文件
    -rw-r--r--. 2 root root  595 Apr  3 17:38 fstab.2   链接文件



##### 符号链接：指向一个文件路径的另一个文件路径
**特性：**
- 符号链接与文件是两人个各自独立的文件，各有自己的inode；对原文件创建符号链接不会增加引用计数
- 支持对目录创建符号链接，可以跨文件系统
- 删除符号链接文件不影响原文件；但删除原文件，符号指定的路径即不存在，此时会变成无效链接

注意：符号链接文件的大小是其指定的文件的路径字符串的字节数

**创建：**
>ln -s  src link_file

    [root@centos7 ~]# ln -s fstab fstab.slink
    lrwxrwxrwx. 1 root root    5 Apr  9 18:38 fstab.slink -> fstab

-v：verbose（显示过程）

    [root@centos7 ~]# ln -sv fstab fstab.slink
    ‘fstab.slink’ -> ‘fstab’

#### 内核级文件系统的组成部分：

>文件系统驱动：由内核提供
>
>文件系统箮理工具：由用户空间的应用程序提供

####　ext系列文件系统的管理工具：
>mkfs.ext2, mkfs.ext3, mkfs.ext4
>
>mkfs -t ext2 = mkfs.ext2

    [root@centos7 ~]# mkfs.ext2 /dev/sda1

####　ext系列文件系统专用管理工具：mke2fs
格式：`mke2fs [OPTIONS]  device`

**-t {ext2|ext3|ext4}：指明要创建的文件系统类型**    

    mkfs.ext4 = mkfs -t ext4 = mke2fs -t ext4

**-b {1024|2048|4096}：指明文件系统的块大小**
**-L LABEL：指明卷标**
**-j：创建有日志功能的文件系统ext3**

    mke2fs -j = mke2fs -t ext3 = mkfs -t ext3 = mkfs.ext3
**-i #：bytes-per-inode，指明inode与字节的比率；即每多少字节创建一个Indode**
**-N #：直接指明要给此文件系统创建的inode的数量**
**-m #：指定预留的空间，百分比**
**-O [^]FEATURE：以指定的特性创建目标文件系统**
注意：不加\^表示表示启用此特性，加^表示关闭磁特性

#### e2label命令：卷标的查看与设定
**查看：e2label device**
**设定：e2label device LABEL**

    [root@centos6 ~]# e2label /dev/sda3 madata
    [root@centos6 ~]# e2label /dev/sda3
    madata

#### tune2fs命令：查看或修改ext系列文件系统的某些属性
adjust tunable filesystem parameters on ext2/ext3/ext4 filesystems；
注意：块大小创建后不可修改

格式：`tune2fs [OPTIONS] device`

    [root@centos6 ~]# tune2fs /dev/sda1
    tune2fs 1.41.12 (17-May-2010)
    Usage: tune2fs [-c max_mounts_count] [-e errors_behavior] [-g group]
    [-i interval[d|m|w]] [-j] [-J journal_options] [-l]
    [-m reserved_blocks_percent] [-o [^]mount_options[,...]] 
    [-r reserved_blocks_count] [-u user] [-C mount_count] [-L volume_label]
    [-M last_mounted_dir] [-O [^]feature[,...]]
    [-E extended-option[,...]] [-T last_check_time] [-U UUID]
    [ -I new_inode_size ] device

**-l：查看超级块的内容**

    [root@centos6 ~]# tune2fs -l /dev/sda1
    Filesystem volume name:   <none>      
    Last mounted on:          /boot
    Filesystem UUID:          23e3ddf8-14c5-4e17-8dda-ec10c312666d
    Filesystem magic number:  0xEF53
    Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery 
    extent flex_bg sparse_super large_file
    Default mount options:    user_xattr acl
    Filesystem state:         clean
    Errors behavior:          Continue
    Filesystem OS type:       Linux
    Inode count:              65536
    Block count:              262144
    Reserved block count:     13107
    Free blocks:              241295
    Free inodes:              65498
    First block:              0
    Block size:               4096
    Fragment size:            4096
    Reserved GDT blocks:      63
    Blocks per group:         32768
    Fragments per group:      32768
    Inodes per group:         8192
    Inode blocks per group:   512

##### 修改指定文件系统的属性

**-j：ext2 --> ext3**
**-L LABEL：修改卷标**
**-m #：调整预留空间百分比**
**-O [\^]FEATHER：开启或关闭某种特性（开启去掉^）**

    [root@centos6 ~]# tune2fs -O ^has_journal /dev/sda3
    tune2fs 1.41.12 (17-May-2010)

**-o [^]mount_options：开启或关闭某种默认挂载选项**

    [root@centos6 ~]# tune2fs -o acl /dev/sda3
    tune2fs 1.41.12 (17-May-2010)
    [root@centos6 ~]# tune2fs -l /dev/sda3
    Default mount options:    user_xattr acl
    [root@centos6 ~]# tune2fs -o ^acl /dev/sda3
    tune2fs 1.41.12 (17-May-2010)
    [root@centos6 ~]# tune2fs -l /dev/sda3
    Default mount options:    user_xattr 
#### dumpe2fs命令：显示ext系列文件系统的属性信息
格式：`dumpe2fs  [-h] device`

#### 用于实现文件系统检测的工具

因进程意外中止或系统崩溃等 原因导致定稿操作非正常终止时，可能会造成文件损坏；此时，应该检测并修复文件系统； 建议，离线进行；

##### ext系列文件系统的专用工具：
**e2fsck : check a Linux ext2/ext3/ext4 file system**
格式：e2fsck [OPTIONS]  device
**-y：对所有问题自动回答为yes**
**-f：即使文件系统处于clean状态，也要强制进行检测**

**fsck：check and repair a Linux file system**
**-t fstype：指明文件系统类型**
    
    fsck -t ext4 = fsck.ext4
**-a：无须交互而自动修复所有错误**
**-r：交互式修复**   

##### CentOS 6如何使用xfs文件系统：
>\#yum  -y  install  xfsprogs 


**创建：mkfs.xfs**
**检测：fsck.xfs**    

#### blkid命令
>blkid device
>blkid  -L LABEL：根据LABEL定位设备
>blkid  -U  UUID：根据UUID定位设备

    [root@centos6 ~]# blkid /dev/sda3
    /dev/sda3: LABEL="madata" UUID="6a9f1975-bf53-44f6-bf1a-62e68dd4b1c3" TYPE="ext4" 
    [root@centos6 ~]# e2label /dev/sda3 MYDATA
    [root@centos6 ~]# blkid -L MYDATA
    /dev/sda3
    [root@centos6 ~]# blkid -U 6a9f1975-bf53-44f6-bf1a-62e68dd4b1c3
    /dev/sda3

#### swap文件系统：
>Linux上的交换分区必须使用独立的文件系统；
>且文件系统的System ID必须为82；

##### 创建swap设备：mkswap命令
格式：`mkswap [OPTIONS]  device`
**-L LABEL：指明卷标**
**-f：强制**

Windows无法识别Linux的文件系统； 因此，存储设备需要两种系统之间交叉使用时，应该使用windows和Linux同时支持的文件系统：fat32(vfat);

    # mkfs.vfat device

#### 文件系统的使用
>首先要“挂载”：mount命令和umount命令
>
>根文件系统这外的其它文件系统要想能够被访问，都必须通过“关联”至根文件系统上的某个目录来实现，此关联操作即为“挂载”；此目录即为“挂载点”；
>
>**挂载点：mount_point，用于作为另一个文件系统的访问入口**
- 事先存在
- 应该使用未被或不会被其它进程使用到的目录
- 挂载点下原有的文件将会被隐藏

##### mount命令
格式：`mount  [-nrw]  [-t vfstype]  [-o options]  device  dir`

    [root@centos6 ~]# mount
    /dev/sda2 on / type ext4 (rw)
    proc on /proc type proc (rw)
    sysfs on /sys type sysfs (rw)
    devpts on /dev/pts type devpts (rw,gid=5,mode=620)
    tmpfs on /dev/shm type tmpfs (rw,rootcontext="system_u:object_r:tmpfs_t:s0")
    /dev/sda1 on /boot type ext4 (rw)
    /dev/sda3 on /data type ext4 (rw)
    none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
    gvfs-fuse-daemon on /root/.gvfs type fuse.gvfs-fuse-daemon (rw,nosuid,nodev)
    /dev/sr0 on /media/CentOS_6.10_Final type iso9660 
    (ro,nosuid,nodev,uhelper=udisks,uid=0,gid=0,iocharset=utf8,mode=0400,dmode=0500)
    
    [root@centos6 ~]# mount /dev/sda3 /mnt
    [root@centos6 ~]# mount
    /dev/sda3 on /mnt type ext4 (rw)

**-r：readonly，只读挂载；  光盘设备只能只读挂载**
**-w：read and write, 读写挂载**
**-n：默认情况下，设备挂载或卸载的操作会同步更新至/etc/mtab文件中；-n用于禁止此特性**
**-t vfstype：指明要挂载的设备上的文件系统的类型；多数情况下可省略，此时mount会通过blkid来判断要挂载的设备的文件系统类型**
**-L LABEL：挂载时以卷标的方式指明设备**
**-U UUID：挂载时以UUID的方式指明设备**
**-o options：挂载选项**
>sync/async：同步/异步操作；
>atime/noatime：文件或目录在被访问时是否更新其访问时间戳；
>diratime/nodiratime：目录在被访问时是否更新其访问时间戳；
>remount：重新挂载；
>acl：支持使用facl功能；

    # mount -o acl  device dir
    # tune2fs  -o  acl  device
>ro：只读
>rw：读写
>dev/nodev：此设备上是否允许创建设备文件；
>exec/noexec：是否允许运行此设备上的程序文件；
>auto/noauto：允许自动被挂载；
>user/nouser：是否允许普通用户挂载此文件系统；
>suid/nosuid：是否允许程序文件上的suid和sgid特殊权限生效；                    

>defaults：Use default options: rw, suid, dev, exec, auto, nouser, async, and relatime.

**一个使用技巧：**
可以实现将目录绑定至另一个目录上，作为其临时访问入口；
mount --bind  源目录  目标目录
```
[root@centos6 ~]# mount --bind /etc /mnt
[root@centos6 ~]# ls /mnt
```
**查看当前系统所有已挂载的设备：**
>\# mount
>\# cat  /etc/mtab
>\# cat  /proc/mounts

#### 文件系统挂载使用：
##### 挂载光盘设备：
光盘设备文件：
>IDE: /dev/hdc
>SATA: /dev/sr0

符号链接文件：
>/dev/cdrom
>/dev/cdrw
> /dev/dvd
>/dev/dvdrw

**mount -r /dev/cdrom /media/cdrom
umount /dev/cdrom**


​      
##### 挂载U盘：事先识别U盘的设备文件；

##### 挂载本地的回环设备：

**\# mount  -o  loop  /PATH/TO/SOME_LOOP_FILE   MOUNT_POINT**

##### 两个特殊设备：
>/dev/null: 数据黑洞
>/dev/zero：吐零机

#### umount命令：umount  device|dir

注意：正在被进程访问到的挂载点无法被卸载；

**查看被哪个或哪些进程所占用：**
>\# lsof  MOUNT_POINT
>\# fuser -v  MOUNT_POINT

**终止所有正在访问某挂载点的进程：**

>\# fuser  -km  MOUNT_POINT

#### 交换分区的启用和禁用：

##### 创建交换分区的命令：mkswap

**启用：swapon**

`swapon  [OPTION]  [DEVICE]`

-a：定义在/etc/fstab文件中的所有swap设备；

**禁用：swapoff**

swapoff DEVICE

#### 开机自动挂载etc/fstab
**设定除根文件系统以外的其它文件系统能够开机时自动挂载：/etc/fstab文件**
每行定义一个要挂载的文件系统及相关属性：
6个字段：
- 要挂载的设备：
  设备文件；
  LABEL
  UUID
  伪文件系统：如sysfs, proc, tmpfs等

- 挂载点
  swap类型的设备的挂载点为swap；

- 文件系统类型；

- 挂载选项
  defaults：使用默认挂载选项；
  如果要同时指明多个挂载选项，彼此间以逗号分隔；
  defaults,acl,noatime,noexec

- 转储频率
  0：从不备份
  1：每天备份
  2：每隔一天备份

- 自检次序
  0：不自检
  1：首先自检，通常只能是根文件系统可用1
  2：次级自检


**mount  -a：可自动挂载定义在此文件中的所有支持自动挂载的设备；**

#### df命令
显示目前在Linux系统上的文件系统的磁盘使用情况统计
格式：`df [OPTION]... [FILE]...`

```
[root@centos7 ~]# df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/sda2       52403200  4333832  48069368   9% /
devtmpfs          915908        0    915908   0% /dev
tmpfs             931624        0    931624   0% /dev/shm
tmpfs             931624    10720    920904   2% /run
tmpfs             931624        0    931624   0% /sys/fs/cgroup
/dev/sda3       31441920    32992  31408928   1% /data
/dev/sda1        1038336   157828    880508  16% /boot
tmpfs             186328        8    186320   1% /run/user/42
tmpfs             186328       36    186292   1% /run/user/0
/dev/sr0        10491772 10491772         0 100% /run/media/root/CentOS 7 x86_64
```
**-l：仅显示本地文件的相关信息**
**-h：human-readable  单位换算**
**-i：显示inode的使用状态而非blocks**
```
[root@centos7 ~]# df -i
Filesystem       Inodes  IUsed    IFree IUse% Mounted on
/dev/sda2      26214400 135331 26079069    1% /
devtmpfs         228977    398   228579    1% /dev
tmpfs            232906      1   232905    1% /dev/shm
tmpfs            232906    970   231936    1% /run
tmpfs            232906     16   232890    1% /sys/fs/cgroup
/dev/sda3      15728640      3 15728637    1% /data
/dev/sda1        524288    341   523947    1% /boot
tmpfs            232906      7   232899    1% /run/user/42
tmpfs            232906     20   232886    1% /run/user/0
/dev/sr0              0      0        0     - /run/media/root/CentOS 7 x86_64
```
#### du命令
显示总文件大小（ls只显示目录大小）
格式：`du [OPTION]... [FILE]...`
**-s: sumary**
**-h: human-readable**

```
[root@centos7 ~]# ls -ldh /etc
drwxr-xr-x. 144 root root 8.0K Apr 13 13:42 /etc
[root@centos7 ~]# du -hs /etc
41M	/etc
```
#### dd命令：convert and copy a file
用法：dd if=/PATH/FROM/SRC of=/PATH/TO/DEST
```
[root@promote ~]# dd if=/etc/fstab of=/tmp/fstab
1+1 records in
1+1 records out
565 bytes (565 B) copied, 0.000536591 s, 1.1 MB/s
```
**bs=#：block size, 复制单元大小**
**count=#：复制多少个bs**

>磁盘拷贝：
>dd if=/dev/sda of=/dev/sdb
>备份MBR
>dd if=/dev/sda of=/tmp/mbr.bak bs=512 count=1
>破坏MBR中的bootloader：
>dd if=/dev/zero of=/dev/sda bs=256 count=1

**练习：**
1、创建一个10G的分区，并格式化为ext4文件系统；
(1) block大小为2048；预留空间为2%，卷标为MYDATA；
(2) 挂载至/mydata目录，要求挂载时禁止程序自动运行，且不更新文件的访问时间戳；
(3) 可开机自动挂载；

    [root@promote ~]# fdisk /dev/sda
    Command (m for help): n
    Partition type:
       p   primary (0 primary, 0 extended, 4 free)
       e   extended
    Select (default p): p
    Partition number (1-4, default 1): 1
    First sector (2048-251658239, default 2048): 
    Using default value 2048
    Last sector, +sectors or +size{K,M,G} (2048-251658239, default 251658239): +10G
    Partition 1 of type Linux and of size 10 GiB is set
    Command (m for help): p
     Device Boot      Start         End      Blocks   Id  System
    /dev/sda1            2048    20973567    10485760   83  Linux
    [root@promote ~]# partx -a /dev/sda
    partx: /dev/sda: error adding partitions 1-2  
    [root@promote ~]# mke2fs -t ext4 -L MYDATA -b 2048 -m 2 /dev/sda1
    [root@promote ~]# blkid /dev/sda1
    /dev/sda1: LABEL="MYDATA" UUID="85dd2a86-5339-4917-9002-8f11d188e3dc" TYPE="ext4" 
    [root@promote ~]# mount -o noatime,noexec /dev/sda1 /mydata
    [root@promote ~]# vim /etc/fstab 
    # /etc/fstab
    # Created by anaconda on Sun Mar 24 14:30:18 2019
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    UUID=385f075d-1421-48a1-911b-d6ec2f9c2016 /                       xfs     defaults        0 0
    UUID=3710be3c-5419-4d96-9795-e5f6863b59b3 /boot                   xfs     defaults        0 0
    UUID=3f0703b3-e4e1-4f8d-84c4-dfb4c6ec4dc6 /data                   xfs     defaults        0 0
    UUID=8c696c94-b458-44c3-a218-a9c09512e4bd swap                    swap    defaults        0 0
    /dev/sda1                                 /MYDATA                 ext4    defaults        0 0
    [root@promote ~]# mount -a
2、创建一个大小为2G的swap分区，并启动之；
```
[root@centos7 ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1819         681         229          13         908         917
Swap:             0           0           0
[root@centos7 ~]# mkswap /dev/sda5
mkswap: /dev/sda5: warning: wiping old swap signature.
Setting up swapspace version 1, size = 4194300 KiB
no label, UUID=6ab6d221-499d-4300-9394-96506bfabb57
[root@centos7 ~]# swapon /dev/sda5
[root@centos7 ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1819         684         226          13         908         915
Swap:          4095           0        4095
```