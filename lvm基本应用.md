**LVM2:
LVM: Logical Volume Manager， Version: 2**

**dm: device mapper（设备映射），将一个或多个底层块设备组织成一个逻辑设备的模块；**
`/dev/dm-#`
![](https://upload-images.jianshu.io/upload_images/16547068-0eb26ef993a69982.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>/dev/mapper/VG_NAME-LV_NAME
>如：/dev/mapper/vol0-root
>符号链接：/dev/VG_NAME/LV_NAME
>如：/dev/vol0/root

    [root@promote ~]# cd /dev/mapper
    [root@promote mapper]# ll
    total 0
    lrwxrwxrwx. 1 root root       7 Apr 11 21:37 centos-home -> ../dm-2
    lrwxrwxrwx. 1 root root       7 Apr 11 21:37 centos-root -> ../dm-0
    lrwxrwxrwx. 1 root root       7 Apr 11 21:37 centos-swap -> ../dm-1
    crw-------. 1 root root 10, 236 Apr 11 21:37 control

#### pv（物理卷）管理工具：
**pvs：简要pv信息显示**

    [root@promote ~]# pvs
      PV         VG             Fmt  Attr PSize    PFree
      /dev/sda2  centos_promote lvm2 a--  <199.00g 4.00m

**pvdisplay：显示pv的详细信息**

    [root@promote ~]# pvdisplay
      --- Physical volume ---
      PV Name               /dev/sda2
      VG Name               centos_promote
      PV Size               <199.00 GiB / not usable 3.00 MiB
      Allocatable           yes 
      PE Size               4.00 MiB
      Total PE              50943
      Free PE               1
      Allocated PE          50942
      PV UUID               M6rkVy-FZMk-girX-Vul7-FEcN-sJS6-xDwcvU

**pvcreate /dev/DEVICE: 创建pv**

#### vg（卷组）管理工具：
**vgs：简要显示vg信息**

    [root@promote ~]# vgs
      VG             #PV #LV #SN Attr   VSize    VFree
      centos_promote   1   3   0 wz--n- <199.00g 4.00m

**vgdisplay：显示vg的详细信息**

    [root@promote ~]# vgdisplay
      --- Volume group ---
      VG Name               centos_promote
      System ID             
      Format                lvm2
      Metadata Areas        1
      Metadata Sequence No  4
      VG Access             read/write
      VG Status             resizable
      MAX LV                0
      Cur LV                3
      Open LV               3
      Max PV                0
      Cur PV                1
      Act PV                1
      VG Size               <199.00 GiB
      PE Size               4.00 MiB
      Total PE              50943
      Alloc PE / Size       50942 / 198.99 GiB
      Free  PE / Size       1 / 4.00 MiB
      VG UUID               fH4Oc1-iVlL-HJqs-HtcU-2XNA-wSch-V7laxn

`vgcreate  [-s #[kKmMgGtTpPeE]] VolumeGroupName  PhysicalDevicePath [PhysicalDevicePath...]`
`vgextend  VolumeGroupName  PhysicalDevicePath [PhysicalDevicePath...]`
`vgreduce  VolumeGroupName  PhysicalDevicePath [PhysicalDevicePath...]`

先做pvmove vgremove

#### lv（逻辑卷）管理工具：
**lvs：简要显示lv信息**

    [root@promote ~]# lvs
      LV   VG             Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
      home centos_promote -wi-ao---- 146.99g                                                    
      root centos_promote -wi-ao----  50.00g                                                    
      swap centos_promote -wi-ao----   2.00g       
**lvdisplay：显示lv的详细信息**

    [root@promote ~]# lvdisplay
     --- Logical volume ---
      LV Path                /dev/centos_promote/swap
      LV Name                swap
      VG Name                centos_promote
      LV UUID                Xcy2JA-IZ6C-0jC2-VkBK-TXLL-PEOv-bSnTNc
      LV Write Access        read/write
      LV Creation host, time promote.cache-dns.local, 2019-04-11 21:52:32 +0800
      LV Status              available
      # open                 2
      LV Size                2.00 GiB
      Current LE             512
      Segments               1
      Allocation             inherit
      Read ahead sectors     auto
      - currently set to     8192
      Block device           253:1
       
      --- Logical volume ---
      LV Path                /dev/centos_promote/home
      LV Name                home
      VG Name                centos_promote
      LV UUID                tpMG5K-GlWc-o8qX-gtoa-46xv-eQSs-Hktet3
      LV Write Access        read/write
      LV Creation host, time promote.cache-dns.local, 2019-04-11 21:52:32 +0800
      LV Status              available
      # open                 1
      LV Size                146.99 GiB
      Current LE             37630
      Segments               1
      Allocation             inherit
      Read ahead sectors     auto
      - currently set to     8192
      Block device           253:2
       
      --- Logical volume ---
      LV Path                /dev/centos_promote/root
      LV Name                root
      VG Name                centos_promote
      LV UUID                jMtc29-aRsN-bXIi-SZyG-aEdJ-3WTk-yX7QAN
      LV Write Access        read/write
      LV Creation host, time promote.cache-dns.local, 2019-04-11 21:52:32 +0800
      LV Status              available
      # open                 1
      LV Size                50.00 GiB
      Current LE             12800
      Segments               1
      Allocation             inherit
      Read ahead sectors     auto
      - currently set to     8192
      Block device           253:0

`lvcreate -L #[mMgGtT] -n NAME VolumeGroup`
`lvremove /dev/VG_NAME/LV_NAME`

##### 扩展逻辑卷：
>\# lvextend -L [+]#[mMgGtT] /dev/VG_NAME/LV_NAME 扩展物理边界
>\# resize2fs /dev/VG_NAME/LV_NAME  扩展逻辑边界

##### 缩减逻辑卷：
>\# umount /dev/VG_NAME/LV_NAME
>\# e2fsck -f /dev/VG_NAME/LV_NAME
>\# resize2fs /dev/VG_NAME/LV_NAME #[mMgGtT]
>\# lvreduce -L [-]#[mMgGtT] /dev/VG_NAME/LV_NAME
>\# mount

##### 快照：snapshot
>lvcreate -L #[mMgGtT] -p r -s -n snapshot_lv_name original_lv_name
```
[root@localhost ~]# mount /dev/myvg/mylv /mydata/
[root@localhost ~]# lvcreate -L 10G -s -n mysnap /dev/myvg/mylv
  Using default stripesize 64.00 KiB.
  Logical volume "mysnap" created.
[root@localhost ~]# mount /dev/myvg/mysnap /snap/
```


练习：
1、创建一个10G的卷组，取名给myvg，PE大小为16MB
```
[root@localhost ~]# fdisk -l /dev/sdd

磁盘 /dev/sdd：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x2a039123

   设备 Boot      Start         End      Blocks   Id  System
/dev/sdd1            2048    10487807     5242880   8e  Linux LVM
/dev/sdd2        10487808    20973567     5242880   8e  Linux LVM
/dev/sdd3        20973568    31459327     5242880   8e  Linux LVM
/dev/sdd4        31459328    41943039     5241856   8e  Linux LVM
[root@localhost ~]# pvcreate /dev/sdd{1,2}
  Physical volume "/dev/sdd1" successfully created
  Physical volume "/dev/sdd2" successfully created

[root@localhost ~]# vgcreate  -s 16M myvg /dev/sdd{1,2}
  Volume group "myvg" successfully created
[root@localhost ~]# vgdisplay myvg
  --- Volume group ---
  VG Name               myvg
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               9.97 GiB
  PE Size               16.00 MiB
  Total PE              638
  Alloc PE / Size       0 / 0   
  Free  PE / Size       638 / 9.97 GiB
  VG UUID               RpVYYx-SsPF-2h7l-9hlW-FXMf-ti5T-iCytfw
````

2、创建一个逻辑卷，取名为mylv，大小为5G。
```
[root@localhost ~]# lvcreate -L 5G -n mylv myvg
  Logical volume "mylv" created.
```
3、为卷组myvg新增一个分区，扩展其大小为15G；
```
[root@localhost ~]# pvcreate /dev/sdd{3,4}
  Physical volume "/dev/sdd3" successfully created.
  Physical volume "/dev/sdd4" successfully created.
[root@localhost ~]# vgextend myvg /dev/sdd3
  Volume group "myvg" successfully extended
[root@localhost ~]# vgdisplay myvg
  --- Volume group ---
  VG Name               myvg
  System ID             
  Format                lvm2
  Metadata Areas        3
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               0
  Max PV                0
  Cur PV                3
  Act PV                3
  VG Size               14.95 GiB
  PE Size               16.00 MiB
  Total PE              957
  Alloc PE / Size       320 / 5.00 GiB
  Free  PE / Size       637 / 9.95 GiB
  VG UUID               RpVYYx-SsPF-2h7l-9hlW-FXMf-ti5T-iCytfw
```
4、在格式化挂载逻辑卷mylv之后，尝试扩展逻辑卷mylv大小为10G；
```
[root@localhost ~]# df -lh 
文件系统                   容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root    18G  4.8G  13G  28%  /
.....
/dev/mapper/myvg-mylv    4.8G   66M  4.5G    2% /mydata
[root@localhost ~]# umount /mydata
[root@localhost ~]# lvextend -L +5G /dev/myvg/mylv 
  Size of logical volume myvg/mylv changed from 5.00 GiB (320 extents) to 10.00 GiB (640 extents).
  Logical volume myvg/mylv successfully resized.
[root@localhost ~]# resize2fs /dev/myvg/mylv 
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/myvg/mylv to 2621440 (4k) blocks.
The filesystem on /dev/myvg/mylv is now 2621440 blocks long.
[root@localhost ~]# lvdisplay /dev/myvg/mylv
  --- Logical volume ---
  LV Path                /dev/myvg/mylv
  LV Name                mylv
  VG Name                myvg
  LV UUID                LUVw2b-hxTp-nMWh-swgU-n62l-hSL3-QVUoEY
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2018-03-08 20:48:31 +0800
  LV Status              available
  # open                 0
  LV Size                10.00 GiB
  Current LE             640
  Segments               3
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:6
```

5、使用快照备份对应的逻辑卷内容，备份完成会后，删除快照；
```
[root@localhost ~]# tar Jcf snaptest.tar.xz /snap/*
tar: 从成员名中删除开头的“/”
[root@localhost ~]# ll snaptest.tar.xz 
-rw-r--r--. 1 root root 7820112 3月   8 21:11 snaptest.tar.xz
[root@localhost ~]# umount /snap/
[root@localhost ~]# lvremove /dev/myvg/mysnap 
Do you really want to remove active logical volume myvg/mysnap? [y/n]: y
  Logical volume "mysnap" successfully removed
```
6、缩减逻辑卷mylv的大小为6G；
```
root@localhost ~]# umount /dev/myvg/mylv 
[root@localhost ~]# e2fsck -f /dev/myvg/mylv
e2fsck 1.42.9 (28-Dec-2013)
第一步: 检查inode,块,和大小
第二步: 检查目录结构
第3步: 检查目录连接性
Pass 3A: Optimizing directories
Pass 4: Checking reference counts
第5步: 检查簇概要信息

