#### yum概述
rpm很大程度上简化了安装程序的流程，但它也存在一个缺陷：不能解决程序包的依赖问题。而yum的诞生就是为了弥补这个不足。yum不能说是一款全新的程序包管理工具，它只是rpm的前端管理工具。之所以这么说，是因为yum的安装、升级、卸载、查询等操作，都要依靠rpm来完成。要想使用yum，系统上必须安装rpm，yum程序包是依赖于rpm程序包的。

yum是一款基于rpm包管理器的前端程序包管理器，全称为Yellow dog Update Modifier。它可以从指定服务器上自动下载程序包，并自动分析程序包的元数据、自动处理程序包之间的依赖关系，能一次性安装完所有依赖的包，而无须繁琐地一次次安装所有依赖包。

#### yum的工作原理
yum是基于C-S（客户端-服务器）模式的，用户从服务器上下载程序包进行安装。为此，我们需要在服务器上构建一个存储rpm包的仓库，用于存储rpm包，并接受来自客户端的访问。为了方便客户端一次性获取全部rpm包详细信息，以分析依赖关系，服务器端将仓库中所有rpm包的详细信息（包括依赖关系信息）提取出来，做成仓库的元数据文件。这样依赖，客户端只需获取元数据文件，便能得到整个仓库中rpm包的信息，以进行依赖关系分析。 

**第一次使用yum时的的工作方式如下所示：**
>第一步：获取yum仓库元数据文件，并缓存在本地的/var/cache/yum目录下。 
>第二步：在元数据文件中查找用户选择要安装的程序包，根据元数据文件在本地分析要安装的程序包的依赖关系，结合当前系统上已安装的程序包，列出需要但未安装的程序包清单。 
>第三步：将需要下载的程序包清单发送给yum仓库。 
>第四步：下载各程序包缓存至本地，并安装。 
>第五步：程序包安装结束后，缓存的程序包文件将被删除，但缓存的仓库元数据文件将被保留，用于下次安装时使用。

