**RAID:Redundant Arrays of Independent（Inexpensive） Disks
独立（廉价）磁盘冗余阵列**
                                       
Berkeley提出: A case for Redundent Arrays of Inexpensive Disks RAID

>提高IO能力：磁盘并行读写；
>提高耐用性；磁盘冗余来实现

**级别：多块磁盘组织在一起的工作方式有所不同；**
**常用级别：RAID-0, RAID-1, RAID-5, RAID-10, RAID-50, JBOD**
#### RAID实现的方式： 
 - 硬件实现方式
  外接式磁盘阵列：通过扩展卡提供适配能力
  内接式RAID：主板集成RAID控制器
- Software RAID：软件方式实现

>级别：level
>        RAID-0：0, 条带卷，strip;
>        RAID-1: 1, 镜像卷，mirror;
>        RAID-2
>        ..
>        RAID-5：
>        RAID-6
>        RAID10
>        RAID01



##### RAID-0:![](https://upload-images.jianshu.io/upload_images/16547068-a16923bd87ed2d12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/200)
RAID 0最简单的实现方式就是将N块硬盘（最好容量相同）串联在一起形成一个大的硬盘组，在写入数据时，数据会被分为一定大小的多个区块，依次写入到磁盘中（如图所示）。RAID-0的最大优点是可以成倍提高磁盘的存储容量，如三块1TB的硬盘组成的RAID 0其容量为3TB。其缺点是一旦其中任何一个硬盘出现故障，整个磁盘阵列都将无法使用，可靠性仅为单个硬盘的1/N。


​         
>读、写性能提升；
>可用空间：N*min(S1,S2,...)
>无容错能力
>最少磁盘数：2, 2+


##### RAID-1：![](https://upload-images.jianshu.io/upload_images/16547068-a2de413be06aa7a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/200)
RAID 1被称为磁盘镜像模式，原理在一个硬盘上写入数据时会在闲置硬盘上也同时写入，生成镜像文件，在不影响性能的情况下尽可能保证可靠性，只要磁盘阵列中任何一块硬盘出现问题，都可以有另一块镜像硬盘可以使用，具备很好的冗余能力。虽然这样对数据保存会比较安全，但成本也会增加，硬盘利用率仅为50%。另外，一旦这个磁盘阵列出现故障，那么整个阵列的可靠性也相对不再可靠，必须及时更换硬盘。因此RAID 1主要用在保存关键性数据的场合。
>读性能提升、写性能略有下降；
>可用空间：1*min(S1,S2,...)
>有冗余能力
>最少磁盘数：2, 2+


##### RAID-4：![](https://upload-images.jianshu.io/upload_images/16547068-b89c958a023eb73b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)
RAID 4以数据块为单位，也就是按磁盘为单位。这样的特点使得RAID 4在写入小文件时比RAID 3要快，但在恢复数据时RAID 4的难度要大得多，控制器的设计也要复杂得多。

>1101, 0110, 异或运算得 1011（校验码）
>如果数据丢失 可从校验码推断出丢失数据信息
>缺陷：其中一块盘作为集中校验盘，访问压力较大，导致它作为性能瓶颈存在

##### RAID-5：![](https://upload-images.jianshu.io/upload_images/16547068-4ba3eb3d3326d875.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)
RAID 5是一种分布式的独立磁盘阵列，被设计为一种存储性能、数据安全和存储成本兼顾的存储解决方案，也可以理解为是RAID 0和RAID 1的折中方案。
RAID 5与RAID 4一样将数据以块为单位分步在各个硬盘上。RAID 5不对数据进行备份，而是把数据和对应的奇偶校验信息存储在组成磁盘阵列的各个磁盘上，并且奇偶校验信息和与其相对应的信息存放在不同的硬盘上。当一个硬盘损坏时，系统可以根据在另一块硬盘上存放的该损坏数据相对应的奇偶校验信息来恢复数据。RAID 5只允许同时损坏一个硬盘，所以如果一个硬盘出现故障后RAID 5还能够正常读取数据，但应及时更换损坏的硬盘，否则再出现另外的硬盘损坏则整个磁盘阵列将会崩溃。
>轮流做校验盘
>            读、写性能提升
>            可用空间：(N-1)*min(S1,S2,...)
>            有容错能力：1块磁盘
>            最少磁盘数：3, 3+

