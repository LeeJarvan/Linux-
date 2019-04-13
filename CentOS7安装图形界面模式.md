本人电通过VMware14安装Centos7过程中出现蓝屏现象，最后发现是因为在系统安装图形界面过程中报错导致，所以决定先通过安装最小化命令行模式，通过yum进行安装图形界面模式。


*Step1：安装并开启CentOS7，登录root用户
Step2：配置网络网卡，确保与外网保持联通
Step3：获取并安装图形界面GNOME的程序包
Step4：修改CentOS默认启动模式为图形化模式
Step5：重启CentOS7*


在VMware虚拟机中安装CentOS系统，若没有提前配置安装过GUI图形界面的程序包，则系统安装成功后初次启动系统会默认进入命令行模式的界面，如下：

![](//upload-images.jianshu.io/upload_images/4866277-c213fb9341e74f88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/607/format/webp)

从操作习惯和便利性角度，为了从命令行模式转换为图形界面模式启动系统，需要下载CentOS系统所需的GUI模式的程序包进行安装和配置，具体操作步骤如下：

#### Step-1：安装并开启CentOS7，登录root用户

如上所示，相关登录信息在安装CentOS7时就已经配置完成，localhost login和Password为：
```
超级管理员==> root---root
普通用户  ==> test001---123456
```

#### Step-2：配置网络网卡，确保与外网保持联通

以命令 `cd /etc/sysconfig/network-scripts/` 进入network-scripts目录下，找到文件**ifcfg-ens33**(具体名字可能因系统不同而各异，如eth0、eth33...)，对该文件进行配置网卡信息

![image](//upload-images.jianshu.io/upload_images/4866277-f047c0ef89d82a96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/794/format/webp)

以命令 `vi ifcfg-ens33` 打开网络配置文件，【INS】键进入编辑输入模式，在文件末尾加上（***根据需要添加or变更，非必须***）

```
# 指定DNS服务器的IP地址，使其可正常解析域名，从而访问外网
DNS1=8.8.8.8
DNS2=4.2.2.2

```

并配置

```
# 启动网络时，启动该设备
ONBOOT=yes

```

![image](//upload-images.jianshu.io/upload_images/4866277-6f150a717e8c6053.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/376/format/webp)

【ESC】键退出编辑模式，然后以命令 `:wq` 保存并退出该网络配置文件

重新加载网络配置文件，使得刚才的配置生效
操作命令：`service network restart`

![image](//upload-images.jianshu.io/upload_images/4866277-8cd54922fef75841.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/555/format/webp)

Ping一下，检查是否联通外网

![image](//upload-images.jianshu.io/upload_images/4866277-97534632bec6cebe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/618/format/webp)

#### Step-3：获取并安装图形界面GNOME的程序包

以命令`yum`检查yum是否可正常使用

![](//upload-images.jianshu.io/upload_images/4866277-2eed06cf37abbbdb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/635/format/webp)

以命令 `yum groupinstall "GNOME Desktop" "Graphical Administration Tools"` 获取并安装CentOS默认的图形界面GNOME程序包

PS：
*若安装期间出现错误，比如提示某个目录下的包文件 xxx.noarch 冲突，则使用命令 `yum -y remove xxx.noarch` 移除该冲突文件后，再以命令 `yum groupinstall "GNOME Desktop" "Graphical Administration Tools"` 安装GNOME图形模块*

过程中，会有提示类似"... is ok?(Y/N)"，直接选择Y，回车。然后就是Waiting。。。一直到提示"Completed!"，表示已经安装GNOME程序包完成

![](//upload-images.jianshu.io/upload_images/4866277-c0fbb7495b78f3e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800/format/webp)

#### Step-4：修改CentOS默认启动模式为图形化模式

以命令 `systemctl get-default` 可查看当前默认的模式为multi-user.target，即命令行模式

![](//upload-images.jianshu.io/upload_images/4866277-eccd05bf907e4b67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/455/format/webp)

需要以命令 `systemctl set-default graphical.target` 修改为图形界面模式

```
# 修改模式命令：
systemctl set-default graphical.target  # 将默认模式修改为图形界面模式
systemctl set-default multi-user.target # 将默认模式修改为命令行模式

```

![](//upload-images.jianshu.io/upload_images/4866277-61c0bf32690f424d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/819/format/webp)

再次以命令 `systemctl get-default` 即可查看当前修改后的默认模式为graphical.target，即图形界面模式

![](//upload-images.jianshu.io/upload_images/4866277-32db0a8bb1a4e613.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/467/format/webp)

#### Step-5：重启CentOS，检验GUI界面效果

以命令 `reboot` 重启CentOS系统，Waiting。。。此时，已切换进入到GUI图形界面模式，如下：

![](//upload-images.jianshu.io/upload_images/4866277-73b8cc6d4b7781c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800/format/webp)

点击登录用户头像区域，输入密码，即可进行登录

![image](//upload-images.jianshu.io/upload_images/4866277-1ac7b29cd7c308da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800/format/webp)

进入到CentOS7的桌面

![](//upload-images.jianshu.io/upload_images/4866277-62808574ab89e031.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)