![](https://upload-images.jianshu.io/upload_images/16547068-35874ecb2562ba93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)

保留yum仓库元数据文件，能够节省带宽，但同时也带来一个问题：yum仓库如果进行了更新，它的元数据文件也会发生变化，这样以来客户端的元数据文件和服务器的元数据文件就不一致了。为了解决者两个文件的一致性问题，便需要用到元数据文件的校验码了。我们可以在yum仓库中为元数据文件生成一个校验码，客户端从服务器请求元数据文件校验码，与缓存中的元数据文件进行比对，相同则缓存与服务器元数据文件一致，可以使用，不同则不一致，需要重新下载元数据文件。

**后续使用yum安装程序包的流程：**
>第一步：获取yum仓库校验码文件。 
>第二步：核对校验码，相同则使用缓存的元数据文件，不同则重新下载元数据文件。 
>第三步：之后的步骤与上面介绍的首次使用yum相同。

![](https://upload-images.jianshu.io/upload_images/16547068-2a9eec0cee14cacb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)


**CentOS上两个常见的前端工具： yum, dnf**
**yum repository: yum repo**
存储了众多rpm包，以及包的相关的元数据文件（放置于特定目录下：repodata）；



#### yum客户端：
##### 配置文件：
**/etc/yum.conf：为所有仓库提供公共配置**
```
[main]
cachedir=/var/cache/yum/$basearch/$releasever #设置缓存目录
keepcache=0     #安装后是否保留缓存
debuglevel=2    #调试消息输出等级
logfile=/var/log/yum.log    #日志文件路径
exactarch=1     #是否做精确平台匹配
obsoletes=1     
gpgcheck=1      #是否检查来源合法性和包完整性
plugins=1       #是否支持插件机制
installonly_limit=5     #最多同时安装多少个程序包
bugtracker_url=http://bugs.centos.org/set_project.php?
project_id=23&ref=http://bugs.centos.org/bug_report_page.php?category=yum       #bug追踪URL
distroverpkg=centos-release     #发行版版本
```
**/etc/yum.repos.d/\*.repo：为仓库的指向提供配置**
```
[repositoryID]      #仓库名
name=Some name for this repository      #对repo作功能性说明
baseurl=url://path/to/repository/       #仓库指向的路径，可指向多个路径
enabled={1|0}       #是否启用该仓库，默认为1(启用)
gpgcheck={1|0}      #是否要对程序包数据的来源合法性和数据完整性做校验
gpgkey=URL      #指定GPG密钥文件的访问路径，可由仓库提供
enablegroups={1|0}      #是否允许以组的方式管理仓库；
failovermethod={roundrobin|priority}        #当baseurl同时指向多个仓库路径时，可指定以什么方式选择url去访问仓库，以及当某一路径访问失败时，可指定如何再选择路径；roundrobin是随机挑选路径访问，priority是自上而下选择路径访问；默认为roundrobin
cost=       #开销；开销越小，该仓库url更优；默认为1000。
```
**仓库文件服务器指向：**
>ftp://
>http://
>nfs://
>file:///
#### yum命令的用法：
`yum [options] [command] [package ...]`
command is one of:
>\* install package1 [package2] [...]
>\* update [package1] [package2] [...]
>\* update-to [package1] [package2] [...]
>\* check-update
>\* upgrade [package1] [package2] [...]
>\* upgrade-to [package1] [package2] [...]
>\* distribution-synchronization [package1] [package2] [...]
>\* remove | erase package1 [package2] [...]
>\* list [...]
>\* info [...]
>\* provides | whatprovides feature1 [feature2] [...]
>\* clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]
>\* makecache
>\* groupinstall group1 [group2] [...]
>\* groupupdate group1 [group2] [...]
>\* grouplist [hidden] [groupwildcard] [...]
>\* groupremove group1 [group2] [...]
>\* groupinfo group1 [...]
>\* search string1 [string2] [...]
>\* shell [filename]
>\* resolvedep dep1 [dep2] [...]
>\* localinstall rpmfile1 [rpmfile2] [...]
>(maintained for legacy reasons only - use install)
>\* localupdate rpmfile1 [rpmfile2] [...]
>(maintained for legacy reasons only - use update)
>\* reinstall package1 [package2] [...]
>\* downgrade package1 [package2] [...]
>\* deplist package1 [package2] [...]
>\* repolist [all|enabled|disabled]
>\* version [ all | installed | available | group-* | nogroups* | grouplist | groupinfo ]
>\* history [info|list|packages-list|packages-info|summary|addon-info|redo|undo|rollback|new|sync|stats]
>\* check
>\* help [command]

**显示仓库列表：**
`repolist [all|enabled|disabled]`
**显示程序包：list**
`yum list [all | glob_exp1] [glob_exp2] [...]`
`yum list {available|installed|updates} [glob_exp1] [...]`
**安装程序包：**
`yum install package1 [package2] [...]`
`yum reinstall package1 [package2] [...] ` (重新安装)
**升级程序包：**
`yum update [package1] [package2] [...]`
`yum downgrade package1 [package2] [...] `(降级)
**检查可用升级：**
`yum check-update`
**卸载程序包：**
`yum remove | erase package1 [package2] [...]`
**查看程序包information：**
` yum info package1 [package2] [...]`
**查看指定的特性(可以是某文件)是由哪个程序包所提供：**
`yum provides | whatprovides feature1 [feature2] [...]`
**清理本地缓存：**
`yum clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]`
**构建缓存：**
`yum makecache`
**搜索：以指定的关键字搜索程序包名及summary信息**
`yum search string1 [string2] [...]`
**查看指定包所依赖的capabilities：**
`yum deplist package1 [package2] [...]`
**查看yum事务历史：**
`yum history [info|list|packages-list|packages-info|summary|addon-info|redo|undo|rollback|new|sync|stats]`

##### 安装及升级本地程序包：
`yum localinstall rpmfile1 [rpmfile2] [...]`      (maintained for legacy reasons only - use install)
`yum localupdate rpmfile1 [rpmfile2] [...]`       (maintained for legacy reasons only - use update)

##### 包组管理的相关命令：
`yum groupinstall group1 [group2] [...]`
`yum groupupdate group1 [group2] [...]`
`yum grouplist [hidden] [groupwildcard] [...]`
`yum groupremove group1 [group2] [...]`
`yum groupinfo group1 [...]`

##### 如何使用光盘当作本地yum仓库：
(1) 挂载光盘至某目录，例如/media/cdrom
`mount -r -t iso9660 /dev/cdrom /media/cdrom`
(2) 创建配置文件
```
[CentOS 7]
name=this is a local repo.
baseurl=file:///media/cdrom
gpgcheck=0
enabled=1
```
##### yum的命令行选项：
>--nogpgcheck：禁止进行gpg check；
>-y: 自动回答为“yes”；
>-q：静默模式；
>--disablerepo=repoidglob：临时禁用此处指定的repo；
>--enablerepo=repoidglob：临时启用此处指定的repo；
>--noplugins：禁用所有插件；

##### yum的repo配置文件中可用的变量：
>\$releasever: 当前OS的发行版的主版本号；
>\$arch: 平台；
>\$basearch：基础平台；
>\$YUM0-\$YUM9
>[http://mirrors.magedu.com/centos/\$releasever/\$basearch/os](http://mirrors.magedu.com/centos/\$releasever/\$basearch/os)
>如：http://mirrors.magedu.com/centos/7/x86_64/os
##### 创建yum仓库：
`createrepo [options] <directory>`
```
[root@promote ~]# yum install createrepo
```
**练习：使用自制的yum源安装ftp、openssh、wget、tcpdump等软件包**
使用光盘镜像中的包模拟创建本地仓库
```
[root@promote ~]# mount -r -t iso9660 /dev/cdrom /media/cdrom/
[root@promote ~]# mkdir -p /yumrepo/Packages            ###创建本地packages目录
###将需要的Packages拷贝到本地目录
cp /media/cdrom/Packages/ftp-0.17-67.el7.x86_64.rpm /yumrepo/Packages/
cp /media/cdrom/Packages/openssh-* /yumrepo/Packages/
cp /media/cdrom/Packages/curl-7.29.0-35.el7.centos.x86_64.rpm /yumrepo/Packages/
cp /media/cdrom/Packages/wget-1.14-13.el7.x86_64.rpm /yumrepo/Packages/
cp /media/cdrom/Packages/tcpdump-4.5.1-3.el7.x86_64.rpm /yumrepo/Packages/
###使用createrepo命令创建本地yum源(如果没有此命令可以使用yum -y install createrepo安装)

[root@promote yumrepo]# createrepo /yumrepo/
Spawning worker 0 with 15 pkgs
Workers Finished
Gathering worker results

Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete
```
修改yum配置文件，将仓库指向本地源
```
[root@www ~]# cd /etc/yum.repos.d/
[root@www yum.repos.d]# vim local.repo
    [yumrepo]
    name=yumrepo
    baseurl=file:///yumrepo
    enabled=1
    gpgcheck=0
[root@www yum.repos.d]# yum repolist         #######查看yum仓库
已加载插件：fastestmirror, security
Loading mirror speeds from cached hostfile
 * base: mirror.lzu.edu.cn
 * extras: mirror.lzu.edu.cn
 * updates: mirrors.cqu.edu.cn
仓库标识                                 仓库名称                                   状态
base                                     CentOS-6 - Base                         6,713
extras                                   CentOS-6 - Extras                       35
updates                                  CentOS-6 - Updates                      251
yumrepo                                  yumrepo                                 15
repolist: 7,014
```
安装ftp、openssh、curl、wget、tcpdump等软件包
```
yum install -y ftp openssh curl wget tcpdump
```