#### FHS：Filesystem Hierarchy Standard  文件系统层级结构标准

    /bin：所有用户可用的基本命令程序文件
    
    /sbin：供系统管理使用的工具程序
    
    /boot：引导加载器必须用到的各静态文件：kernel，initramfs（initrd），grub等
    
    /dev：存储特殊文件或设备文件
    
          设备有两种类型：字符设备（线性设备），块设备（随机设备）
    
    /etc：系统程序的静态配置文件，不能为二进制程序     etc也是一个层级结构
    
    /home：普通用户的家目录的集中位置：一般每个普通的家目录默认为此目录下与用户名同名的子目录，/home/USERNAME
    
    /root：管理员的家目录  可选
    
    /lib：为系统启动或根文件系统上的应用程序（/bin，/sbin等）提供共享库，以及为内核提供内核模块
    
      libc.so.*：动态链接的C库
    
            ld*：运行时链接器/加载器
    
        modules：用于存储内核模块的目录
    
    /lib64：64位系统特有的存放64位共享库的路径
    
    /media：便携式设备挂载点，cdrom，floppy等
    
    /mnt：其他文件系统的临时挂载点
    
    /opt：附加应用程序的安装位置  可选路径
    
    /srv：当前主机为服务提供的数据
    
    /tmp：为那些会产生临时文件的程序提供用于存储临时文件的目录，可供用户执行写入操作，有特殊权限
    
    /usr：usr Hierarchy：全局共享的只读数据路径    
    
        bin,sbin,lib,lib64     
    
        include：C程序头文件   
    
        share：命令手册页和自带文档等架构特有的文件的存储位置
    
        local：另一个层级目录
    
        X11R6：X-winow程序的安装位置
    
        src：程序源码文件的存储位置
    
    /usr/local：Local hieearchy，让系统管理员安装本地应用程序，也通常用于安装第三方程序
    
    /var：/var Hierarchy，存储经常发生变化的
    
       cache：Application cache data
    
         lib：Variable state information
    
       local：Variable data for /usr/local
    
       lock ：Lock files
    
         log：Log files and directories
    
         opt：Variable data for /opt
    
         run：Data relevant to running processes
    
       spool：Application spool data
    
         tmp： Temporary files preserved between system reboots
    
    /proc：内核及进程存储其相关信息，它们多为内核参数，例如net.ipv4.ip_forward，虚拟为net/ipv4/ip_forward，存储于/proc/sys/，
    
    因此其完整路径为/proc/sys/net/ipv4/ip_forward
    
    /sys：sysfs是虚拟文件系统提供了一种比proc更为理想的访问内核数据的途径，其主要作用在于为管理Linux设备提供一种统一管理模型的接口

   参考：http://www.ibm.com/developerworks/cn/linux/l-cn-sysfs/


#### Linux系统上的文件类型
-：常规文件，即f
d：directory，目录文件
b：block decvice，块设备文件，以“block”为单位进行随机访问
c：character device，字符设备文件，支持以“character”为单位进行线性访问
    major number：主设备号，用于标识设备类型，尽而确定要加载的驱动程序
    minor number：次设备号，用于标识同一类型中的不同的设备
         8位二进制：0-255
l：symbolic link，符号链接文件（类似于Windows上的快捷方式），也叫作软链接文件（soft link）
p：pipe，命名管道
s：socker，套接字文件