#### 概述：
**API：Application Program Interface
ABI：Application Binary Interface**

>Unix-like格式：ELF
>Windows格式：exe, msi

**库级别的虚拟化：**
>Linux: WinE（虚拟出windows运行环境）
>Windows: Cywin（虚拟出linux运行环境）

**系统级开发：**
>C/C++：httpd, vsftpd, nginx（服务器类应用程序）
>go

**应用级开发：**
>java/Python/perl/ruby/php：
>java: hadoop,  hbase,   (依赖于jvm)
>Python：openstack, (依赖于pvm)
>perl: (依赖于perl解释器)
>ruby: (依赖于ruby解释器)
>php: 依赖于(php解释器)

**C/C++程序格式：**
>源代码：文本格式的程序代码；
>编译开发环境：编译器、头文件、开发库
>二进制格式：文本格式的程序代码 --> 编译器 --> 二进制格式（二进制程序、库文件、配置文件、帮助文件）

**java/python程序格式：**
>源代码：编译成能够在其虚拟机(jvm/pvm)运行的格式；
>开发环境：编译器、开发库
>二进制

**项目构建工具：**
>c/c++: make
>java: maven

#### 程序包管理器：
**源代码  --> 目标二进制格式（二进制程序、库文件、配置文件、帮助文件） --> 组织成为一个或有限几个“包”文件**
管理“包”操作：安装、升级、卸载、查询、校验（Linux）

**程序包管理器功能：将编译好的应用程序的各组成文件打包成一个或几个程序包文件，从而更方便地实现程序包的安装、升级、卸载和查询等管理操作；**

##### 包管理器：
>debian：dpt工具,，命令行dpkg，程序包以 ".deb"为后缀
>redhat：redhat package manager，rpm工具，程序包以 ".rpm"为后缀； rpm is package manager（rpm）
>S.u.S.E：rpm，程序包以 ".rpm"为后缀； 
>
>Gentoo：ports机制实现统一安装管理


**程序包源代码命令格式：name-VERSION.tar.gz**
**VERSION：major（主版本号）.minor（次版本号）.release（发行号）**

##### rpm包命名格式：
对于rpm包文件，如：httpd-2.2.15-15.el6.centos.1.i686.rpm
|字符名称|含义|
 |:--- |:---:|