##### RAID-6：![](https://upload-images.jianshu.io/upload_images/16547068-c0f14ef3b20cbdfb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

>读、写性能提升
>可用空间：(N-2)*min(S1,S2,...)
>有容错能力：2块磁盘
>最少磁盘数：4, 4+


#### 混合类型
##### RAID-10：![](https://upload-images.jianshu.io/upload_images/16547068-0df603995a279561.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)
RAID 10是先镜射再分割数据，再将所有硬盘分为两组，视为是RAID 0的最低组合，然后将这两组各自视为RAID 1运作。当RAID 10有一个硬盘受损，其余硬盘会继续运作。

>读、写性能提升
>可用空间：N*min(S1,S2,...)/2
>有容错能力：每组镜像最多只能坏一块；
>最少磁盘数：4, 4+
##### RAID-01：![](https://upload-images.jianshu.io/upload_images/16547068-29d38c942e8f2819.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)
RAID 01则是跟RAID 10的程序相反，是先分割再将数据镜射到两组硬盘。它将所有的硬盘分为两组，变成RAID 1的最低组合，而将两组硬盘各自视为RAID 0运作。
RAID 01只要有一个硬盘受损，同组RAID 0的所有硬盘都会停止运作，只剩下其他组的硬盘运作，可靠性较低。



##### JBOD：Just a Bunch Of Disks![](https://upload-images.jianshu.io/upload_images/16547068-5fad5bc18463be4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)
JBOD并不是RAID的等级。由于并没有规范，市场上有两类主流的做法

- 使用单独的链接端口如SATA、USB或1394同时控制多个各别独立的硬盘，使用这种模式通常是较高端的设备，还具备有RAID的功能，不需要依靠JBOD达到合并逻辑扇区的目的。
- 只是将多个硬盘空间合并成一个大的逻辑硬盘，没有错误备援机制。

数据的存放机制是由第一颗硬盘开始依序往后存放，即操作系统看到的是一个大硬盘（由许多小硬盘组成的）。但如果硬盘损毁，则该颗硬盘上的所有数据将无法救回。若第一颗硬盘损坏，通常无法作救援（因为大部分文件系统将磁盘分割表（partition table）存在磁盘前端，即第一颗），失去磁盘分割表即失去一切数据，若遭遇磁盘阵列数据或硬盘出错的状况，危险程度较RAID 0更剧。它的好处是不会像RAID 0，每次访问都要读写全部硬盘。但在部分的JBOD数据恢复实践中，可以恢复未损毁之硬盘上的数据。同时，因为每次读写操作只作用于单一硬盘，JBOD的传输速率与I/O表现均与单颗硬盘无异。

>功能：将多块磁盘的空间合并一个大的连续空间使用；
>可用空间：sum(S1,S2,...)


#### 主流 RAID 等级技术对比
![](https://upload-images.jianshu.io/upload_images/16547068-3060546d45b863c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

​    

#### CentOS 6上的软件RAID的实现：
结合内核中的md(multi devices)

**mdadm：模式化的工具**
命令的语法格式：`mdadm [mode] <raiddevice> [options] <component-devices>`
支持的RAID级别：LINEAR, RAID0, RAID1, RAID4, RAID5, RAID6, RAID10;

**模式：**
>创建：-C
>装配: -A
>监控: -F
>管理：-f, -r, -a

<raiddevice>: `/dev/md#`
<component-devices>: 任意块设备


**-C: 创建模式**
>-n #: 使用#个块设备来创建此RAID；
>-l #：指明要创建的RAID的级别；
>-a {yes|no}：自动创建目标RAID设备的设备文件；
>-c CHUNK_SIZE: 指明块大小；
>-x #: 指明空闲盘的个数；

例如：创建一个10G可用空间的RAID5；

**-D：显示raid的详细信息；**
`mdadm -D /dev/md#`

**管理模式：**
>-f: 标记指定磁盘为损坏；
>-a: 添加磁盘
>-r: 移除磁盘

**观察md的状态：**
`cat /proc/mdstat`

**停止md设备：**
`mdadm -S /dev/md#`

#### watch命令：
**-n #: 刷新间隔，单位是秒**
`watch -n# 'COMMAND'`

    [root@centos7 ~]# watch -n1 'ifconfig ens33 '