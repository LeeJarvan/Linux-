### 数据库
>数据库（Database）是按照数据结构来组织、存储和管理数据的仓库，它产生于距今六十多年前，随着信息技术和市场的发展，特别是二十世纪九十年代以后，数据管理不再仅仅是存储和管理数据，而转变成用户所需要的各种数据管理的方式。数据库有很多种类型，从最简单的存储有各种数据的表格到能够进行海量数据存储的大型数据库系统都在各个方面得到了广泛的应用。
>
>主流的数据库有：sqlserver，mysql，Oracle、SQLite、Access、MS SQL Server等，本文主要讲述的是mysql

### MySQL安装
#### yum源安装MySQL和开启设置服务
打开官方网站，上边有各种版本的yum源，找到自己想要的版本设置yum源，按照提示将例子复制到对应yum库里
https://downloads.mariadb.org/mariadb/repositories/

![](https://upload-images.jianshu.io/upload_images/16547068-8b5fe69f0fb28cdd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


>`systemctl start mariadb` 开启服务

>`ss -nutl` 打开了3306的tcp端口

>查询端口对应的进程信息 `lsof -i :3306` 或 `netstat -tnlp | grep 3306`

```
[root@promote ~]# netstat -tnlp | grep 3306
tcp6       0      0 :::3306                 :::*                    LISTEN      4594/mysqld        
```

#### 运行MySQL

```
[root@promote ~]# mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 8
Server version: 10.3.15-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> select user();  查看哪个用户登录的
+----------------+
| user()         |
+----------------+
| root@localhost |   root登录
+----------------+
1 row in set (0.041 sec)

MariaDB [(none)]> show databases;  查询数据库信息
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.105 sec)

MariaDB [(none)]> drop database test;  删除数据库test
Query OK, 0 rows affected (0.059 sec)

MariaDB [(none)]> exit
Bye
```

##### 运行安全脚本

```
[root@promote ~]# /usr/bin/mysql_secure_installation 

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y  是否设置登录密码
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y  是否禁止匿名登录
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n  是否禁止远程登录
 ... skipping.

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y  是否删除测试test数据库
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y  是否立即生效
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

### MySQL基础入门操作
#### 命令行交互式命令：mysql

`mysql`命令的选项：

-**u**USERNAME：用户名；默认为root

-**h**HOST：服务器主机；默认为localhost；客户端连接服务器，服务器会反解客户的IP为主机名，关闭此功能（skip_name_resolve=ON）

-**p**PASSWORD：用户的密码；建议使用-p, 默认为空密码

```
[root@promote ~]# mysql -uroot -hlocalhost -pcentos
Welcome to the MariaDB monitor.  Commands end with ; or \g.
```

注意：mysql的用户账号由两部分组成：'USERNAME'@'HOST'; 其中HOST用于限制此用户可通过哪些远程主机连接当前的mysql服务；

>HOST的表示方式，支持使用通配符：
>**%**：匹配任意长度的任意字符；
>如：172.16.%.%,  172.16.0.0/16
>**_**：匹配任意单个字符；

**-D**db_name：连接到服务器端之后，设定其处指明的数据库为默认数据库；

**-e** 'SQL COMMAND;'：连接至服务器并让其执行此命令后直接返回；

**-P**,--port=#：mysql服务器监听的端口；默认为3306/tcp；

**-S**,--socket=/PATH/TO/mysql.sock：套接字文件路径；
				
#### 客户端命令与服务端命令

##### 客户端命令：本地执行
**获取帮助**
>mysql> help

| 客户端命令 |          作用          |
| :--------: | :--------------------: |
|     \G     |        竖着显示        |
|     \g     |      语句结束标记      |
| \u db_name | 设定哪个库为默认数据库 |
|     \q     |          退出          |
|  \d CHAR   |   设定新的语句结束符   |
|    \\!     |     执行shell命令      |
|    \\.     |  装载并执行mysql脚本   |

```
MariaDB [mysql]> SELECT * FROM user\G;
*************************** 1. row ***************************
                  Host: localhost
                  User: root
              Password: *128977E278358FF80A246B5046F51043A2B1FCED
           Select_priv: Y
           Insert_priv: Y
           Update_priv: Y
           Delete_priv: Y
           Create_priv: Y
             Drop_priv: Y
··· ···
MariaDB [mysql]> \! ls /var
account  cache	db     games   kerberos  local	log   nis  preserve  spool  www
adm	 crash	empty  gopher  lib	 lock	mail  opt  run	     tmp    yp
```

##### 服务端命令

```
mysql> help contents
You asked for help about help category: "Contents"
For more information, type 'help <item>', where <item> is one of the following
categories:
   Account Management
   Administration
   Compound Statements
   Data Definition
   Data Manipulation
   Data Types
   Functions
   Functions and Modifiers for Use with GROUP BY
   Geographic Features
   Help Metadata
   Language Structure
   Plugins
   Procedures
   Storage Engines
   Table Maintenance
   Transactions
   User-Defined Functions
   Utility
```

通过mysql连接发往服务器执行并取回结果（SQL语句）

>DDL: Data Defination Language 数据定义语言，修改表结构
>　　CREATE（创建）, DROP（删除）, ALTER（修改表结构）
>DML: Data Manipulation Language 数据操作语言，修改表里的数据
>　　INSERT, DELETE, UPDATE（更新数据）
>DQL ：Data Query Language 数据的查询语言
>　　SELECT 用法多，非常灵活
>DCL ：Data Control Language 数据控制语言，授权限
>　　GRANT, REVOKE（取消授权）

**获取帮助**
>mysql> help contents
>mysql> help '命令类别'

**注意：每个语句必须有语句结束符，默认为分号(;)**

#### 数据库管理（DDL）
##### 查看数据库
格式：`SHOW {DATABASES | SCHEMAS} [LIKE 'pattern' | WHERE expr]`

>默认（安装自带）数据库：
>**mysql** -  用户权限相关数据
>**test** - 用于用户测试数据（刚才被删除）
>**information_schema** - MySQL本身架构相关数据

```
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.003 sec)
```

##### 创建数据库
格式：`CREATE  {DATABASE | SCHEMA}  [IF NOT EXISTS]  db_name;`
`[DEFAULT]  CHARACTER SET [=] charset_name`
`[DEFAULT]  COLLATE [=] collation_name`
						
查看支持的所有字符集：SHOW CHARACTER SET 
查看支持的所有排序规则：SHOW  COLLATION

```
MariaDB [(none)]> CREATE DATABASE hidb;
Query OK, 1 row affected (0.014 sec)

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| hidb               |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.058 sec)
```
**数据库就是/var/lib/mysql路径下的一个目录**

```
[root@promote ~]# ll /var/lib/mysql/
total 122936
-rw-rw----. 1 mysql mysql    16384 May 23 18:51 aria_log.00000001
-rw-rw----. 1 mysql mysql       52 May 23 18:51 aria_log_control
drwx------. 2 mysql mysql       20 May 24 09:55 hidb
```

##### 修改数据库
格式：`ALTER {DATABASE | SCHEMA}  [db_name]`
`[DEFAULT]  CHARACTER SET [=] charset_name`
`[DEFAULT]  COLLATE [=] collation_name`
						
##### 删除数据库
格式：`DROP {DATABASE | SCHEMA} [IF EXISTS] db_name`

##### 使用数据库
格式：`use db1`

```
MariaDB [(none)]> use hidb
Database changed
MariaDB [hidb]> 
```

#### MySQL表管理						
##### 基本数据类型
###### 字符型
- 定长字符型
  CHAR(#)；不区分字符大小写；#指定字节长度，最大不超过256
  BINARY(#)；区分字符大小写；#同上

- 变长字符型
   VARCHAR(#)；不区分字符大小写；#同上
   VARBINARY(#)；区分字符大小写；#同上

###### 数值型
- 精确数值型
  INT：整形数值
  INT的五个变种：TINYINT、SMALLINT、MEDIUMINT、INT、LONGINT

- 近似数值型（浮点型）
  FLOAT：单精度浮点型
  DOUBLE：双精度浮点型

###### 日期时间型：
- 日期型：DATE `YYYY-MM-DD（1000-01-01/9999-12-31）`
- 时间型：TIME `HH:MM:SS（'-838:59:59'/'838:59:59'）`
- 日期时间型：DATETIME `YYYY-MM-DD HH:MM:SS（1000-01-01 00:00:00/9999-12-31 23:59:59 Y）`
- 年份型：YEAR(2)、YEAR（4）
- 时间戳型：TIMESTAMP(从1970年开始计时所经过的秒数) `YYYYMMDD HHMMSS（1970-01-01 00:00:00/2037 年某时）`

###### 对象存储型：
- TEXT：不区分字符大小写
- BLOB：区分字符大小写

TEXT和BLOB还有以下变种：
TINYTEXT、SMALLTEXT、MEDIUMTEXT、TEXT、LONGTEXT
TINYBLOB、SMALLBLOB、MEDIUMBLOB、BLOB、LONGBLOB

###### 内置类型
- 集合型：SET
- 枚举型：ENUM
- 集合型，表示取值范围只能在给定的集合的**组合范围**内
- 枚举型，表示取值范围只能是给定的字符串中的一个

###### 数据类型的修饰符
- NOT NULL 非空
- NULL 允许为空
- AUTO_INCREMENT；自动增长
- DEFAULT value；默认值
- PRIMARY KEY；主键（意味着，唯一、非空）
- UNIQUE KEY；唯一键，可以为空
  		

##### 查看表
格式：`SHOW [FULL] TABLES [{FROM | IN} db_name] [LIKE 'pattern' | WHERE expr]`

查看表结构
格式：`DESC [db_name.]tb_name;`

查看某个表的字段属性
格式：`SHOW COLUMNS FROM table_name;`

```
MariaDB [mysql]> SHOW TABLES IN mysql;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| column_stats              |
| columns_priv              |
| db                        |
| event                     |
| func                      |
MariaDB [mysql]> SHOW COLUMNS FROM db; 
+-----------------------+---------------+------+-----+---------+-------+
| Field                 | Type          | Null | Key | Default | Extra |
+-----------------------+---------------+------+-----+---------+-------+
| Host                  | char(60)      | NO   | PRI |         |       |
| Db                    | char(64)      | NO   | PRI |         |       |
| User                  | char(80)      | NO   | PRI |         |       |
| Select_priv           | enum('N','Y') | NO   |     | N       |       |
| Insert_priv           | enum('N','Y') | NO   |     | N       |       |
| Update_priv           | enum('N','Y') | NO   |     | N       |       |
| Delete_priv           | enum('N','Y') | NO   |     | N       | 
```

##### 创建表
格式：`CREATE TABLE  [IF NOT EXISTS]  tbl_name  (create_defination)  [table_options]`

```
create table 表名(
    列名  类型  是否可以为空，
    列名  类型  是否可以为空
)ENGINE=InnoDB DEFAULT CHARSET=utf8
```

```
CREATE TABLE `tab1` (
  `nid` int(11) NOT NULL auto_increment,
  `name` varchar(255) DEFAULT zhangyanlin,
  `email` varchar(255),
  PRIMARY KEY (`nid`) 
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

注意：
（1）默认值，创建列时可以指定默认值，当插入数据时如果未主动设置，则自动添加默认值
（2）自增，如果为某列设置自增列，插入数据时无需设置此列，默认将自增（表中只能有一个自增列），注意：
　　① 对于自增列，必须是索引（含主键）
　　② 对于自增可以设置步长和起始值
（3）主键，一种特殊的唯一索引，不允许有空值，如果主键使用单个列，则它的值必须唯一，如果是多列，则其组合必须唯一。					

**create_defination**
>字段：col_name  data_type
>
>键：
>PRIMARY KEY (col1, col2, ...)
>UNIQUE KEY  (col1, col2,...)
>FOREIGN KEY (column)
>
>索引：
>KEY|INDEX  [index_name]  (col1, col2,...)

**table_options**

>ENGINE [=] engine_name

查看数据库支持的所有存储引擎类型：
`mysql> SHOW  ENGINES;`

查看某表的状态信息：
`mysql> SHOW  TABLES  STATUS  [LIKE  'tbl_name'][WHERE clause]`


**第二种创建方式：**
复制表结构：`CREATE TABLE tbl_name LIKE other_table_name;`
```
MariaDB [Market]> CREATE TABLE customers_copy LIKE customers_info;
```

**第三种创建方式：**
复制表数据：`CREATE TABLE tbl_name() SELECT clause`			
```
CREATE TABLE TBL8 SELECT HOST,USER,PASSWORD FROM MYSQL.USER;                        
CREATE TABLE tbl_name () select from 
```

```
MariaDB [(none)]> CREATE DATABASE testdb;  创建数据库testdb
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> use testdb;  进入数据库testdb
Database changed
MariaDB [testdb]> CREATE TABLE testdb.students (id int UNSIGNED NOT NULL PRIMARY KEY,name VARCHAR (20) NOT NULL,age tinyint UNSIGNED);  
创建testdb库中名为students表；
表有3列：id、name、age；id：
数据类型int为正、不为空、设为主键；
name：数据类型VARCHAR (20)、不为空；
age：数据类型tinyint UNSIGNED)

Query OK, 0 rows affected (0.105 sec)

MariaDB [testdb]> show tables;  查看库中的表
+------------------+
| Tables_in_testdb |
+------------------+
| students         |
+------------------+
1 row in set (0.001 sec)

MariaDB [testdb]> desc students;  查看表中内容
+-------+---------------------+------+-----+---------+-------+
| Field | Type                | Null | Key | Default | Extra |
+-------+---------------------+------+-----+---------+-------+
| id    | int(10) unsigned    | NO   | PRI | NULL    |       |
| name  | varchar(20)         | NO   |     | NULL    |       |
| age   | tinyint(3) unsigned | YES  |     | NULL    |       |
+-------+---------------------+------+-----+---------+-------+
3 rows in set (0.061 sec)

MariaDB [testdb]> show create table students;  查看创建这个表的命令
+----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table    | Create Table                                                                                                                                                                                     |
+----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| students | CREATE TABLE `students` (
  `id` int(10) unsigned NOT NULL,
  `name` varchar(20) NOT NULL,
  `age` tinyint(3) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1 |
+----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.001 sec)

MariaDB [testdb]> create table students3 select *  from students;  创建students3复制students的内容
Query OK, 0 rows affected (0.064 sec)
Records: 0  Duplicates: 0  Warnings: 0
MariaDB [testdb]> drop table students;  删除表students
Query OK, 0 rows affected (0.108 sec)

MariaDB [testdb]> show tables;
+------------------+
| Tables_in_testdb |
+------------------+
| students3        |
+------------------+
1 row in set (0.003 sec)
```


##### 修改表
格式：`ALTER [ONLINE | OFFLINE] [IGNORE] TABLE tbl_name  [alter_specification [, alter_specification] ...]`
					
**alter_specification**
>**字段**
>添加：`ADD  [COLUMN]  col_name  data_type  [FIRST | AFTER col_name ]`

>删除：`DROP  [COLUMN] col_name `

>修改：`CHANGE [COLUMN] old_col_name new_col_name column_definition  [FIRST|AFTER col_name]	
>MODIFY [COLUMN] col_name column_definition  [FIRST | AFTER col_name]`

>**键**

>添加：`ADD  {PRIMARY|UNIQUE|FOREIGN}  KEY (col1, col2,...)`

>删除：
>主键：`DROP PRIMARY KEY`
>外键：`DROP FOREIGN KEY fk_symbol`

>**索引**

>添加：`ADD {INDEX|KEY} [index_name]  (col1, col2,...)`

>删除：`DROP {INDEX|KEY}  index_name`

>**表选项**
>`ENGINE [=] engine_name`

```
#在students表添加索引name和id
MariaDB [hidb]> ALTER TABLE students ADD INDEX(name,id);

#删除students表的Class字段
MariaDB [hidb]> ALTER TABLE students DROP Class;

#修改students表中的id字段为无符号的非空整型字符
MariaDB [hidb]> ALTER TABLE students MODIFY id int(10) UNSIGNED NOT NULL;
```

**示例**

```
ALTER TABLE s1** MODIFY **phone int; 把phone的数据类型改为int

ALTER TABLE s1** CHANGE COLUMN** phone mobile char(11); 把字段phone改名为字段mobile，数据类型为char(11)

ALTER TABLE s1 **DROP COLUMN** mobile; 删除字段mobile

ALTER TABLE students **ADD **gender ENUM('m','f') 增加gender字段，为枚举类型，只能是m或f

ALETR TABLE students **CHANGE** id sid int UNSIGNED NOT NULL PRIMARY KEY; 把修改id字段为sid，数据类型为正int、非空、主键

ALTER TABLE students **ADD** UNIQUE KEY(name); 在name字段加唯一键

ALTER TABLE students** ADD INDEX**(age); 在age字段加索引

DESC students; 查看这个表

```

##### 删除表
格式：`DROP  TABLE  [IF EXISTS]   tbl_name [, tbl_name] ...`
					


##### 索引管理：
索引是特殊的数据结构，要有索引名称
			
创建：`CREATE  [UNIQUE|FULLTEXT|SPATIAL] INDEX  index_name  [BTREE|HASH]  ON tbl_name (col1, col2,,...)`
				
删除：`DROP  INDEX index_name ON tbl_name`

示例：在grade表中创建一个索引，由id+name字段组成

```
MariaDB [hidb]> CREATE INDEX suoyin ON grade(id,name);
Query OK, 0 rows affected (0.68 sec)
Records: 0  Duplicates: 0  Warnings: 0
```


查看：`SHOW {INDEX | INDEXES | KEYS} {FROM | IN} tbl_name [{FROM | IN} db_name] [WHERE expr]`

```
MariaDB [mysql]> SHOW INDEX FROM db;
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| db    |          0 | PRIMARY  |            1 | Host        | A         |        NULL |     NULL | NULL   |      | BTREE      |         |               |
| db    |          0 | PRIMARY  |            2 | Db          | A         |        NULL |     NULL | NULL   |      | BTREE      |         |               |
| db    |          0 | PRIMARY  |            3 | User        | A         |           0 |     NULL | NULL   |      | BTREE      |         |               |
| db    |          1 | User     |            1 | User        | A         |           0 |     NULL | NULL   |      | BTREE      |         |               |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
4 rows in set (0.002 sec)
```

#### DML语句

##### INSERT 

格式：`INSERT  [INTO]  tbl_name  [(col1,...)]  {VALUES|VALUE}  (val1, ...),(...),...`

① tbl_name后不加(),默认按表结构的列；若加()，前后()内容要对应，顺序可以不按表结构，也可以设null值，但最好不要，
② 选项不是数字，都要加''，例：name='ljw'


```
MariaDB [testdb]>INSERT INTO students(id,name,age) VALUES(1,'ljw',18); 
Query OK, 1 row affected (0.078 sec)
MariaDB [testdb]> INSERT INTO students VALUES(2,'xiaoming',19),(3,'xiaohong',20);
Query OK, 2 rows affected (0.065 sec)
Records: 2  Duplicates: 0  Warnings: 0
MariaDB [testdb]> SELECT * FROM students;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  2 | xiaoming |   19 |
|  3 | xiaohong |   20 |
+----+----------+------+
3 rows in set (0.001 sec)
```

##### UPDATE
格式：UPDATE tbl_name SET col1=val1, col2=val2, ... [WHERE clause] [ORDER BY 'col_name' [DESC]] [LIMIT [m,]n]; 

注意：若不加where，直接把所有列都修改了，Limit m,n 跳过m行，要n行

```
MariaDB [testdb]> INSERT s3 SELECT * FROM students;
Query OK, 3 rows affected (0.004 sec)
Records: 3  Duplicates: 0  Warnings: 0
MariaDB [testdb]> UPDATE s3 SET name='xiaohei',age=30 WHERE id=2;
Query OK, 1 row affected (0.063 sec)
Rows matched: 1  Changed: 1  Warnings: 0
MariaDB [testdb]> SELECT * FROM s3;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  2 | xiaohei  |   30 |
|  3 | xiaohong |   20 |
+----+----------+------+
3 rows in set (0.001 sec)
```

##### DELETE
格式：`DELETE FROM tbl_name [WHERE clause] [ORDER BY 'col_name' [DESC]];`可先排序再指定删除的行数

注意：若不加where，直接把所有列都删除了

`TRUNCATE TABLE tbl_name;` 清空表，快速清空，删除的时候，不计日志，谨慎使用

```
MariaDB [testdb]> SELECT * FROM s3;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  2 | xiaohei  |   30 |
|  3 | xiaohong |   20 |
+----+----------+------+
3 rows in set (0.001 sec)

MariaDB [testdb]> DELETE FROM s3 WHERE id=3;
Query OK, 1 row affected (0.003 sec)

MariaDB [testdb]> SELECT * FROM s3;
+----+---------+------+
| id | name    | age  |
+----+---------+------+
|  1 | ljw     |   18 |
|  2 | xiaohei |   30 |
+----+---------+------+
2 rows in set (0.001 sec)
```


##### DQL（SELECT）				
格式：`SELECT col1,col2,... FROM tbl_name [WHERE clause] [ORDER BY 'col_name' [DESC]] [LIMIT [m,]n]; `

示例：

`select id,name,... from tab_name;` 查出指定的表中内容

`select count(*) from tab_name; `查看表中的记录数量，count()是自带的函数


**1、字段表示法**
*: 所有字段
as ：字段别名，若事先做好了表，想要把列的英语改成中文，不用修改，可以直接用别名

```
MariaDB [testdb]> SELECT * FROM students;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  2 | xiaoming |   19 |
|  3 | xiaohong |   20 |
+----+----------+------+
3 rows in set (0.055 sec)

MariaDB [testdb]> SELECT id AS 学生编号,name 姓名,age 年龄 FROM students; 
+--------------+----------+--------+
| 学生编号     | 姓名     | 年龄   |
+--------------+----------+--------+
|            1 | ljw      |     18 |
|            2 | xiaoming |     19 |
|            3 | xiaohong |     20 |
+--------------+----------+--------+
3 rows in set (0.001 sec)
```

**2、排序：ORDER BY 'col_name' [DESC]**
by后指定列，desc反向排序，反向也可以-col_name

注意：若其中有空值，正向排序空值在第一行，反向排序空值在最后一行，可以order by -col_name desc 即正向排序，有把空值放在最后一行

```
MariaDB [testdb]> SELECT * FROM students ORDER BY -age DESC;  按年龄的列正向排序，null在最后
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  2 | xiaoming |   19 |
|  3 | xiaohong |   20 |
|  4 | lijiawen |   22 |
|  5 | wcl      |   23 |
|  6 | ljwn     | NULL |
+----+----------+------+
6 rows in set (0.001 sec)
```

**3、WHERE clause：where 后的选项**
操作符：>, <, >=, <=, ==, !=

```
MariaDB [testdb]> SELECT * FROM students WHERE age >=20;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  3 | xiaohong |   20 |
|  4 | lijiawen |   22 |
|  5 | wcl      |   23 |
+----+----------+------+
3 rows in set (0.040 sec)
```

BETWEEN ... AND ...

```
MariaDB [testdb]> SELECT * FROM students WHERE age BETWEEN 18 AND 20;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  2 | xiaoming |   19 |
|  3 | xiaohong |   20 |
+----+----------+------+
3 rows in set (0.042 sec)
```

LIKE：模糊匹配
- % ：任意长度的任意字符
- _ ：任意单个字符；

```
MariaDB [testdb]> SELECT * FROM students WHERE name LIKE 'xiao%';
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  2 | xiaoming |   19 |
|  3 | xiaohong |   20 |
+----+----------+------+
2 rows in set (0.001 sec)
```

RLIKE ：正则表达式模式匹配

```
MariaDB [testdb]> SELECT * FROM students WHERE name RLIKE 'ng$';
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  2 | xiaoming |   19 |
|  3 | xiaohong |   20 |
+----+----------+------+
2 rows in set (0.001 sec)
```

IS NULL ，IS NOT NULL 寻找空值，不能用=，只能用is，所以最好不要有null，不好管理

```
MariaDB [testdb]> SELECT * FROM students WHERE age is null;
+----+------+------+
| id | name | age  |
+----+------+------+
|  6 | ljwn | NULL |
+----+------+------+
1 row in set (0.001 sec)
```

IN （val1,val2,…） 离散值显示

```
MariaDB [testdb]> SELECT * FROM students WHERE age IN(18,20,22);
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  3 | xiaohong |   20 |
|  4 | lijiawen |   22 |
+----+----------+------+
3 rows in set (0.002 sec)
```

条件逻辑操作：and ，or ，not

```
MariaDB [testdb]> SELECT * FROM students WHERE age not IN(18,20,22);
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  2 | xiaoming |   19 |
|  5 | wcl      |   23 |
+----+----------+------+
2 rows in set (0.002 sec)
```

**4、LIMIT限制**

SELECT * FROM 表 LIMIT 5;            - 前5行

SELECT * FROM 表  LIMIT 4,5;          - 从第4行开始的5行

SELECT * FROM 表  LIMIT 5 OFFSET 4    - 从第4行开始的5行

```
MariaDB [testdb]> SELECT * FROM students;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  2 | xiaoming |   19 |
|  3 | xiaohong |   20 |
|  4 | lijiawen |   22 |
|  5 | wcl      |   23 |
|  6 | ljwn     | NULL |
+----+----------+------+
6 rows in set (0.001 sec)

MariaDB [testdb]> SELECT * FROM students LIMIT 3;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | ljw      |   18 |
|  2 | xiaoming |   19 |
|  3 | xiaohong |   20 |
+----+----------+------+
3 rows in set (0.003 sec)

MariaDB [testdb]> SELECT * FROM students LIMIT 2,3;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  3 | xiaohong |   20 |
|  4 | lijiawen |   22 |
|  5 | wcl      |   23 |
+----+----------+------+
3 rows in set (0.001 sec)

MariaDB [testdb]> SELECT * FROM students LIMIT 3 OFFSET 2;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  3 | xiaohong |   20 |
|  4 | lijiawen |   22 |
|  5 | wcl      |   23 |
+----+----------+------+
3 rows in set (0.001 sec)
```


#### DCL（用户账号及权限管理）							
##### 用户账号
**用户账号**：'user'@'host'
user: 用户名
host: 允许用户通过哪些主机远程连接mysqld 服务
表示方式：IP 、网络地址、主机名、通配符(% 和_)

禁止检查主机名：my.cnf
[mysqld]
skip_name_resolve = ON

**创建用户**：`CREATE USER **'username'@'host' [**IDENTIFIED BY 'password'];`

例：`CREATE user 'ljw1'@'192.168.0.%' identified by 'centos'; `添加ljw1账号在192.168.0这个网段，可以输centos密码连接

**查看当前等登录的用户**：`SELECT user();`

**查看已经添加的用户**：`SELECT User,Host,Password FROM mysql.user;`

**删除用户**：`DROP USER 'username'@'host';`

示例：`drop user'ljw'@'192.168.0.106';`

**更改口令**：多用第一种

① SET PASSWORD FOR\'user'@'host' = PASSWORD('password');
分析：password();是调用了自带的函数
例：set password for 'along'@'192.168.30.%'=password('along');

② UPDATE user SET password=PASSWORD('magedu') WHERE User='root';

注意：相当于改了user的表，不推荐用，修改表的命令不会马上生效，需执行**FLUSH PRIVILEGES **刷新一下生效

③ /usr/local/mysql/bin/mysqladmin -u root –poldpassword password 'newpassword'

注意：仅创建的用户，其所拥有的权限很小，所以我们要进行授权

##### 授权，回收权限

###### 授权
授权并创建账号：`GRANT priv_type,... ON [object_type] db_name.tb_name TO 'user'@'host' [IDENTIFIED BY 'password'] [WITHGRANT OPTION]; `

>① priv_type: ALL [PRIVILEGES] 授权类型：
>insert增，delete删 ， update改，select查，all所有权限

>② db_name.tb_name: 对哪个数据库的哪个表授权：
>*.*: 所有库的所表
>db_name.*: 指定库的所有表
>db_name.tb_name: 指定库的指定表
>db_name.routine_name ：指定库的存储过程和函数

例：`GRANT ALL ON test.* to 'ljw'@'%' identified by 'centos'; `创建ljw用户，允许其在所有主机通过centos密码登录，对test库的所有表有所有权限

**查看指定用户所获得的授权：**
`SHOW GRANTS FOR  'user'@'host'	`					
`SHOW GRANTS FOR CURRENT_USER;`

###### 回收授权：
`REVOKE priv_type, ... ON db_name.tb_name FROM 'user'@'host`

例：`REVOKE DELETE ON test .* from 'ljw'@'%'; `回收ljw@'%'用户对test库的所有表的删除权限

注意：
① MariaDB 服务进程启动时会读取mysql 库中所有授权表至内存
② GRANT 或REVOKE 等执行权限操作会保存于系统表中，MariaDB 的服务进程通常会自动重读授权表，使之生效
③ 对于不能够或不能及时重读授权表的命令，可手动让MariaDB 的服务进程重读授权表：
`mysql> FLUSH PRIVILEGES;`


				