httpd-2.2.15-15.el6.centos.1.i686.rpm|软件的包全名|
httpd|软件的包名|
2.2.15|软件的版本|
|15|软件的发布次数|
|el6.centos|适合的Linux平台|
|i686|适合的硬件平台|
|rpm|rpm包扩展名|
```
name-VERSION-release.arch.rpm
```
>VERSION：major.minor.release
>release.arch：rpm包的发行号
>release.os: 2.el7.i386.rpm
>archetecture（架构，即操作系统平台类型）：i386, x64(amd64), ppc, noarch（平台无关，适用于全部平台）
>如：redis-3.0.2.tar.gz --> redis-3.0.2-1.centos7.x64.rpm
>![](https://upload-images.jianshu.io/upload_images/16547068-095ff12587e3ed9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**拆包：主包和支包**
>主包：name-VERSION-release.arch.rpm
>支包：name-function-VERSION-release.arch.rpm
>function：devel, utils（工具程序）, libs（库文件）, ...

**包与包之间存在依赖关系：**
>假如存在X, Y, Z三个包
>X依赖于Y,Z
>Y依赖于A, B, C
>C依赖于Y

##### 前端工具：自动解决依赖关系
>yum：rhel系列系统上rpm包管理器的前端工具；
>apt-get (apt-cache)：deb包管理器的前端工具；
>zypper：suse的rpm管理器前端工具；
>dnf：Fedora 22+系统上rpm包管理器的前端工具；

##### 程序包管理器组成格式：
**1、程序包的组成清单（每个程序包都单独实现）**
>文件清单
>安装或卸载时运行的脚本

**2、数据库（公共）**
>程序包的名称和版本；
>包和包依赖关系；
>各包功能说明；
>安装生成的各文件的文件路径及校验码信息；
>...
>centos上存取rpm包数据库的路径：/var/lib/rpm/
```
[root@localhost Packages]# ls /var/lib/rpm
Basenames     Group       Obsoletename  Requirename  Triggername
Conflictname  Installtid  Packages      Sha1header
Dirnames      Name        Providename   Sigmd5
```
#### 获取程序包的途径：

**(1) 系统发行版的光盘或官方的文件服务器（或镜像站点）**
[http://mirrors.aliyun.com](http://mirrors.aliyun.com/)
[http://mirrors.sohu.com](http://mirrors.sohu.com/)
[http://mirrors.163.com](http://mirrors.163.com/)
**(2) 项目的官方站点**
**(3) 第三方组织**
- EPEL：是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。
- 搜索引擎
  [http://pkgs.org](http://pkgs.org/)
  [http://rpmfind.net](http://rpmfind.net/)
  [http://rpm.pbone.net](http://rpm.pbone.net/)

**(4) 自动动手，丰衣足食**

**建议：检查下载包其合法性**
>来源合法性；
>程序包的完整性；

#### CentOS系统上rpm命令管理程序包：
**安装、升级、卸载、查询和校验、数据库维护**
rpm命令：`rpm  [OPTIONS]  [PACKAGE_FILE]`

>**安装：-i, --install**
>**升级：-U, --update, -F, --freshen**
>**卸载：-e, --erase**
>**查询：-q, --query**
>**校验：-V, --verify**
>**数据库维护：--builddb, --initdb**

##### 安装：
`rpm {-i|--install} [install-options] PACKAGE_FILE ...`
安装程序包：`rpm  -ivh  PACKAGE_FILE ...`

**GENERAL OPTIONS：**
>-v：verbose，详细信息
>-vv：更详细的输出

**[install-options]：**
>-h：hash marks输出进度条；每个#表示2%的进度；
>--test：测试安装，检查并报告依赖关系及冲突消息等；
>--nodeps：忽略依赖关系；（不建议）
>--replacepkgs：重新安装
>注意：rpm可以自带脚本；

|   脚本类型    |            脚本运行时段            | 不执行对于脚本时的选项 |
| :-----------: | :--------------------------------: | :--------------------: |
|  preinstall   |     安装过程开始之前运行的脚本     |        --nopre         |
|  postinstall  |     安装过程完成之后运行的脚本     |        --nopost        |
| preuninstall  | 卸载过程真正开始执行之前运行的脚本 |       --nopreun        |
| postuninstall |     卸载过程完成之后运行的脚本     |       --nopostun       |


**--nosignature：不检查包签名信息，不检查来源合法性；
--nodigest：不检查包完整性信息；**

**安装rpm包示例：**

- 安装zsh：
```
[root@localhost Packages]# rpm -ivh zsh-5.0.2-7.el7.x86_64.rpm 
警告：zsh-5.0.2-7.el7.x86_64.rpm: 头V3 RSA/SHA256 Signature, 密钥 ID f4a80eb5: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:zsh-5.0.2-7.el7                  ################################# [100%]
```
- 测试安装zsh：
```
root@localhost Packages]# rpm -ivh --test zsh-5.0.2-7.el7.x86_64.rpm 
警告：zsh-5.0.2-7.el7.x86_64.rpm: 头V3 RSA/SHA256 Signature, 密钥 ID f4a80eb5: NOKEY
准备中...                          ################################# [100%]
```
##### 升级：
`rpm {-U|--upgrade} [install-options] PACKAGE_FILE ...`
`rpm {-F|--freshen} [install-options] PACKAGE_FILE ...`

**-U：升级或安装**
**-F：升级**

升级程序包：`rpm  -Uvh PACKAGE_FILE ...`   `rpm  -Fvh PACKAGE_FILE ...`

**--oldpackage：降级；
--force：强制升级；**

**注意：**
>(1) 不要对内核做升级操作；Linux支持多内核版本并存，因此，直接安装新版本内核；
>(2) 如果某原程序包的配置文件安装后曾被修改过，升级时，新版本的程序提供的同一个配置文件不会覆盖原有版本的配置文件，而是把新版本的配置文件重命名(FILENAME.rpmnew)后提供；

##### 卸载：
`rpm {-e|--erase} [--allmatches] [--nodeps] [--noscripts] [--test] PACKAGE_NAME ...`
>--allmatches：卸载所有匹配指定名称的程序包的各版本；
>--nodeps：忽略依赖关系
>--test：测试卸载，dry run模式

##### 查询：
`rpm {-q|--query} [select-options] [query-options]`

**[select-options]**
PACKAGE_NAME：查询指定的程序包是否已经安装，及其版本；

>-a, --all：查询所有已经安装过的包；
>-f  FILE：查询指定的文件由哪个程序包安装生成；
>-p, --package PACKAGE_FILE：用于实现对未安装的程序包执行查询操作；
>--whatprovides CAPABILITY：查询指定的CAPABILITY由哪个程序包提供；
>--whatrequires CAPABILITY：查询指定的CAPABILITY被哪个包所依赖；

**[query-options]**
>--changelog：查询rpm包的更新日志；
>-l, --list：程序安装生成的所有文件列表；
>-i, --info：程序包相关的信息，版本号、大小、所属的包组，等；
>-c, --configfiles：查询指定的程序包提供的配置文件；
>-d, --docfiles：查询指定的程序包提供的文档；
>--provides：列出指定的程序包提供的所有的CAPABILITY；
>-R, --requires：查询指定的程序包的依赖关系；
>--scripts：查看程序包自带的脚本片断；

**用法：**
|              查询目的              | 对已安装程序包进行查询 | 对未安装程序包进行查询 |
| :--------------------------------: | :--------------------: | :--------------------: |
|   查询程序安装生成的所有文件列表   |      -ql PACKAGE       |   -qpl PACKAGE_FILE    |
| 查询指定的文件是由哪个程序包生成的 |        -qf FILE        |                        |
|   查询指定的程序包提供的配置文件   |      -qc PACKAGE       |   -qpc PACKAGE_FILE    |
|        查询程序包相关的信息        |      -qi PACKAGE       |   -qpi PACKAGE_FILE    |
|     查询指定的程序包提供的文档     |      -qd PACKAGE       |    -qpdPACKAGE_FILE    |
##### 校验：
`rpm {-V|--verify} [select-options] [verify-options] `
```
S  file Size differs #文件大小改变
M  Mode differs (includes permissions and file type) #权限改变
5  digest (formerly MD5 sum) differs #md5码改变（文件内容完整性发生改变）
D  Device major/minor number mismatch #主次设备号不匹配
L  readLink(2) path mismatch #readlink路径不匹配
U  User ownership differs #属主改变
G  Group ownership differs #属组改变
T  mTime differs #时间戳改变
P  caPabilities differ #caPabilities改变
```
##### 包来源合法性验证和完整性验证：
**rpm程序包的来源合法性验证和完整性验证的实现过程：**
![](https://upload-images.jianshu.io/upload_images/16547068-0c7b3eac8a9eb318.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)


**获取并导入信任的包制作者的密钥：**
对于CentOS发行版来说：rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

**验证方式：**
(1) 安装此组织签名的程序时，会自动执行验证；
(2) 手动验证：rpm -K PACKAGE_FILE

##### 数据库维护重建：
`rpm {--initdb|--rebuilddb} [--dbpath DIRECTORY] [--root DIRECTORY]`
--initdb：初始化数据库，当前无任何数据库可实始化创建一个新的；当前有时不执行任何操作；
--rebuilddb：重新构建，通过读取当前系统上所有已经安装过的程序包进行重新创建；

**rpm管理器数据库路径：/var/lib/rpm/**

查询操作：通过此处的数据库进行；
获取数据库维护帮助文档：CentOS 6上使用`man rpm`，CentOS 7上使用`man rpmdb`