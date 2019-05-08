**btrfs文件系统：CentOS7使用(技术预览版)**

> Btrfs (读法：B-tree, Butter FS, Better FS),，一种支持写入时复制（COW）的文件系统，运行在Linux作业系统，采用GPL授权。Oracle于2007年对外宣布这项计划，并释出原始码，在2014年8月释出稳定版。目标是取代Linux目前的ext3文件系统，改善ext3的限制，特别是单个文件的大小，总文件系统大小或文件检查和加入ext3未支持的功能。Btrfs也宣称专注在“容错、修复及易于管理”。

#### 核心特性：
- 多物理卷支持：btrfs可由多个底层物理卷组成；支持RAID，以联机“添加”、“移除”，“修改”
- 写时复制更新机制(CoW)：复制、更新及替换指针，而非“就地”更新（直接更新源文件）
- 数据及元数据校验码：checksum
- 子卷：sub_volume
- 快照：支持快照的快照
- 透明压缩

#### 文件系统创建：
`mkfs.btrfs`
**-L 'LABEL'**
```
[root@promote ~]# mkfs.btrfs -L mydata /dev/sdb /dev/sdc
btrfs-progs v4.9.1
See http://btrfs.wiki.kernel.org for more information.

Label:              mydata
UUID:               f0dd9cf7-d04f-46a0-bec6-2724271a27db
Node size:          16384
Sector size:        4096
Filesystem size:    40.00GiB
Block group profiles:
  Data:             RAID0             2.00GiB
  Metadata:         RAID1             1.00GiB
  System:           RAID1             8.00MiB
SSD detected:       no
Incompat features:  extref, skinny-metadata
Number of devices:  2
Devices:
   ID        SIZE  PATH
    1    20.00GiB  /dev/sdb
    2    20.00GiB  /dev/sdc
[root@promote ~]# blkid /dev/sdb
/dev/sdb: LABEL="mydata" UUID="f0dd9cf7-d04f-46a0-bec6-2724271a27db" UUID_SUB="df6af4ca-61c6-46fe-9a19-5e1270a40433" TYPE="btrfs" 
[root@promote ~]# blkid /dev/sdc
/dev/sdc: LABEL="mydata" UUID="f0dd9cf7-d04f-46a0-bec6-2724271a27db" UUID_SUB="9793055b-4f35-449a-8b38-032f762ccfbe" TYPE="btrfs" 

```
**-d <type>: raid0, raid1, raid5, raid6, raid10, single**
**-m <profile>: raid0, raid1, raid5, raid6, raid10, single, dup**
**-O <feature>**
**-O list-all: 列出支持的所有feature**
```
[root@centos7 ~]# mkfs.btrfs -O list-all
Filesystem features available:
mixed-bg            - mixed data and metadata block groups (0x4, compat=2.6.37, safe=2.6.37)
extref              - increased hardlink limit per file to 65536 (0x40, compat=3.7, safe=3.12, default=3.12)
raid56              - raid56 extended format (0x80, compat=3.9)
skinny-metadata     - reduced-size metadata extent refs (0x100, compat=3.10, safe=3.18, default=3.18)
no-holes            - no explicit hole extents for files (0x200, compat=3.14, safe=4.0)
```
##### 属性查看：
`btrfs filesystem show`
```
[root@promote ~]# btrfs filesystem show
Label: 'mydata'  uuid: f0dd9cf7-d04f-46a0-bec6-2724271a27db
	Total devices 2 FS bytes used 112.00KiB
	devid    1 size 20.00GiB used 2.01GiB path /dev/sdb
	devid    2 size 20.00GiB used 2.01GiB path /dev/sdc
[root@promote ~]# btrfs filesystem show /dev/sdb
Label: 'mydata'  uuid: f0dd9cf7-d04f-46a0-bec6-2724271a27db
	Total devices 2 FS bytes used 112.00KiB
	devid    1 size 20.00GiB used 2.01GiB path /dev/sdb
	devid    2 size 20.00GiB used 2.01GiB path /dev/sdc
[root@promote ~]# btrfs filesystem label /dev/sdb
mydata

```
##### 挂载文件系统：
`mount -t btrfs /dev/sdb MOUNT_POINT`
```
[root@promote ~]# mkdir /mydata
[root@promote ~]# mount -t btrfs /dev/sdb /mydata
[root@promote ~]# mount
/dev/sdb on /mydata type btrfs (rw,relatime,seclabel,space_cache,subvolid=5,subvol=/)
```
##### 透明压缩机制：
`mount -o compress={lzo|zlib} DEVICE MOUNT_POINT`
```
[root@promote ~]# mount -o compress=lzo /dev/sdb /mydata
[root@promote ~]# cp /etc/rc.d/init.d/functions  /mydata/
[root@promote ~]# cd /mydata/
[root@promote mydata]# ll -l
total 28
-rw-r--r--. 1 root root     0 Apr 14 09:58 a.txt
-rw-r--r--. 1 root root 18281 Apr 14 10:02 functions
-rw-r--r--. 1 root root  4335 Apr 14 09:58 grub2.cfg
```
#####  缩小或增加文件系统
`btrfs filesystem resize [+|-] SIZE MOUNT_POINT`
```
[root@promote ~]# btrfs filesystem  resize -10G /mydata
Resize '/mydata' of '-10G'
[root@promote ~]# btrfs filesystem show /mydata/
Label: 'mydata'  uuid: f0dd9cf7-d04f-46a0-bec6-2724271a27db
	Total devices 2 FS bytes used 916.00KiB
	devid    1 size 10.00GiB used 2.01GiB path /dev/sdb
	devid    2 size 20.00GiB used 2.01GiB path /dev/sdc
[root@promote ~]# df -lh
Filesystem                       Size  Used Avail Use% Mounted on
/dev/mapper/centos_promote-root   50G  1.1G   49G   3% /
devtmpfs                         898M     0  898M   0% /dev
tmpfs                            910M     0  910M   0% /dev/shm
tmpfs                            910M  9.6M  901M   2% /run
tmpfs                            910M     0  910M   0% /sys/fs/cgroup
/dev/sda1                       1014M  146M  869M  15% /boot
/dev/mapper/centos_promote-home   67G   33M   67G   1% /home
tmpfs                            182M     0  182M   0% /run/user/0
/dev/sdb                          30G   18M   18G   1% /mydata
```
##### 添加设备
`btrfs device add DEVICE MOUNT_POINT `
```
[root@promote ~]# btrfs device add /dev/sdd /mydata
[root@promote ~]# df -lh
Filesystem                       Size  Used Avail Use% Mounted on
/dev/mapper/centos_promote-root   50G  1.1G   49G   3% /
devtmpfs                         898M     0  898M   0% /dev
tmpfs                            910M     0  910M   0% /dev/shm
tmpfs                            910M  9.6M  901M   2% /run
tmpfs                            910M     0  910M   0% /sys/fs/cgroup
/dev/sda1                       1014M  146M  869M  15% /boot
/dev/mapper/centos_promote-home   67G   33M   67G   1% /home
tmpfs                            182M     0  182M   0% /run/user/0
/dev/sdb                          55G   18M   51G   1% /mydata
```
##### 均衡操作
把/mydata中其它的数据，均衡（移动到）到新添加的磁盘中（会占用io资源）
```
[root@promote ~]# btrfs balance start /mydata
```
注意：如果数据量过大的时候，这个时间可能就会很久
balance也有暂停（pause），继续（resume），取消（cancel）等操作，可以通过man btrfs-balance去查看下

