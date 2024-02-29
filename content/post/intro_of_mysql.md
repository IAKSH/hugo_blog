---
title: "Intro_of_mysql"
date: 2024-02-29T14:45:55Z
draft: true
summary: "未完"
---

# 0x00 | 总述
MySQL是曾经比较流行的中小型数据库解决方案，和其他数据库管理系统一样，使用SQL语言以及部分自身扩展。

MySQL官网提供了部分Linux发行版上的软件包，不过基本上都是些老旧的发行版了。Linux世界正在逐渐抛弃MySQL，转而拥抱由社区主导的MariaDB项目。

本文以`Debian 12 (bookworm)`和`Ubuntu 22.04 (jimmy)`为例，带你快速入门以MySQL/MariaDB为例的RMDBS。

实际上需要学的内容也基本就是如何使用命令行管理各数据库，表，索引，以及用户。这些实际上应该也有第三方GUI可以一键化的吧？至于具体的，如何在程序中连接并与数据库交互，就得另外去看关于SQL，JDBC或者其他语言的对应Connector了。

由于通过软件源安装的MySQL/MariaDB通常都会由另一个新的用户运行，关于MySQL/MariaDB的相关操作都需要root权限。

至于SQL语言，似乎是大小写无关的，此处为了与其他文章统一，除变量名外全部使用了大写。实际上为了阅读和输入方便，我通常使用小写。

# 0x01 | 安装
在`Ubuntu 22.04 (jimmy)`中，依然保留了MySQL相关组件的软件包，安装`mysql-server`包即可。

在`Debian 12 (bookworm)`中，软件源已不再保留MySQL的软件包，应当使用MariaDB的`mariadb-server`替代，不用担心，MariaDB几乎完全兼容MySQL API。

# 0x02 | 使用systemd启停与监视服务器
MySQL和MariaDB通常都会在后台驻留一个守护进程，分别是`/usr/sbin/mysqld`和`usr/sbin/mariadbd`。

很明显，你可以通过`systemctl`工具管理它们。（对于使用upstart的系统，应当使用`service`系列指令）

```sh
# 对于systemd
systemctl {start|stop|restart|status} mysqld
# 对于upstart
service mysqld {start|stop|restart|status}
```

除此之外，MySQL也提供了如`mysqladmin`的管理工具，不过我认为这是一种老式的，仅仅为了向后兼容而继续存在的管理方式。不过也许能用来实现一些炫酷的操作，比如rootless？

# 0x03 | 登录和修改密码
MySQL/MariaDB也存在类似Linux DAC的安全机制，两者默认都存在一个`root@localhost`账户，此账户拥有所有权限，在root权限下使用`mysql`或`mariadb`指令即可登录。这个账户默认是空密码，下面是其中一种修改方式（下同）：
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'your_password';
```
对于有密码的账户，需要使用`-u`指定用户，再用`-p`指定需要输入密码，如下：
```sh
mysql -u root -p
# 对于MariaDB
mariadb -u root -p
```
除此之外，还可以新建一个用户：
```sql
CREATE USER user_name IDENTIFIED BY 'user_password';
```
随后还需要为其设置权限：
```sql
--将数据库certain_db的所有权限给予用户certain_user
GRANT ALL PRIVILEGES ON certain_db.* TO certain_user@'%' IDENTIFIED BY 'user_password';
--应用权限修改
FLUSH PRIVILEGES;
-- 可以使用如下SQL语句查询某用户的权限
SHOW GRANTS FOR 'certain_user';
```
如果需要删除用户，可以使用如下SQL语句：
```sql
DROP USER certain_user@'%';
```
上述的对用户的修改实际上是对`mysql.user`表的修改，实际上，你也可以直接操纵该表的内容以设置账户。

# 0x04 | 数据库的结构
MySQL/MariaDB都属于RMDBS，整个数据库管理系统中有若干个数据库，每个数据库中有若干个表，每个表就类似于Excel中的样式。

database system
	├ database 1
	├ ...
	└ database n
		├ table 1
		├ ...
		└  table n
			├ ...

常用的关于数据库结构的SQL语句如下：
```sql
--列出所有数据库
SHOW DATABASES;

--创建新的数据库
CREATE DATABASE certain_database;

--设置当前操作的数据库
USE certain_db;

--列出当前数据库中所有的表
SHOW TABLES;

--创建新的表
--需要注意的是，此处表名和字段名不能用引号(')，应当用反单引号(`)
CREATE TABLE certain_table (column_name column_type,...);

--列出表的结构信息
SHOW COLUMNS FROM certain_table;
```

除此之外，数据库中实际上还存在索引等。

# 0x05 | 表数据的增删查改
这部分实际上应该是SQL语法的内容。
1. 为表插入一条记录 `INSERT INTO certain_table (column1,column2,...) VALUES (var1,var2,...);`需要注意的是，前一个参数表并不需要完全和表的结构一致，它只是个参数表。
2. 为表删除一条记录`DELETE FROM certain_table WHERE ...`
3. 查询并输出一条记录`SELECT column1,column2,... FROM certain_table`，可以使用通配符(`*`)，后可接WHERE ...
4. 修改（更新）某条记录的某个键值`UPDATE certain_table SET something=114,something2=514,... where ...`

# 0x06 | 表结构的变换
主要就是用`ALTER`语句（alternate?）
TBC...

# 0x07 | 表的复制

# 0x08 | 表的索引

# 0x09 | 临时表

# 0x0A | 事务

# 0x0B | 导入和导出数据

---
参考：[MySQL 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/mysql/mysql-tutorial.html)