/dev/myvg/mylv: ***** 文件系统已修改 *****
/dev/myvg/mylv: 3722/655360 files (0.1% non-contiguous), 91436/2621440 blocks
[root@localhost ~]# resize2fs /dev/myvg/mylv 6G
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/myvg/mylv to 1572864 (4k) blocks.
The filesystem on /dev/myvg/mylv is now 1572864 blocks long.
[root@localhost ~]# lvreduce -L 6G /dev/myvg/mylv 
  WARNING: Reducing active logical volume to 6.00 GiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce myvg/mylv? [y/n]: y
  Size of logical volume myvg/mylv changed from 10.00 GiB (640 extents) to 6.00 GiB (384 extents).
  Logical volume myvg/mylv successfully resized.
root@localhost ~]# mount /dev/myvg/mylv /mydata/
[root@localhost ~]# head -n 2 /mydata/passwd  
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
```
7、删除逻辑卷，卷组及物理卷
```
[root@localhost ~]# umount /mydata/
[root@localhost ~]# lvremove /dev/myvg/mylv 
Do you really want to remove active logical volume myvg/mylv? [y/n]: y
  Logical volume "mylv" successfully removed
[root@localhost ~]# vgremove /dev/myvg
  Volume group "myvg" successfully removed
[root@localhost ~]# pvremove /dev/sdd{1,2,3,4}
  Labels on physical volume "/dev/sdd1" successfully wiped.
  Labels on physical volume "/dev/sdd2" successfully wiped.
  Labels on physical volume "/dev/sdd3" successfully wiped.
  Labels on physical volume "/dev/sdd4" successfully wiped.
```