##### 移除一个物理卷（btrfs会自动把数据先挪走再拆除）：
```
[root@promote ~]# btrfs device delete /dev/sdb /mydata
[root@promote ~]# df -lh
Filesystem                       Size  Used Avail Use% Mounted on
/dev/mapper/centos_promote-root   50G  1.1G   49G   3% /
devtmpfs                         898M     0  898M   0% /dev
tmpfs                            910M     0  910M   0% /dev/shm
tmpfs                            910M  9.6M  901M   2% /run
tmpfs                            910M     0  910M   0% /sys/fs/cgroup
/dev/sda1                       1014M  146M  869M  15% /boot
/dev/mapper/centos_promote-home   67G   33M   67G   1% /home
tmpfs                            182M     0  182M   0% /run/user/0
/dev/sdc                          40G   18M   38G   1% /mydata
[root@promote ~]# btrfs filesystem show /mydata/
Label: 'mydata'  uuid: f0dd9cf7-d04f-46a0-bec6-2724271a27db
	Total devices 2 FS bytes used 916.00KiB
	devid    2 size 20.00GiB used 2.03GiB path /dev/sdc
	devid    3 size 20.00GiB used 2.03GiB path /dev/sdd

```
##### 修改raid级别
**修改数据的组织机制：act on data chunks**
`btrfs balance -dconvert=raid0 /mydata`
**修改元数据的组织机制：act on metadata chunks**
`btrfs balance -mconvert=raid1 /mydata`
**修改系统的组织机制：act on system chunks**
`btrfs balance -sconvert=raid5 /mydata`

