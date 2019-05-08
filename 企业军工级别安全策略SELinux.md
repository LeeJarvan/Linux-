#### SELinux
>SELinux 全称 Security Enhanced Linux (安全加强 Linux)，是 MAC (Mandatory Access Control，强制访问控制系统)的一个实现，目的在于明确的指明某个进程可以访问哪些资源(文件、网络端口等)。


#### DAC与MAC
- DAC：Linux自己的安全机制叫做DAC（Discretionary Access Control，自主访问控制）
- MAC：SELinux实现的功能叫做MAC（Mandatory Access Control，强制访问控制机制）

#### SELinux有两种工作级别：
- strict：严格级别，每个进程都受到selinux的控制
- targeted：仅有限个进程受到selinux的控制,，只监控容易被入侵的进程
```
[root@promote ~]# cat /etc/sysconfig/selinux 

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted 
```
####SELinux工作机制：
SELinux采用类似沙箱（sandbox）的方式来运行进程：

>subject operation object
>subject：进程
>object：可以是进程，可以是文件
>适用于文件的操作：open，read，write，close，chown，chmod

**SELinux为每个文件提供了安全标签，也为进程提供了安全标签：**
>user:role:type
>user：SELinux的user
>role：角色
>type：类型
```
[root@promote ~]# ps auxZ
LABEL                           USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
system_u:system_r:init_t:s0     root          1  0.3  0.3 128364  6956 ?        Ss   21:12   0:01 /usr/lib/systemd/s
system_u:system_r:kernel_t:s0   root          2  0.0  0.0      0     0 ?        S    21:12   0:00 [kthreadd]
```
```
[root@promote ~]# ls -Z
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 1.sh
-rw-------. root root system_u:object_r:admin_home_t:s0 anaconda-ks.cfg
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 epel-release-latest-7.noarch.rpm
```

#### SELinux规则库：
>规则：定义了哪种域能访问哪种或哪些种类型内的文件
>遵循“法无授权即禁止”的规则，也就是说没有明确授权的所有操作均禁止

#### 启动SELinux
>selinux的配置
>SELinux是否启用：在/etc/selinux/config文件中定义
```
[root@promote ~]# vim /etc/sysconfig/selinux 
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing   修改此处为enforcing
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
##### SELinux的状态：
>enforcing：强制，每个受限的进程都必然受限
>permissive：启用，每个受限的进程违规操作时不会被禁止，但会被记录于审计日志
>disabled：禁用

相关命令：
**getenforce：获取selinux当前状态**
```
[root@promote ~]# getenforce
Enforcing
```
>setenforce 0｜1
>0：设置为permissive
>1：设置为enforcing
>
>此设定仅当前有效，重启系统后无效
>永久有效需要改配置文件：/etc/sysconfig/selinux，/etc/selinux/config
>SELINUX=｛disabled｜enforcing｜permissive｝

##### 给文件重新打标签：
**chcon：change file SELinux security context，改变上下文**
`chcon [option]... CONTEXT FILE...`
`chcon [option]... [-u USER] [-r ROLE] [-t TYPE] FILE...`
`chcon [option]...  --reference=RFILE FILE...`
```
[root@promote tmp]# chcon -t user_tmp_t home.txt
[root@promote tmp]# ls -Z home.txt
-rw-r--r--. root root unconfined_u:object_r:user_tmp_t:s0 home.txt
```
**-R：递归打标签**

>还原文件的默认标签： 
>restorecon [-R] /path/to/somewhere（可以是文件，也可以是目录）

##### 设定某些布尔型特性：
`getsebool [-a] [boolean]`
`setsebool [ -PV] boolean value | bool1=val1 bool2=val2 ...`
**-P：把设置添加进规则库，使之永久生效，若不使用此选项则只当前有效，重启系统会失效**
```
[root@promote ~]# getsebool ftp_home_dir
ftp_home_dir --> off
[root@promote ~]# setsebool ftp_home_dir 1
ftp_home_dir --> on
[root@promote ~]# setsebool ftp_home_dir 0
ftp_home_dir --> off
[root@promote ~]# setsebool ftp_home_dir on
ftp_home_dir --> on
[root@promote ~]# setsebool -P  ftp_home_dir on
ftp_home_dir --> on
```