**btrfs的子命令：**
>filesystem
>device
>balance
>subvolume	 #子卷的管理命令

##### 创建一个子卷cache：
```
[root@promote ~]#  btrfs subvolume create /mydata/cache
Create subvolume '/mydata/cache'
[root@promote ~]# btrfs subvolume list /mydata
ID 261 gen 50 top level 5 path cache
```
**挂载子卷**
```
[root@promote ~]# umount /mydata
[root@promote ~]# mount -o subvol=cache /dev/sdb /mnt
[root@promote ~]# ls /mnt
messages
[root@promote ~]# btrfs subvolume show /mnt
/mnt
	Name: 			cache
	UUID: 			027abbea-474f-2c4c-b258-c931b99b47cc
	Parent UUID: 		-
	Received UUID: 		-
	Creation time: 		2019-04-14 10:31:10 +0800
	Subvolume ID: 		261
	Generation: 		51
	Gen at creation: 	49
	Parent ID: 		5
	Top level ID: 		5
	Flags: 			-
	Snapshot(s):
```
**也可以使用subid挂载**
`mount -o subvolid=268 /dev/sdb /mnt`
**查看volid：**
```
[root@promote ~]# btrfs subvolume list /mydata
ID 261 gen 50 top level 5 path cache
```
##### 删除子卷（注意：请先挂在父卷）：
```
[root@promote ~]# btrfs subvolume delete /mydata/cache
Delete subvolume (no-commit): '/mydata/cache'
```

##### 创建快照(快照卷必须与原卷在同一个卷组中)：
`trfs subvolume snapshot /mydata/logs /mydata/logs_snapshot`
##### 删除快照：
`btrfs subvolume dalete /mydata/logs_snapshot`
##### 对单个文件创建快照：
`cp --reflink file.txt file.txt_snapshot`
#### 把其它文件系统转换成btrfs
这里拿ext4做的测试，其它文件系统请一定要事前严格测试下：
**创建一个ext4的文件系统**
```
[root@promote ~]# mke2fs -t ext4 /dev/sdd1
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310720 blocks
65536 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done 
```
**强制检测下：**

```[root@promote ~]# umount /mnt
[root@promote ~]# fsck -f /dev/sdd1
fsck from util-linux 2.23.2
e2fsck 1.42.9 (28-Dec-2013)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/sdd1: 12/327680 files (0.0% non-contiguous), 58463/1310720 blocks
```
**转换(数据没丢):**
```
[root@promote ~]# btrfs-convert /dev/sdd1
create btrfs filesystem:
	blocksize: 4096
	nodesize:  16384
	features:  extref, skinny-metadata (default)
creating ext2 image file
creating btrfs metadatacopy inodes [o] [         0/        12]
conversion complete[root@promote ~]# 
```
**查看有没有生效：**
```
[root@promote ~]# btrfs filesystem show
warning, device 3 is missing
Label: 'mydata'  uuid: f0dd9cf7-d04f-46a0-bec6-2724271a27db
	Total devices 3 FS bytes used 932.00KiB
	devid    2 size 20.00GiB used 2.03GiB path /dev/sdc
	devid    4 size 20.00GiB used 0.00B path /dev/sdb
	*** Some devices missing

Label: none  uuid: 3a407f45-6860-49ed-81bd-8cc1d09238d9
	Total devices 1 FS bytes used 228.76MiB
	devid    1 size 5.00GiB used 485.78MiB path /dev/sdd1
```
**回滚到之前的文件系统（之前是ext4）：**
```
[root@promote ~]# umount /mnt
[root@promote ~]# btrfs-convert -r /dev/sdd1
rollback complete
```
**查看：**
```
[root@promote ~]# blkid /dev/sdd1
/dev/sdd1: UUID="555c8c51-c040-443a-98c1-e2010a2158ac" TYPE="ext4" 
```