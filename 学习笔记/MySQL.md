李玉婷P1-P178 夏磊:P327---P350, 再看周阳：P179---P242

https://blog.csdn.net/weixin_45260385/article/details/113881457

## 数据库的相关概念

DB

数据库（database）：存储数据的“仓库”。它保存了一系列有组织的数据。

数据库是一个以某种有组织的方式存储的数据集合。

理解数据库的一种最简单的办法是将其想象为一个文件柜。

此文件柜是一个存放数据的物理位置，不管数据是什么以及如何组织的。

DBMS

数据库管理系统（Database Management System）。

数据库是通过 DBMS 创建和操作的容器

常见的数据库管理系统：MySQL、Oracle、DB2、SqlServer等。

人们通常用数据库这个术语来代表他们使用的数据库软件。这是不正确的，它是引起混淆的根源。

确切地说，数据库软件应称为DBMS（数据库管理系统）。

数据库是通过 DBMS 创建和操纵的容器。

数据库可以是保存在硬设备上的文件，但也可以不是。

在很大程度上说，数据库究竟是文件还是别的什么东西并不重要，因为你并不直接访问数据库；

你使用的是 DBMS ，它替你访问数据库。

SQL

结构化查询语言（Structure Query Language）：专门用来与数据库通信的语言。

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680153183856-e78f13a5-25b1-49ac-a050-d478164c6daa.png" alt="img" style="zoom: 50%;" />

SQL的优点：

①简单易学；

②不是某个特定数据库供应商专有的语言，几乎所有DBMS都支持SQL；

③虽然简单，但实际上是一种强有力的语言，灵活使用其语言元素，可以进行非常复杂和高级的数据库操作。

## 数据库存储数据的特点

1、将数据放到表中，表再放到库中

2、**一个数据库中可以有多个表，每个表都有一个的名字，用来标识自己。表名具有唯一性。**

**3、表具有一些特性，这些特性定义了数据在表中如何存储，类似“类”的设计。**

**4、表由列组成，我们也称为字段。所有表都是由一个或多个列组成的，每一列类似”属性”。**

**5、表中的数据是按行存储的，每一行类似于“对象”。**

一个数据库系统中可以包含很多数据库，每个数据库中又包含很多表，表的一列称为一个字段，一行称为一条记录。

类 —— 表

类中属性 —— 表中字段

对象 —— 记录

## 术语

在你将资料放入自己的文件柜时，并不是随便将它们扔进某个抽屉就完事了，而是在文件柜中创建文件，然后将相关的资料放入特定的文件中。

在数据库领域中，这种文件称为表。

表是一种结构化的文件，可用来存储某种特定类型的数据。

表可以保存顾客清单、产品目录，或者其他信息清单。

**表（table）是某种特定类型数据的结构化清单。**

**这里关键的一点在于，存储在表中的数据是一种类型的数据或一个清单。**

**决不应该将顾客的清单与订单的清单存储在同一个数据库表中。**

这样做将使以后的检索和访问很困难。

应该创建两个表，每个清单一个表。

**数据库中的每个表都有一个名字，用来标识自己。**

**此名字是唯一的，这表示数据库中没有其他表具有相同的名字。**

**表具有一些特性，这些特性定义了数据在表中如何存储，如可以存储什么样的数据，数据如何分解，各部分信息如何命名，等等。**

**描述表的这组信息就是所谓的模式，模式可以用来描述数据库中特定的表以及整个数据库（和其中表的关系）。**

**模式（schema） 是关于数据库和表的布局及特性的信息。**

表由列组成。列中存储着表中某部分的信息。

**列（column） 是表中的一个字段。所有表都是由一个或多个列组成的。**

理解列的最好办法是将数据库表想象为一个网格。

**网格中每一列存储着一条特定的信息。**

例如，在顾客表中，一个列存储着顾客编号，另一个列存储着顾客名，而地址、城市、以及邮政编码全都存储在各自的列中。

数据库中每个列都有相应的数据类型。数据类型定义列可以存储的数据种类。

例如，如果列中存储的为数字（或许是订单中的物品数），则相应的数据类型应该为数值类型。

如果列中存储的是日期、文本、注释、金额等，则应该用恰当的数据类型规定出来。

**数据类型（datatype） 是所容许的数据的类型。每个表列都有相应的数据类型，它限制（或容许）该列中存储的数据。**

**字段（field） 基本上与列（ column） 的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常用在计算字段的连接上。**

**行（row） 是表中的一个记录。**

你可能听到用户在提到行（ row）时称其为数据库记录（ record）。

在很大程度上，这两个术语是可以互相替代的，但从技术上说，行才是正确的术语。

表中每一行都应该有可以唯一标识自己的一列（或一组列）。

一个顾客表可以使用顾客编号列，而订单表可以使用订单ID，雇员表可以使用雇员ID或雇员社会保险号。

**唯一标识表中每行的这个列（或这组列）称为主键，用来表示一个特定的行。**

**主键（primary key） 是一列（或一组列），其值能够唯一区分表中每个行。**

没有主键，更新或删除表中特定行很困难，因为没有安全的方法保证只涉及相关的行。

**大多数数据库设计人员都应保证他们创建的每个表具有一个主键，以便于以后的数据操纵和管理。**

**表中的任何列都可以作为主键，只要它满足以下条件：**

- **任意两行都不具有相同的主键值；**
- **每行都必须具有一个主键值（主键列不允许NULL值）。**

**这里列出的主键值规则是 MySQL 本身强制实施的。**

主键通常定义在表的一列上，但这并不是必需的，也可以一起使用多个列作为主键。

在使用多列作为主键时，上述条件必须应用到构成主键的所有列，所有列值的组合必须是唯一的（但单个列的值可以不唯一）。

**除 MySQL 强制实施的规则外，应该坚持的几个普遍认可的最好习惯为：**

- **不更新主键列中的值；**
- **不重用主键列的值；**
- **不在主键列中使用可能会更改的值。（例如，如果使用一个名字作为主键以标识某个供应商，当该供应商合并和更改其名字时，必须更改这个主键）**

**外键（foreign key）是某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。**

理解关系表的最好方法是来看一个现实世界中的例子。

假如有一个包含产品目录的数据库表，其中每种类别的物品占一行。

对于每种物品要存储的信息包括产品描述和价格，以及生产该产品的供应商信息。

现在，假如有由同一供应商生产的多种物品，那么在何处存储供应商信息（如，供应商名、地址、联系方法等）呢？

将这些数据与产品信息分开存储的理由如下：

- 因为同一供应商生产的每个产品的供应商信息都是相同的，对每个产品重复此信息既浪费时间又浪费存储空间。
- 如果供应商信息改变（例如，供应商搬家或电话号码变动），只需改动一次即可。
- 如果有重复数据（即每种产品都存储供应商信息），很难保证每次输入该数据的方式都相同。不一致的数据在报表中很难利用。

关键是，相同数据出现多次决不是一件好事，此因素是关系数据库设计的基础。

**各表通过某些常用的值（即关系设计中的关系（ relational） ）互相关联。**

**在这个例子中，可建立两个表，一个存储供应商信息，另一个存储产品信息。**

**vendors 表包含所有供应商信息，每个供应商占一行，每个供应商具有唯一的标识，此标识称为主键（ primary key ），可以是供应商 ID 或任何其他唯一值。**

**products 表只存储产品信息，它除了存储供应商 ID（ vendors 表的主键）外不存储其他供应商信息。** 

**vendors 表的主键又叫作 products 的外键，它将 vendors 表与 products 表关联，利用供应商 ID 能从 vendors 表中找出相应供应商的详细信息。**

products 表

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697700480250-68ee3d95-9cfe-4cf7-a230-2540d4d054ff.png" alt="Snipaste_2023-10-19_15-27-29"  />

vendors 表

![Snipaste_2023-10-19_15-29-39](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697700588220-06f8117e-e913-47ef-96cf-98d6d855a313.png)

这样做的好处如下：

- 供应商信息不重复，从而不浪费时间和空间；
- 如果供应商信息变动，可以只更新 vendors 表中的单个记录，相关表中的数据不用改动；
- 由于数据无重复，显然数据是一致的，这使得处理数据更简单

**使用关系表时，在关系列中插入合法的数据非常重要。**

**回到这里的例子，如果在 products 表中插入拥有非法供应商 ID（即没有在 vendors 表中出现）的供应商生产的产品，则这些产品是不可访问的，**

**因为它们没有关联到某个供应商。**

**为防止这种情况发生，可指示 MySQL 只允许在 products 表的供应商 ID 列中出现合法值（即出现在 vendors 表中的供应商）。**

**这就是维护引用完整性，它是通过在表的定义中指定主键和外键来实现的。**



**外键还可以设置级联方式，也就是主表修改 id 或者删除的时候，从表怎么做。**

**有 3 种级联方式：**

**CASCADE（关联删除或更新），SET NULL（关联外键设置为 null），RESTRICT 或者 NO ACTION（没有从表的关联记录才可以删除或更新）。**

**外键用于表示关系：**

**一对一（设计外键约束后使用连表查询）、**

**一对多（和一对一没本质的区别，插入数据时自动表示关联，如多个员工有一个相同的部门 id）、**

**多对多（需要建立一个中间表来保存其它两张表的外键，同时指定这些外键为中间表的 primary key，也就是复合（组合）主键。并且级联方式选择 CASCADE）**

**关系表的设计就是要保证把信息分解成多个表，一类数据一个表。**



**什么是自动增量？** 

**某些表列需要唯一值。例如，订单编号、雇员ID或（如上面例子中所示的）顾客ID。**

**在每个行添加到表中时， MySQL可以自动地为每个行分配下一个可用编号，不用在添加一行时手动分配唯一值（这样做必须记住最后一次使用的值）。**

**这个功能就是所谓的自动增量。**

如果需要它，则必须在用 CREATE 语句创建表时把它作为表定义的组成部分。



**子句（clause）**

**子句（clause） SQL语句由子句构成，有些子句是必需的，而有的是可选的。**

**一个子句通常由一个关键字和所提供的数据组成。子句的例子有 SELECT 语句的 FROM 子句**

## 服务启动/暂停

以管理员身份运行终端，执行命令

```sh
net start MySQL80
net stop MySQL80
```

密码 123456

或

```sh
mysqld --datadir="C:\ProgramData\MySQL\MySQL Server 8.0\Data" --console
或
mysqld --datadir="C:\ProgramData\MySQL\MySQL Server 8.0\Data"
```

ctrl + c 暂停并退出

可以在 windows 服务配置为手动启动，而不是开机自启动

查看连接端口

```sh
show global variables like 'port';
```

## MySQL 服务端的登录和退出

```sh
mysql–h 主机名–u用户名–p密码

mysql -hlocalhost -uroot -p123456
或
mysql -hlocalhost -uroot -p
```

断开客户端与服务器的连接并且关闭客户端的话，可以在mysql>提示符后输入下边任意一个命令：

```sh
quit
exit
\q
```

新建数据库时，字符集选择 utf8mb4 ，排序规则选择 utf8mb4_unicode_ci

mysql 、information_schema 、performance_scheme 、sys 是 MySQL 内部使用的数据库

## windows 查看端口

```sh
netstat -ano|findstr 3306
```

结束 pid 为 3306 的进程

```sh
taskkill /pid 3306 /f
```

## MySQL 图形管理软件快捷键

**1. 主窗口**

```
 Ctrl+F: 输入关键字查询表

 Ctrl+Q: 打开查询

 Ctrl+H: 查看执行历史

 Ctrl+G: 打开之前查询保存的sql位置
```

**2. 打开具体某一个表后**

```
 Ctrl+Q: 打开这个表的查询

 Ctrl+D:打开表的设计表

 Ctrl+F: 输入关键字查询表中数据

 Ctrl+左/右箭头:可到行首，行尾

 Ctrl+左/右箭头: 可到列首，行尾
```

**3. 打开表设计后**

```
  Ctrl+Q: 打开查询

  Ctrl+S: 保存

  Ctrl+C: 复制列

  Ctrl+V: 将复制的列粘贴到尾处

  Ctrl+O: 打开表
```

**4. 打开查询器后**

```
  Ctrl+R: 执行sql语句

  Ctrl+Shift+r: 有多个sql语句时，执行选中的sql

  Ctrl+/: 注释sql语句

  Ctrl+Shift+/: 取消sql语句注释

  Ctrl+S: 保存执行的sql语句，再次打开数据库时，左侧列表，查询处可查看到

  Ctrl+N:打开一新的查询

  Ctrl+T: 停止执行sql语句

  Ctrl+O: 载入sql文件，导入

  Ctrl+D : 复制光标所在的行的sql语句

  Ctrl+L: 删除光标所在行的sql语句
```

## MySQL 语法规范

**SQL 语句是由简单的英语单词构成的。这些单词称为关键字，每个SQL语句都是由一个或多个关键字构成的。**

**1、不要用关键字命名一个表或列。**

**2、命令不区分大小写**

按照习俗，这些**命令、函数什么的都是要大写的，**

**而一些名称类的东西，比如数据库名，表名、列名啥的都是要小写的。**

许多 SQL 开发人员喜欢对所有 SQL 关键字使用大写，而对所有列和表名使用小写，这样做使代码更易于阅读和调试。

**3、多条 SQL 语句必须以分号（；）分隔。**

 MySQ L如同多数 DBMS 一样，不需要在单条 SQL 语句后加分号。

但特定的DBMS可能必须在单条SQL语句后加上分号。

即使不一定需要，但加上分号肯定没有坏处。如果你使用的是 mysql 命令行，必须加上分号来结束 SQL 语句

4、使用空格 

在处理 SQL 语句时，其中所有空格都被忽略。 

SQL 语句可以在一行上给出，也可以分成许多行。

多数SQL开发人员认为将 SQL 语句分成多行更容易阅读和调试。

**一个命令结束方式，按回车键，提交命令（建议以分号结尾）**

```sh
mysql> SELECT NOW();
mysql> SELECT NOW()\g
mysql> SELECT NOW()\G
```

可回车换行

```sh
mysql> SELECT
    -> NOW()
    -> ;
```

多个命令

```sh
mysql> SELECT NOW(); SELECT NOW(); SELECT NOW();
```

使用 \c 放弃本次操作

```ah
mysql> SELECT NOW()\c
```

注释：

单行注释：#注释文字

单行注释：-- 注释文字

多行注释：/* 注释文字 */

**字符串的表示**

在命令里有时会使用到字符串，我们可以使用单引号''或者双引号""把字符串内容引起来

**建议最好使用单引号来表示字符串**

```sh
mysql> SELECT 'aaa';
```

这个语句只是简单的把字符串'aaa'又输出来了而已。

但是一定要在字符串内容上加上引号，不然的话 MySQL 服务器会把它当作列名，

比如这样就会返回一个错误：

```sh
mysql> SELECT aaa;
ERROR 1054 (42S22): Unknown column 'aaa' in 'field list'
mysql>
```

**字段的表示**

`` 表示字段名，用于区分命令关键字

## SQL语言的分类

DQL（Data Query Language）：数据查询语言，用于检索数据库中的数据，主要是SELECT语句；

DML（Data Manipulation Language)：数据操纵语言，用于改变数据库中的数据，主要包括INSERT、UPDATE和DELETE语句；

DDL（Data Definition Language)：数据定义语言，用于库和表的创建、修改、删除。主要包括CREATE、DROP、ALTER语句；

DCL（Data Control Language)：数据控制语言，用于定义用户的访问权限和安全级别。主要包括GRANT和REVOKE语句；

TCL（Transaction Control Language)：事务控制语言，用于维护数据的一致性，包括COMMIT、ROLLBACK和SAVEPOINT语句。

## 后文需要的数据库数据表

图形化用户界面客户端 Navicat => 右键连接 => 新建数据库 => 右键对应数据库 => 运行 SQL 文件 => 右键刷新数据库

![Snipaste_2023-03-06_22-04-17.png](https://cdn.nlark.com/yuque/0/2023/png/1614731/1678111509470-68904683-5c63-43e6-b05f-adc173a3e7be.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)

﻿myemployees.sql

```sql
/*
SQLyog Ultimate v10.00 Beta1
MySQL - 5.5.15 : Database - myemployees
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`myemployees` /*!40100 DEFAULT CHARACTER SET gb2312 */;

USE `myemployees`;

/*Table structure for table `departments` */

DROP TABLE IF EXISTS `departments`;

CREATE TABLE `departments` (
  `department_id` int(4) NOT NULL AUTO_INCREMENT,
  `department_name` varchar(3) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `location_id` int(4) DEFAULT NULL,
  PRIMARY KEY (`department_id`),
  KEY `loc_id_fk` (`location_id`),
  CONSTRAINT `loc_id_fk` FOREIGN KEY (`location_id`) REFERENCES `locations` (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=271 DEFAULT CHARSET=gb2312;

/*Data for the table `departments` */

insert  into `departments`(`department_id`,`department_name`,`manager_id`,`location_id`) values (10,'Adm',200,1700),(20,'Mar',201,1800),(30,'Pur',114,1700),(40,'Hum',203,2400),(50,'Shi',121,1500),(60,'IT',103,1400),(70,'Pub',204,2700),(80,'Sal',145,2500),(90,'Exe',100,1700),(100,'Fin',108,1700),(110,'Acc',205,1700),(120,'Tre',NULL,1700),(130,'Cor',NULL,1700),(140,'Con',NULL,1700),(150,'Sha',NULL,1700),(160,'Ben',NULL,1700),(170,'Man',NULL,1700),(180,'Con',NULL,1700),(190,'Con',NULL,1700),(200,'Ope',NULL,1700),(210,'IT ',NULL,1700),(220,'NOC',NULL,1700),(230,'IT ',NULL,1700),(240,'Gov',NULL,1700),(250,'Ret',NULL,1700),(260,'Rec',NULL,1700),(270,'Pay',NULL,1700);

/*Table structure for table `employees` */

DROP TABLE IF EXISTS `employees`;

CREATE TABLE `employees` (
  `employee_id` int(6) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(20) DEFAULT NULL,
  `last_name` varchar(25) DEFAULT NULL,
  `email` varchar(25) DEFAULT NULL,
  `phone_number` varchar(20) DEFAULT NULL,
  `job_id` varchar(10) DEFAULT NULL,
  `salary` double(10,2) DEFAULT NULL,
  `commission_pct` double(4,2) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `department_id` int(4) DEFAULT NULL,
  `hiredate` datetime DEFAULT NULL,
  PRIMARY KEY (`employee_id`),
  KEY `dept_id_fk` (`department_id`),
  KEY `job_id_fk` (`job_id`),
  CONSTRAINT `dept_id_fk` FOREIGN KEY (`department_id`) REFERENCES `departments` (`department_id`),
  CONSTRAINT `job_id_fk` FOREIGN KEY (`job_id`) REFERENCES `jobs` (`job_id`)
) ENGINE=InnoDB AUTO_INCREMENT=207 DEFAULT CHARSET=gb2312;

/*Data for the table `employees` */

insert  into `employees`(`employee_id`,`first_name`,`last_name`,`email`,`phone_number`,`job_id`,`salary`,`commission_pct`,`manager_id`,`department_id`,`hiredate`) values (100,'Steven','K_ing','SKING','515.123.4567','AD_PRES',24000.00,NULL,NULL,90,'1992-04-03 00:00:00'),(101,'Neena','Kochhar','NKOCHHAR','515.123.4568','AD_VP',17000.00,NULL,100,90,'1992-04-03 00:00:00'),(102,'Lex','De Haan','LDEHAAN','515.123.4569','AD_VP',17000.00,NULL,100,90,'1992-04-03 00:00:00'),(103,'Alexander','Hunold','AHUNOLD','590.423.4567','IT_PROG',9000.00,NULL,102,60,'1992-04-03 00:00:00'),(104,'Bruce','Ernst','BERNST','590.423.4568','IT_PROG',6000.00,NULL,103,60,'1992-04-03 00:00:00'),(105,'David','Austin','DAUSTIN','590.423.4569','IT_PROG',4800.00,NULL,103,60,'1998-03-03 00:00:00'),(106,'Valli','Pataballa','VPATABAL','590.423.4560','IT_PROG',4800.00,NULL,103,60,'1998-03-03 00:00:00'),(107,'Diana','Lorentz','DLORENTZ','590.423.5567','IT_PROG',4200.00,NULL,103,60,'1998-03-03 00:00:00'),(108,'Nancy','Greenberg','NGREENBE','515.124.4569','FI_MGR',12000.00,NULL,101,100,'1998-03-03 00:00:00'),(109,'Daniel','Faviet','DFAVIET','515.124.4169','FI_ACCOUNT',9000.00,NULL,108,100,'1998-03-03 00:00:00'),(110,'John','Chen','JCHEN','515.124.4269','FI_ACCOUNT',8200.00,NULL,108,100,'2000-09-09 00:00:00'),(111,'Ismael','Sciarra','ISCIARRA','515.124.4369','FI_ACCOUNT',7700.00,NULL,108,100,'2000-09-09 00:00:00'),(112,'Jose Manuel','Urman','JMURMAN','515.124.4469','FI_ACCOUNT',7800.00,NULL,108,100,'2000-09-09 00:00:00'),(113,'Luis','Popp','LPOPP','515.124.4567','FI_ACCOUNT',6900.00,NULL,108,100,'2000-09-09 00:00:00'),(114,'Den','Raphaely','DRAPHEAL','515.127.4561','PU_MAN',11000.00,NULL,100,30,'2000-09-09 00:00:00'),(115,'Alexander','Khoo','AKHOO','515.127.4562','PU_CLERK',3100.00,NULL,114,30,'2000-09-09 00:00:00'),(116,'Shelli','Baida','SBAIDA','515.127.4563','PU_CLERK',2900.00,NULL,114,30,'2000-09-09 00:00:00'),(117,'Sigal','Tobias','STOBIAS','515.127.4564','PU_CLERK',2800.00,NULL,114,30,'2000-09-09 00:00:00'),(118,'Guy','Himuro','GHIMURO','515.127.4565','PU_CLERK',2600.00,NULL,114,30,'2000-09-09 00:00:00'),(119,'Karen','Colmenares','KCOLMENA','515.127.4566','PU_CLERK',2500.00,NULL,114,30,'2000-09-09 00:00:00'),(120,'Matthew','Weiss','MWEISS','650.123.1234','ST_MAN',8000.00,NULL,100,50,'2004-02-06 00:00:00'),(121,'Adam','Fripp','AFRIPP','650.123.2234','ST_MAN',8200.00,NULL,100,50,'2004-02-06 00:00:00'),(122,'Payam','Kaufling','PKAUFLIN','650.123.3234','ST_MAN',7900.00,NULL,100,50,'2004-02-06 00:00:00'),(123,'Shanta','Vollman','SVOLLMAN','650.123.4234','ST_MAN',6500.00,NULL,100,50,'2004-02-06 00:00:00'),(124,'Kevin','Mourgos','KMOURGOS','650.123.5234','ST_MAN',5800.00,NULL,100,50,'2004-02-06 00:00:00'),(125,'Julia','Nayer','JNAYER','650.124.1214','ST_CLERK',3200.00,NULL,120,50,'2004-02-06 00:00:00'),(126,'Irene','Mikkilineni','IMIKKILI','650.124.1224','ST_CLERK',2700.00,NULL,120,50,'2004-02-06 00:00:00'),(127,'James','Landry','JLANDRY','650.124.1334','ST_CLERK',2400.00,NULL,120,50,'2004-02-06 00:00:00'),(128,'Steven','Markle','SMARKLE','650.124.1434','ST_CLERK',2200.00,NULL,120,50,'2004-02-06 00:00:00'),(129,'Laura','Bissot','LBISSOT','650.124.5234','ST_CLERK',3300.00,NULL,121,50,'2004-02-06 00:00:00'),(130,'Mozhe','Atkinson','MATKINSO','650.124.6234','ST_CLERK',2800.00,NULL,121,50,'2004-02-06 00:00:00'),(131,'James','Marlow','JAMRLOW','650.124.7234','ST_CLERK',2500.00,NULL,121,50,'2004-02-06 00:00:00'),(132,'TJ','Olson','TJOLSON','650.124.8234','ST_CLERK',2100.00,NULL,121,50,'2004-02-06 00:00:00'),(133,'Jason','Mallin','JMALLIN','650.127.1934','ST_CLERK',3300.00,NULL,122,50,'2004-02-06 00:00:00'),(134,'Michael','Rogers','MROGERS','650.127.1834','ST_CLERK',2900.00,NULL,122,50,'2002-12-23 00:00:00'),(135,'Ki','Gee','KGEE','650.127.1734','ST_CLERK',2400.00,NULL,122,50,'2002-12-23 00:00:00'),(136,'Hazel','Philtanker','HPHILTAN','650.127.1634','ST_CLERK',2200.00,NULL,122,50,'2002-12-23 00:00:00'),(137,'Renske','Ladwig','RLADWIG','650.121.1234','ST_CLERK',3600.00,NULL,123,50,'2002-12-23 00:00:00'),(138,'Stephen','Stiles','SSTILES','650.121.2034','ST_CLERK',3200.00,NULL,123,50,'2002-12-23 00:00:00'),(139,'John','Seo','JSEO','650.121.2019','ST_CLERK',2700.00,NULL,123,50,'2002-12-23 00:00:00'),(140,'Joshua','Patel','JPATEL','650.121.1834','ST_CLERK',2500.00,NULL,123,50,'2002-12-23 00:00:00'),(141,'Trenna','Rajs','TRAJS','650.121.8009','ST_CLERK',3500.00,NULL,124,50,'2002-12-23 00:00:00'),(142,'Curtis','Davies','CDAVIES','650.121.2994','ST_CLERK',3100.00,NULL,124,50,'2002-12-23 00:00:00'),(143,'Randall','Matos','RMATOS','650.121.2874','ST_CLERK',2600.00,NULL,124,50,'2002-12-23 00:00:00'),(144,'Peter','Vargas','PVARGAS','650.121.2004','ST_CLERK',2500.00,NULL,124,50,'2002-12-23 00:00:00'),(145,'John','Russell','JRUSSEL','011.44.1344.429268','SA_MAN',14000.00,0.40,100,80,'2002-12-23 00:00:00'),(146,'Karen','Partners','KPARTNER','011.44.1344.467268','SA_MAN',13500.00,0.30,100,80,'2002-12-23 00:00:00'),(147,'Alberto','Errazuriz','AERRAZUR','011.44.1344.429278','SA_MAN',12000.00,0.30,100,80,'2002-12-23 00:00:00'),(148,'Gerald','Cambrault','GCAMBRAU','011.44.1344.619268','SA_MAN',11000.00,0.30,100,80,'2002-12-23 00:00:00'),(149,'Eleni','Zlotkey','EZLOTKEY','011.44.1344.429018','SA_MAN',10500.00,0.20,100,80,'2002-12-23 00:00:00'),(150,'Peter','Tucker','PTUCKER','011.44.1344.129268','SA_REP',10000.00,0.30,145,80,'2014-03-05 00:00:00'),(151,'David','Bernstein','DBERNSTE','011.44.1344.345268','SA_REP',9500.00,0.25,145,80,'2014-03-05 00:00:00'),(152,'Peter','Hall','PHALL','011.44.1344.478968','SA_REP',9000.00,0.25,145,80,'2014-03-05 00:00:00'),(153,'Christopher','Olsen','COLSEN','011.44.1344.498718','SA_REP',8000.00,0.20,145,80,'2014-03-05 00:00:00'),(154,'Nanette','Cambrault','NCAMBRAU','011.44.1344.987668','SA_REP',7500.00,0.20,145,80,'2014-03-05 00:00:00'),(155,'Oliver','Tuvault','OTUVAULT','011.44.1344.486508','SA_REP',7000.00,0.15,145,80,'2014-03-05 00:00:00'),(156,'Janette','K_ing','JKING','011.44.1345.429268','SA_REP',10000.00,0.35,146,80,'2014-03-05 00:00:00'),(157,'Patrick','Sully','PSULLY','011.44.1345.929268','SA_REP',9500.00,0.35,146,80,'2014-03-05 00:00:00'),(158,'Allan','McEwen','AMCEWEN','011.44.1345.829268','SA_REP',9000.00,0.35,146,80,'2014-03-05 00:00:00'),(159,'Lindsey','Smith','LSMITH','011.44.1345.729268','SA_REP',8000.00,0.30,146,80,'2014-03-05 00:00:00'),(160,'Louise','Doran','LDORAN','011.44.1345.629268','SA_REP',7500.00,0.30,146,80,'2014-03-05 00:00:00'),(161,'Sarath','Sewall','SSEWALL','011.44.1345.529268','SA_REP',7000.00,0.25,146,80,'2014-03-05 00:00:00'),(162,'Clara','Vishney','CVISHNEY','011.44.1346.129268','SA_REP',10500.00,0.25,147,80,'2014-03-05 00:00:00'),(163,'Danielle','Greene','DGREENE','011.44.1346.229268','SA_REP',9500.00,0.15,147,80,'2014-03-05 00:00:00'),(164,'Mattea','Marvins','MMARVINS','011.44.1346.329268','SA_REP',7200.00,0.10,147,80,'2014-03-05 00:00:00'),(165,'David','Lee','DLEE','011.44.1346.529268','SA_REP',6800.00,0.10,147,80,'2014-03-05 00:00:00'),(166,'Sundar','Ande','SANDE','011.44.1346.629268','SA_REP',6400.00,0.10,147,80,'2014-03-05 00:00:00'),(167,'Amit','Banda','ABANDA','011.44.1346.729268','SA_REP',6200.00,0.10,147,80,'2014-03-05 00:00:00'),(168,'Lisa','Ozer','LOZER','011.44.1343.929268','SA_REP',11500.00,0.25,148,80,'2014-03-05 00:00:00'),(169,'Harrison','Bloom','HBLOOM','011.44.1343.829268','SA_REP',10000.00,0.20,148,80,'2014-03-05 00:00:00'),(170,'Tayler','Fox','TFOX','011.44.1343.729268','SA_REP',9600.00,0.20,148,80,'2014-03-05 00:00:00'),(171,'William','Smith','WSMITH','011.44.1343.629268','SA_REP',7400.00,0.15,148,80,'2014-03-05 00:00:00'),(172,'Elizabeth','Bates','EBATES','011.44.1343.529268','SA_REP',7300.00,0.15,148,80,'2014-03-05 00:00:00'),(173,'Sundita','Kumar','SKUMAR','011.44.1343.329268','SA_REP',6100.00,0.10,148,80,'2014-03-05 00:00:00'),(174,'Ellen','Abel','EABEL','011.44.1644.429267','SA_REP',11000.00,0.30,149,80,'2014-03-05 00:00:00'),(175,'Alyssa','Hutton','AHUTTON','011.44.1644.429266','SA_REP',8800.00,0.25,149,80,'2014-03-05 00:00:00'),(176,'Jonathon','Taylor','JTAYLOR','011.44.1644.429265','SA_REP',8600.00,0.20,149,80,'2014-03-05 00:00:00'),(177,'Jack','Livingston','JLIVINGS','011.44.1644.429264','SA_REP',8400.00,0.20,149,80,'2014-03-05 00:00:00'),(178,'Kimberely','Grant','KGRANT','011.44.1644.429263','SA_REP',7000.00,0.15,149,NULL,'2014-03-05 00:00:00'),(179,'Charles','Johnson','CJOHNSON','011.44.1644.429262','SA_REP',6200.00,0.10,149,80,'2014-03-05 00:00:00'),(180,'Winston','Taylor','WTAYLOR','650.507.9876','SH_CLERK',3200.00,NULL,120,50,'2014-03-05 00:00:00'),(181,'Jean','Fleaur','JFLEAUR','650.507.9877','SH_CLERK',3100.00,NULL,120,50,'2014-03-05 00:00:00'),(182,'Martha','Sullivan','MSULLIVA','650.507.9878','SH_CLERK',2500.00,NULL,120,50,'2014-03-05 00:00:00'),(183,'Girard','Geoni','GGEONI','650.507.9879','SH_CLERK',2800.00,NULL,120,50,'2014-03-05 00:00:00'),(184,'Nandita','Sarchand','NSARCHAN','650.509.1876','SH_CLERK',4200.00,NULL,121,50,'2014-03-05 00:00:00'),(185,'Alexis','Bull','ABULL','650.509.2876','SH_CLERK',4100.00,NULL,121,50,'2014-03-05 00:00:00'),(186,'Julia','Dellinger','JDELLING','650.509.3876','SH_CLERK',3400.00,NULL,121,50,'2014-03-05 00:00:00'),(187,'Anthony','Cabrio','ACABRIO','650.509.4876','SH_CLERK',3000.00,NULL,121,50,'2014-03-05 00:00:00'),(188,'Kelly','Chung','KCHUNG','650.505.1876','SH_CLERK',3800.00,NULL,122,50,'2014-03-05 00:00:00'),(189,'Jennifer','Dilly','JDILLY','650.505.2876','SH_CLERK',3600.00,NULL,122,50,'2014-03-05 00:00:00'),(190,'Timothy','Gates','TGATES','650.505.3876','SH_CLERK',2900.00,NULL,122,50,'2014-03-05 00:00:00'),(191,'Randall','Perkins','RPERKINS','650.505.4876','SH_CLERK',2500.00,NULL,122,50,'2014-03-05 00:00:00'),(192,'Sarah','Bell','SBELL','650.501.1876','SH_CLERK',4000.00,NULL,123,50,'2014-03-05 00:00:00'),(193,'Britney','Everett','BEVERETT','650.501.2876','SH_CLERK',3900.00,NULL,123,50,'2014-03-05 00:00:00'),(194,'Samuel','McCain','SMCCAIN','650.501.3876','SH_CLERK',3200.00,NULL,123,50,'2014-03-05 00:00:00'),(195,'Vance','Jones','VJONES','650.501.4876','SH_CLERK',2800.00,NULL,123,50,'2014-03-05 00:00:00'),(196,'Alana','Walsh','AWALSH','650.507.9811','SH_CLERK',3100.00,NULL,124,50,'2014-03-05 00:00:00'),(197,'Kevin','Feeney','KFEENEY','650.507.9822','SH_CLERK',3000.00,NULL,124,50,'2014-03-05 00:00:00'),(198,'Donald','OConnell','DOCONNEL','650.507.9833','SH_CLERK',2600.00,NULL,124,50,'2014-03-05 00:00:00'),(199,'Douglas','Grant','DGRANT','650.507.9844','SH_CLERK',2600.00,NULL,124,50,'2014-03-05 00:00:00'),(200,'Jennifer','Whalen','JWHALEN','515.123.4444','AD_ASST',4400.00,NULL,101,10,'2016-03-03 00:00:00'),(201,'Michael','Hartstein','MHARTSTE','515.123.5555','MK_MAN',13000.00,NULL,100,20,'2016-03-03 00:00:00'),(202,'Pat','Fay','PFAY','603.123.6666','MK_REP',6000.00,NULL,201,20,'2016-03-03 00:00:00'),(203,'Susan','Mavris','SMAVRIS','515.123.7777','HR_REP',6500.00,NULL,101,40,'2016-03-03 00:00:00'),(204,'Hermann','Baer','HBAER','515.123.8888','PR_REP',10000.00,NULL,101,70,'2016-03-03 00:00:00'),(205,'Shelley','Higgins','SHIGGINS','515.123.8080','AC_MGR',12000.00,NULL,101,110,'2016-03-03 00:00:00'),(206,'William','Gietz','WGIETZ','515.123.8181','AC_ACCOUNT',8300.00,NULL,205,110,'2016-03-03 00:00:00');

/*Table structure for table `jobs` */

DROP TABLE IF EXISTS `jobs`;

CREATE TABLE `jobs` (
  `job_id` varchar(10) NOT NULL,
  `job_title` varchar(35) DEFAULT NULL,
  `min_salary` int(6) DEFAULT NULL,
  `max_salary` int(6) DEFAULT NULL,
  PRIMARY KEY (`job_id`)
) ENGINE=InnoDB DEFAULT CHARSET=gb2312;

/*Data for the table `jobs` */

insert  into `jobs`(`job_id`,`job_title`,`min_salary`,`max_salary`) values ('AC_ACCOUNT','Public Accountant',4200,9000),('AC_MGR','Accounting Manager',8200,16000),('AD_ASST','Administration Assistant',3000,6000),('AD_PRES','President',20000,40000),('AD_VP','Administration Vice President',15000,30000),('FI_ACCOUNT','Accountant',4200,9000),('FI_MGR','Finance Manager',8200,16000),('HR_REP','Human Resources Representative',4000,9000),('IT_PROG','Programmer',4000,10000),('MK_MAN','Marketing Manager',9000,15000),('MK_REP','Marketing Representative',4000,9000),('PR_REP','Public Relations Representative',4500,10500),('PU_CLERK','Purchasing Clerk',2500,5500),('PU_MAN','Purchasing Manager',8000,15000),('SA_MAN','Sales Manager',10000,20000),('SA_REP','Sales Representative',6000,12000),('SH_CLERK','Shipping Clerk',2500,5500),('ST_CLERK','Stock Clerk',2000,5000),('ST_MAN','Stock Manager',5500,8500);

/*Table structure for table `locations` */

DROP TABLE IF EXISTS `locations`;

CREATE TABLE `locations` (
  `location_id` int(11) NOT NULL AUTO_INCREMENT,
  `street_address` varchar(40) DEFAULT NULL,
  `postal_code` varchar(12) DEFAULT NULL,
  `city` varchar(30) DEFAULT NULL,
  `state_province` varchar(25) DEFAULT NULL,
  `country_id` varchar(2) DEFAULT NULL,
  PRIMARY KEY (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=3201 DEFAULT CHARSET=gb2312;

/*Data for the table `locations` */

insert  into `locations`(`location_id`,`street_address`,`postal_code`,`city`,`state_province`,`country_id`) values (1000,'1297 Via Cola di Rie','00989','Roma',NULL,'IT'),(1100,'93091 Calle della Testa','10934','Venice',NULL,'IT'),(1200,'2017 Shinjuku-ku','1689','Tokyo','Tokyo Prefecture','JP'),(1300,'9450 Kamiya-cho','6823','Hiroshima',NULL,'JP'),(1400,'2014 Jabberwocky Rd','26192','Southlake','Texas','US'),(1500,'2011 Interiors Blvd','99236','South San Francisco','California','US'),(1600,'2007 Zagora St','50090','South Brunswick','New Jersey','US'),(1700,'2004 Charade Rd','98199','Seattle','Washington','US'),(1800,'147 Spadina Ave','M5V 2L7','Toronto','Ontario','CA'),(1900,'6092 Boxwood St','YSW 9T2','Whitehorse','Yukon','CA'),(2000,'40-5-12 Laogianggen','190518','Beijing',NULL,'CN'),(2100,'1298 Vileparle (E)','490231','Bombay','Maharashtra','IN'),(2200,'12-98 Victoria Street','2901','Sydney','New South Wales','AU'),(2300,'198 Clementi North','540198','Singapore',NULL,'SG'),(2400,'8204 Arthur St',NULL,'London',NULL,'UK'),(2500,'Magdalen Centre, The Oxford Science Park','OX9 9ZB','Oxford','Oxford','UK'),(2600,'9702 Chester Road','09629850293','Stretford','Manchester','UK'),(2700,'Schwanthalerstr. 7031','80925','Munich','Bavaria','DE'),(2800,'Rua Frei Caneca 1360 ','01307-002','Sao Paulo','Sao Paulo','BR'),(2900,'20 Rue des Corps-Saints','1730','Geneva','Geneve','CH'),(3000,'Murtenstrasse 921','3095','Bern','BE','CH'),(3100,'Pieter Breughelstraat 837','3029SK','Utrecht','Utrecht','NL'),(3200,'Mariano Escobedo 9991','11932','Mexico City','Distrito Federal,','MX');

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```

工资等级

```sql
CREATE TABLE job_grades
(grade_level VARCHAR(3),
 lowest_sal  INT,
 highest_sal INT);

INSERT INTO job_grades
VALUES ('A', 1000, 2999);

INSERT INTO job_grades
VALUES ('B', 3000, 5999);

INSERT INTO job_grades
VALUES('C', 6000, 9999);

INSERT INTO job_grades
VALUES('D', 10000, 14999);

INSERT INTO job_grades
VALUES('E', 15000, 24999);

INSERT INTO job_grades
VALUES('F', 25000, 40000);
```

girls.sql

```sql
/*
SQLyog Ultimate v10.00 Beta1
MySQL - 5.7.18-log : Database - girls
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`girls` /*!40100 DEFAULT CHARACTER SET utf8 */;

USE `girls`;

/*Table structure for table `admin` */

DROP TABLE IF EXISTS `admin`;

CREATE TABLE `admin` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `username` VARCHAR(10) NOT NULL,
  `password` VARCHAR(10) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

/*Data for the table `admin` */

INSERT  INTO `admin`(`id`,`username`,`password`) VALUES (1,'john','8888'),(2,'lyt','6666');

/*Table structure for table `beauty` */

DROP TABLE IF EXISTS `beauty`;

CREATE TABLE `beauty` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(50) NOT NULL,
  `sex` CHAR(1) DEFAULT '女',
  `borndate` DATETIME DEFAULT '1987-01-01 00:00:00',
  `phone` VARCHAR(11) NOT NULL,
  `photo` BLOB,
  `boyfriend_id` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8;

/*Data for the table `beauty` */

INSERT  INTO `beauty`(`id`,`name`,`sex`,`borndate`,`phone`,`photo`,`boyfriend_id`) VALUES (1,'柳岩','女','1988-02-03 00:00:00','18209876577',NULL,8),(2,'苍老师','女','1987-12-30 00:00:00','18219876577',NULL,9),(3,'Angelababy','女','1989-02-03 00:00:00','18209876567',NULL,3),(4,'热巴','女','1993-02-03 00:00:00','18209876579',NULL,2),(5,'周冬雨','女','1992-02-03 00:00:00','18209179577',NULL,9),(6,'周芷若','女','1988-02-03 00:00:00','18209876577',NULL,1),(7,'岳灵珊','女','1987-12-30 00:00:00','18219876577',NULL,9),(8,'小昭','女','1989-02-03 00:00:00','18209876567',NULL,1),(9,'双儿','女','1993-02-03 00:00:00','18209876579',NULL,9),(10,'王语嫣','女','1992-02-03 00:00:00','18209179577',NULL,4),(11,'夏雪','女','1993-02-03 00:00:00','18209876579',NULL,9),(12,'赵敏','女','1992-02-03 00:00:00','18209179577',NULL,1);

/*Table structure for table `boys` */

DROP TABLE IF EXISTS `boys`;

CREATE TABLE `boys` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `boyName` VARCHAR(20) DEFAULT NULL,
  `userCP` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

/*Data for the table `boys` */

INSERT  INTO `boys`(`id`,`boyName`,`userCP`) VALUES (1,'张无忌',100),(2,'鹿晗',800),(3,'黄晓明',50),(4,'段誉',300);

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```

学生数据库 student.sql

```sql
DROP DATABASE IF EXISTS student;
CREATE DATABASE student;
USE student;
CREATE TABLE student(
	studentno VARCHAR(10) NOT NULL PRIMARY KEY,
	studentname VARCHAR(20) NOT NULL,
	loginpwd VARCHAR(8) NOT NULL,
	sex CHAR(1) ,
	majorid INT NOT NULL REFERENCES grade(majorid),
	phone VARCHAR(11),
	email VARCHAR(20) ,
	borndate DATETIME
)DEFAULT CHARSET=utf8;

CREATE TABLE major(
	majorid INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	majorname VARCHAR(20) NOT  NULL

)DEFAULT CHARSET=utf8;
CREATE TABLE result(
	id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	studentno VARCHAR(10) NOT NULL REFERENCES student(studentno),
	score DOUBLE
)DEFAULT CHARSET=utf8;


INSERT INTO major VALUES(NULL,'javaee');
INSERT INTO major VALUES(NULL,'html5');
INSERT INTO major VALUES(NULL,'android');


INSERT INTO student VALUES('S001','张三封','8888','男',1,'13288886666','zhangsanfeng@126.com','1966-9-1');
INSERT INTO student VALUES('S002','殷天正','8888','男',1,'13888881234','yintianzheng@qq.com','1976-9-2');
INSERT INTO student VALUES('S003','周伯通','8888','男',2,'13288886666','zhoubotong@126.com','1986-9-3');
INSERT INTO student VALUES('S004','张翠山','8888','男',1,'13288886666',NULL,'1995-9-4');
INSERT INTO student VALUES('S005','小小张','8888','女',3,'13288885678','xiaozhang@126.com','1990-9-5');

INSERT INTO student VALUES('S006','张无忌','8888','男',2,'13288886666','zhangwuji@126.com','1998-8-9');
INSERT INTO student VALUES('S007','赵敏','0000','女',1,'13288880987','zhaomin@126.com','1998-6-9');
INSERT INTO student VALUES('S008','周芷若','6666','女',1,'13288883456','zhouzhiruo@126.com','1996-7-9');
INSERT INTO student VALUES('S009','殷素素','8888','女',1,'13288886666','yinsusu@163.com','1986-1-9');
INSERT INTO student VALUES('S010','宋远桥','6666','男',3,'1328888890','songyuanqiao@qq.com','1996-2-9');


INSERT INTO student VALUES('S011','杨不悔','6666','女',2,'13288882345',NULL,'1995-9-9');
INSERT INTO student VALUES('S012','杨逍','9999','男',1,'13288885432',NULL,'1976-9-9');
INSERT INTO student VALUES('S013','纪晓芙','9999','女',3,'13288888765',NULL,'1976-9-9');
INSERT INTO student VALUES('S014','谢逊','9999','男',1,'13288882211',NULL,'1946-9-9');
INSERT INTO student VALUES('S015','宋青书','9999','男',3,'13288889900',NULL,'1976-6-8');



INSERT INTO result VALUES(NULL,'s001',100);
INSERT INTO result VALUES(NULL,'s002',90);
INSERT INTO result VALUES(NULL,'s003',80);

INSERT INTO result VALUES(NULL,'s004',70);
INSERT INTO result VALUES(NULL,'s005',60);
INSERT INTO result VALUES(NULL,'s006',50);
INSERT INTO result VALUES(NULL,'s006',40);
INSERT INTO result VALUES(NULL,'s005',95);
INSERT INTO result VALUES(NULL,'s006',88);
```

## MySQL 常见命令

**MySQL 支持用 DESCRIBE 作为 SHOW COLUMNS FROM 的一种快捷方式。**

**换句话说， DESCRIBE customers; 是 SHOW COLUMNS FROM customers; 的一种快捷方式。**

```sh
# 查看 mysql 有哪些数据库
show databases;

# 新建一个数据库
create database 数据库名;
# 例：create database book;

# 选择一个数据库
use 数据库名称;
# 例：use test;

# 查询数据表
show tables;

# 查看指定的数据库中有哪些数据表
show tables from 数据库名;
# 例：show tables from mysql;

# 查询当前所在数据库
select database();

# 新建一个数据表
create table math(
id int,
name varchar(20));
# create table 表名(列 数据类型, 列 数据类型);

# 查看表的结构
desc 表名;
# 例：desc math;

# 查看表中的所有记录
select * from 表名;
# 例：select * from math;

# 向表中插入记录
insert into 表名(列名) values(列对应的值);
# 注意：插入varchar或date 型的数据要用单引号引起来
# 例：insert into math (id,name) values(1,"ton");

# 修改记录
update 表名   set 列1的值,列2 =列2的值 where...
# 例：update math set name="wugang" where id=1;

# 删除记录
delete from 表名 where ...;
# 例：delete from math where id=1;

# 删除数据表
drop table 表名;
# 例：drop table math;

# 查看MySQL服务端版本
# 登录到 mysql 服务端
select version();

# 没有登录到mysql服务端
# 在 cmd 命令界面输入：
mysql --version
mysql --V

# 修改数据库的字符集
alter database 库名 character set 字符集名; /*utf8*/
```

## 基础查询

语法：select 查询列表 from 表名;

类似于 System.out.println(打印东西);

- 1.查询列表可以是：表中的字段、常量值、表达式、函数。

- 2.查询的结果可以是一个虚拟表格;

为了返回第一行或前几行，可使用 LIMIT 子句。

```sql
SELECT last_name FROM employees LIMIT 5;
```

为得出下一个5行，可指定要检索的开始行和行数，

LIMIT 5, 5指示 MySQL 返回从行 5 开始的 5 行。第一个数为开始位置，第二个数为要检索的行数。

如果是第一行开始则为 0 ，因此， LIMIT 1, 1 将检索出第二行而不是第一行。

如果没有足够的行（例如，给出LIMIT 10, 5，但只有13行）， MySQL 将只返回它能返回的那么多行。

```sql
SELECT last_name FROM employees LIMIT 5, 5;
```

案例

```sql
USE myemployees;

#1.查询表中的单个字段
SELECT last_name FROM employees;

#2.查询表中多个字段
SELECT last_name,salary,email FROM employees;

#3.查询表中的所有字段
#虽然使用通配符可能会使你自己省事，不用明确列出所需列，但检索不需要的列通常会降低检索和应用程序的性能。
SELECT * FROM employees;

#4.查询常量
# select 常量值;
# 注意：字符型和日期型的常量值必须用单引号引起来，数值型不需要
SELECT 100;
SELECT 'join';

#5.查询函数
#select 函数名(实参列表);
SELECT VERSION();

#6.查询表达式 
SELECT 100%98;

#7.起别名
/*
别名有时也称为导出列（ derived column），不管称为什么，它们所代表的都是相同的东西
1.便于理解
2.如果要查询的字段有重名的情况,使用别名区分
*/
#方式一:使用AS
SELECT 100%98 AS 结果;
SELECT last_name AS 姓,first_name AS 名 FROM employees;

#方式二:使用空格
SELECT last_name 姓,first_name 名 FROM employees;

#案例:查询salary,结果显示 out put
SELECT salary AS "out put" FROM employees;

#8.去重
# select distinct 字段名 from 表名;
#案例:查询员工表中涉及的所有部门编号
SELECT DISTINCT department_id FROM employees;

#9.+号的作用
#案例:查询员工的名和姓,并显示为姓名
/*
java中的+号:
1.运算符:两个操作数都为数据型
2.连接符:只要有一个操作数为字符串


mysql中的+号:
只能作为运算符

select 100+90; 两个操作数都为数值型,做加法运算,所以结果为 190
select '123+90';其中一方为字符型,试图将字符型数值转换为数值型
	   如果转换成功,则继续做加法运算,所以结果为 213
select 'john'+90; 如果转换失败,则将字符型数值转换成0,所以结果为 90

select null+0; 只要其中一方为null,则结果肯定为null
*/
SELECT last_name+first_name AS 姓名 FROM employees; 

#10.【补充】concat函数 
/*
功能：拼接字符
select concat(字符1，字符2，字符3,...);
*/
SELECT CONCAT('a','b','c') AS 结果 FROM employees;

SELECT CONCAT(last_name,first_name) AS 姓名 FROM employees;

#11.【补充】ifnull函数
#功能：判断某字段或表达式是否为null，如果为 null 返回指定的值，否则返回原本的值
# 如果为 null 返回 0 ，否则返回原本的值
SELECT IFNULL(commission_pct,0) FROM employees;

#12.【补充】isnull函数
#功能：判断某字段或表达式是否为null，如果是，则返回1，否则返回0
```

计算字段是运行时在 SELECT 语句内创建的。

计算字段应用场景：拼接字段、使用别名、执行算术计算。

只有数据库知道 SELECT 语句中哪些列是实际的表列，哪些列是计算字段。

从客户端（如应用程序）的角度来看，计算字段的数据是以与其他列的数据相同的方式返回的。

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697688686019-3aa80e4b-effc-423b-9b33-4c05df856c49.png" alt="Snipaste_2023-10-19_12-06-16" style="zoom:67%;" />

## 条件查询

**在同时使用 ORDER BY 和 WHERE 子句时，应该让 ORDER BY 位于 WHERE 之后， 否则将会产生错误**

**检查 WHERE prod_name=‘fuses’ 语句，它返回 prod_name 的值为Fuses的一行，MySQL在执行匹配时默认不区分大小写，所以 fuses 与 Fuses 匹配。**

**组合使用 AND 和 OR 操作符时，SQL（像多数语言一样）在处理 OR 操作符前，会优先处理 AND 操作符。**

```sql
SELECT prod_name, prod_price FROM products WHERE vend_id = 1002 OR vend_id = 1003 AND prod_price >= 10;
```

上例表示由供应商1003制造的任何价格为10美元（含）以上的产品，或者由供应商1002制造的任何产品，而不管其价格如何。

换句话说，由于AND在计算次序中优先级更高，操作符被错误地组合了。

**此问题的解决方法是使用圆括号明确地分组相应的操作符。**

```sql
SELECT prod_name, prod_price FROM products WHERE (vend_id = 1002 OR vend_id = 1003) AND prod_price >= 10;
```

**MySQL 支 持 使用 NOT 对 IN 、 BETWEEN 和 EXISTS 子句取反，这与多数其他 DBMS 允许使用 NOT 对各种条件取反有很大的差别**

```sql
SELECT prod_name, prod_price FROM products WHERE vend_id NOT IN (1002, 1003) ORDER BY prod_name;
```

语法：select 查询列表 from 表名 where 筛选条件;

分类

```txt
一、按条件表达式筛选
	条件运算符:> < = != <>不等于 >= <= <=>安全等于
	
二、按逻辑表达式筛选
	逻辑运算符:&& || |
	and or not
	
	&& 和 and:两个条件都为true，结果为true，反之为false
	|| 和 or:只要有一个条件为true，结果为true，反之为false
	! 或 not:如果连接的条件本身为false，结果为true，反之为false	
	
三、模糊查询
	like:一般搭配通配符使用，可以判断字符型或数值型
	通配符：%表示任意多个字符（可以为 0 个字符），_表示任意单个字符
	like、between and、in、is null
```

**根据 MySQL 的配置方式，搜索可以是区分大小写的。如果区分大小写， 'jet%' 与 JetPack 1000 将不匹配。**

**术语：通配符（wildcard） 表示用来匹配值的一部分的特殊字符。**

**术语：搜索模式（search pattern）表示由字面值、通配符或两者组合构成的搜索条件。**

**通配符本身实际是 SQL 的 WHERE 子句中有特殊含义的字符， SQL 支持几种通配符。**

搜索模式'%anvil%'表示匹配任何位置包含文本 anvil 的值，而不论它之前或之后出现什么字符。

**通配符可在搜索模式中任意位置使用，并且可以使用多个通配符。**

**在搜索子句中使用通配符，必须使用 LIKE 操作符。** 

**注意，除了一个或多个字符外， % 还能匹配 0 个字符。 % 代表搜索模式中给定位置的 0 个、 1 个或多个字符。**

**注意尾空格** 

尾空格可能会干扰通配符匹配。

例如，在保存词 anvil 时 ， 如 果它后 面有一个或多个空 格 ， 则子句 WHEREprod_name LIKE '%anvil' 将不会匹配它们，

因为在最后的l后有多余的字符。

解决这个问题的一个简单的办法是在搜索模式最后附加一个%。一个更好的办法是使用函数去掉首尾空格。

**注意 NULL** 

虽然似乎 % 通配符可以匹配任何东西，但有一个例外，即 NULL 。即使是 WHERE prod_name LIKE '%' 也不能匹配用值 NULL 作为产品名的行。

**一些使用通配符要记住的技巧**

-  不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。

-  在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的开始处。把通配符置于搜索模式的开始处，搜索起来是最慢的。

-  仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据

**LIKE 和 REGEXP 之间有一个重要的差别**

```sql
SELECT prod_name 
FROM products
WHERE prod_name LIKE '1000'
ORDER BY prod_name;

SELECT prod_name 
FROM products
WHERE prod_name REGEXP '1000'
ORDER BY prod_name;
```

如果执行上述两条语句，会发现第一条语句不返回数据，而第二条语句返回一行。为什么？

LIKE 匹配整个列但没有使用通配符。如果被匹配的文本在列值中出现， LIKE将不会找到它，相应的行也不被返回（除非使用通配符）。

而 REGEXP 在列值内进行匹配，如果被匹配的文本在列值中出现， REGEXP 将会找到它，相应的行将被返回。

这是一个非常重要的差别。

MySQL 中的正则表达式匹配（自版本3.23.4后）不区分大小写（即，大写和小写都匹配）。

为区分大小写，可使用 BINARY 关键字，如 

```sql
WHERE prod_name REGEXP BINARY 'JetPack .000'
```

**可以在不使用数据库表的情况下用 SELECT 来测试正则表达式。**

REGEXP 检查总是返回 0（没有匹配）或 1（匹配）。

可以用带文字串的 REGEXP 来测试表达式，并试验它们。相应的语法如下：

```sql
SELECT 'hello' REGEXP '[0-9]'
```

这个例子显然将返回 0（因为文本 hello 中没有数字）。

案例

```sql
#一.按条件表达式筛选

#案例1:查询工资>12000的员工信息
SELECT * FROM employees WHERE salary>12000;

#案例2:查询部门编号不等于90号的员工名和部门编号
SELECT last_name,department_id FROM employees WHERE department_id <> 90;

#二、按逻辑表达式筛选

#案例1:查询工资z在10000到20000之间的员工名、工资及奖金
SELECT last_name,salary,commission_pct FROM employees WHERE salary>=10000 AND salary<=20000;

#案例2:查询部门编号不是在90-110之间,或者工资高于15000的员工信息
SELECT * FROM employees WHERE department_id <90 OR department_id>110 OR salary>15000;


#三、模糊查询

#1.like

#案例1:查询员工名中包含字符a的员工信息
SELECT * FROM employees WHERE last_name LIKE '%a%';

#案例2:查询员工名中第三个字符为b，第五个字符为a的员工名和工资
SELECT last_name,salary FROM employees WHERE last_name LIKE '__b_a%';

#案例3:查询员工名种第二个字符为_的员工名
SELECT last_name FROM employees WHERE last_name LIKE '_\_%';

#2.between and

#案例1:查询员工编号在100到120之间的员工信息
SELECT * FROM employees WHERE employee_id>=100 AND employee_id<=120;

SELECT * FROM employees WHERE employee_id BETWEEN 100 AND 120;

/*注意事项：
1.提高语句简洁度
2.包含临界值
3.两个临界值不能调换顺序
*/

#3.in
/*
含义:判断某字段的值是否属于in列表中的某一项
特点:
 1.使用 IN 提高语句简洁度
 2.IN 列表的值类型必须一致或兼容
 3.IN 操作符一般比 OR 操作符清单执行更快。
 4.IN 的最大优点是可以包含其他 SELECT 语句，使得能够更动态地建立 WHERE 子句。
*/
#案例1:查询员工的工种编号是IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号

SELECT last_name,job_id FROM employees WHERE job_id='IT_PROG' OR job_id='AD_PRES' OR job_id='AD_VP';

SELECT last_name,job_id FROM employees WHERE job_id IN('IT_PROG','AD_PRES','AD_VP');

#4.is null
/*
=或<>不能用于判断null值
is null 或 is not null 可以判断null值
*/
#案例1:查询没有奖金的员工名和奖金率

SELECT last_name,commission_pct FROM employees WHERE commission_pct IS NULL;

SELECT last_name,commission_pct FROM employees WHERE commission_pct IS NOT NULL;

#安全等于<=>

#案例1:查询没有奖金的员工名和奖金率

SELECT last_name,commission_pct FROM employees WHERE commission_pct <=> NULL;

#案例2:查询工资为12000的员工信息
SELECT last_name,commission_pct FROM employees WHERE salary <=> 12000;

#is null PK <=> ，建议使用 is null
#	      普通类型的数值	null值		可读性
# is null	 ×		       √		    √
# <=>		 √		       √		    ×
```

## 排序查询

检索出的数据并不是以纯粹的随机顺序显示的。如果不排序，数据一般将以它在底层表中出现的顺序显示。这可以是数据最初添加到表中的顺序。

但是，如果数据后来进行过更新或删除，则此顺序将会受到MySQL重用回收存储空间的影响。

因此，如果不明确控制的话，不能（也不应该）依赖该排序顺序。

关系数据库设计理论认为，如果不明确规定排序顺序，则不应该假定检索出的数据的顺序有意义。

**使用 ORDER BY子句时，应该保证它位于 FROM 子句之后。**

**如果使用 LIMIT，它必须位于 ORDER BY 之后。**

**使用子句的次序不对将产生错误消息。**

引入：select * from employees;

语法：select 查询列表 from 表 【where 筛选条件】 order by

特点：

**1.asc 代表的是升序，desc 代表降序，不写默认为升序**

2.order by子句中可以支持单个字段、多个字段、表达式、函数、别名

3.order by子句一般是放在查询语句的最后面，limit 子句除外

```sql
SELECT prod_id, prod_price, prod_name FROM products ORDER BY prod_price, prod_name
```

**在按多个列排序时，在多个行具有相同的 prod_price 值时才对产品按 prod_name 进行排序。**

```sql
SELECT prod_id, prod_price, prod_name FROM products ORDER BY prod_price, prod_name DESC;
```

DESC 关键字只应用到直接位于其前面的列名。

**在上例中，只对 prod_price 列指定 DESC，对 prod_name 列不指定。**

**因此，prod_price 列以降序排序，而 prod_name 列（在每个价格内）仍然按标准的升序排序。**

**如果想在多个列上进行降序排序， 必须对每个列指定 DESC 关键字**

案例

```sql
#案例1:查询员工信息,要求工资从高到低排序（降序）
SELECT * FROM employees ORDER BY salary DESC;
#升序
SELECT * FROM employees ORDER BY salary;

#案例2:查询部门编号是>=90，按入职时间的先后进行排序
SELECT * FROM employees WHERE department_id>=90 ORDER BY hiredate ASC;

#案例3:按年薪的高低显示员工的信息和年薪【按表达式排序】
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) 年薪 FROM employees 
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC; 

#案例4:按年薪的高低显示员工的信息和年薪【按别名排序】
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) 年薪 FROM employees 
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) 年薪 DESC;

#案例5:按姓名的长度显示员工的姓名和工资【按函数排序】
SELECT LENGTH(last_name) 字节长度,last_name,salary
FROM employees
ORDER BY LENGTH(last_name) DESC;

#案例6:查询员工共信息,要求按工资排序，再按员工编号排序【按多个字段排序】
SELECT * FROM employees
ORDER BY salary ASC,employee_id DESC;
```

区分大小写和排序顺序 在对文本性的数据进行排序时， A与a相同吗？ a位于B之前还是位于Z之后？

这些问题不是理论问题，其答案取决于数据库如何设置。

在字典（ dictionary）排序顺序中，A被视为与a相同，这是MySQL（和大多数数据库管理系统）的默认行为。

但是，许多数据库管理员能够在需要时改变这种行为（如果你的数据库包含大量外语字符，可能必须这样做）。

这里，关键的问题是，如果确实需要改变这种排序顺序，用简单的ORDER BY子句做不到。

## 常见函数

**可在 SQL 语句内完成的许多转换和格式化工作都可以直接在客户端应用程序内完成。**

**但一般来说，在数据库服务器上完成这些操作比在客户端中完成要快得多，因为 DBMS 是设计来快速有效地完成这种处理的。**

**函数没有 SQL 的可移植性强** 

能运行在多个系统上的代码称为可移植的（ portable）。

相对来说，多数SQL语句是可移植的，在SQL实现之间有差异时，这些差异通常不那么难处理。

**而函数的可移植性却不强。几乎每种主要的DBMS的实现都支持其他实现不支持的函数，而且有时差异还很大。**

**为了代码的可移植，许多SQL程序员不赞成使用特殊实现的功能。虽然这样做很有好处，但不总是利于应用程序的性能。**

**如果不使用这些函数，编写某些应用程序代码会很艰难，必须利用其他方法来实现。**

**如果你决定使用函数，应该保证做好代码注释，以便以后你（或其他人）能确切地知道所编写 SQL 代码的含义。**

优点：1.隐藏了实现细节 2.提高了代码的重用性

语法：select 函数名(实参列表) 【from 表】;

特点：

1.叫什么(函数名)

2.干什么(函数功能)

分类:

1.单行函数，如 concat、length、ifnull 等

2.分组函数，做统计使用

### 单行函数

单行函数分类：字符函数、数学函数、日期函数、其他函数、流程控制函数

#### 字符函数

```sql
#一.字符函数
#1.length 获取参数值的字节值
SELECT LENGTH('subei');
SELECT LENGTH('鬼谷子qwe');

SHOW VARIABLES LIKE '%char%';

#2.concat 拼接字符串 把多个串连接起来形成一个较长的串。需要一个或多个指定的串，各个串之间用逗号分隔
SELECT CONCAT(last_name,'_',first_name) 姓名 FROM employees;

#3.upper:变大写、lower：变小写

SELECT UPPER('ton');
SELECT LOWER('ton');

#示例：将姓变大写，名变小写，然后拼接
SELECT CONCAT(UPPER(last_name),LOWER(first_name)) 姓名 FROM employees;


#4.substr、substring
# SELECT SUBSTR(str, startIndex);
# SELECT SUBSTR(str, startIndex. length);
#注意:索引从1开始

#截取从指定所有处后面的所以字符
SELECT SUBSTR('吴刚伐桂在天上',4) out_put;

#截取从指定索引处指定字符长度的字符
SELECT SUBSTR('吴刚伐桂在天上',1,2) out_put;

#案例:姓名中首字符大写,其他字符小写，然后用_拼接,显示出来
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2))) out_put FROM employees;

#5.instr:获取子串第一次出现的索引,找不到返回0
SELECT INSTR('MySQL技术进阶','技术') AS out_put;

#6.trim:去前后空格 MySQL除了支持RTrim()（去掉串右边的空格），还支持LTrim()（去掉串左边的空格）以及Trim()（去掉串左右两边的空格）

SELECT LENGTH(TRIM('	霍山	')) AS out_put;

SELECT TRIM('+' FROM '++++李刚+++刘邦+++') AS out_put;

#7.lpad:用指定的字符实现左填充指定长度
SELECT LPAD('梅林',8,'+') AS out_put;

#8.rpad:用指定的字符实现右填充指定长度
SELECT RPAD('梅林',5,'&') AS out_put;

#9.replace:替换
SELECT REPLACE('莉莉伊万斯的青梅竹马是詹姆','詹姆','斯内普') AS out_put;
```

#### 数学函数

```sql
#1.round:四舍五入
SELECT ROUND(1.45);
SELECT ROUND(1.567,2);

#2.ceil:向上取整,返回>=该参数的最小整数
SELECT CEIL(1.005);
SELECT CEIL(-1.002);

#3.floor:向下取整,返回<=该参数的最大整数
SELECT FLOOR(-9.99);

#4.truncate:截断
SELECT TRUNCATE(1.65,1);

#5.mod:取余
SELECT MOD(10,3);

#6.rand:获取随机数，返回0-1之间的小数
SELECT RAND();
```

#### 日期函数

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697691335895-f876b3b5-140c-4de2-9bf0-13efa2c7d3ec.png" alt="Snipaste_2023-10-19_12-55-27" style="zoom: 67%;" />

```sql
#1.now:返回当前系统时间+日期
SELECT NOW();

#2.year:返回年
SELECT YEAR(NOW());
SELECT YEAR(hiredate) 年 FROM employees;

#3.month:返回月
#monrhname:以英文形式返回月
SELECT MONTH(NOW());
SELECT MONTHNAME(NOW());

#4.day:返回日
#datediff:返回两个日期相差的天数
SELECT DAY(NOW());
SELECT DATEDIFF('2020/06/30','2020/06/21');

#5.str_to_date:将字符通过指定格式转换成日期
SELECT STR_TO_DATE('2020-5-13','%Y-%c-%d') AS out_put;

#6.date_format:将日期转换成字符
SELECT DATE_FORMAT('2020/6/6','%Y年%m月%d日') AS out_put;
SELECT DATE_FORMAT(NOW(),'%Y年%m月%d日') AS out_put;

#7.curdate:返回当前日期
SELECT CURDATE();

#8.curtime:返回当前时间
SELECT CURTIME();
```

#### 流程控制函数

```sql
#1.if函数: if else效果
# IF(表达式, true结果, false结果)
# 显示结果为 小
SELECT IF(10<5,'大','小');

SELECT last_name,commission_pct,IF(commission_pct IS NULL,'没奖金！！！','有奖金!!!') 备注 FROM employees;

#2.case函数
#使用一:switch case 的效果
/*
java中
switch(变量){
	case 变量:语句1;break;
	...
	default:语句n;break;
}

mysql中

case 要判断的变量或表达式
when 常量1 then 要显示的值1或语句1
when 常量2 then 要显示的值2或语句2
...
else 要显示的值n或语句n
end

#案例:查询员工的工资,要求:

部门号=30,显示的工资为1.1倍
部门号=40,显示的工资为1.2倍
部门号=50,显示的工资为1.3倍
其他部门,显示的工资为原工资

*/

SELECT salary 原始工资,department_id,
CASE department_id
WHEN 30 THEN salary*1.1
WHEN 40 THEN salary*1.2
WHEN 50 THEN salary*1.3
ELSE salary
END AS 新工资
FROM employees;

#3.case函数的使用二:类似于多重if
/*
java中:
if(条件1){
	语句1;
}else if(条件2){
	语句2;
}
...
else{
	语句n;
}	

mysql中:
case 
when 条件1 then 要显示的值1或语句1
when 条件2 then 要显示的值2或语句2
...
else 要显示的值n或语句n
end

*/

#案例:查询员工的工资的情况
/*
如果工资>20000，显示A级别
如果工资>15000，显示B级别
如果工资>10000，显示c级别
否则，显示D级别
*/

SELECT salary,
CASE
WHEN salary>20000 THEN 'A'
WHEN salary>15000 THEN 'B'
WHEN salary>10000 THEN 'C'
ELSE 'D'
END AS 工资等级
FROM employees;
```

#### 其他函数

```sql
#version 当前数据库服务器的版本
SELECT VERSION();

#database 当前打开的数据库
SELECT DATABASE();

#user当前用户
SELECT USER();

#password('字符')：返回该字符的密码形式
SELECT PASSWORD('a');

#md5('字符'):返回该字符的md5加密形式
SELECT MD5('a');
```

### 分组函数

功能：用作统计使用，又称为聚合函数或统计函数或组函数

分类：sum 求和、avg 平均值、max 最大值、min最小值、count 计算个数

count函数

count(字段)：统计该字段非空值的个数

count(*):统计结果集的行数

**注意 ：如果指定列名，则 DISTINCT 只能用于 COUNT() 。**

**DISTINCT 不能用于 COUNT(*)，因此不允许使用 COUNT(DISTINCT)，否则会产生错误。**

**类似地， DISTINCT必须使用列名，不能用于计算或表达式。**

**将 DISTINCT 用于 MIN() 和 MAX()** 

**虽然 DISTINCT 从技术上可用于 MIN() 和 MAX() ，但这样做实际上没有价值。**

一个列中的最小值和最大值不管是否包含不同值都是相同的

案例

```sql
#1.简单使用
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT COUNT(salary) FROM employees;
# SELECT 语句可根据需要包含多个分组函数
SELECT SUM(salary) 和,ROUND(AVG(salary),2) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
FROM employees;

#2.参数支持哪些数据类型，sum和avg一般用于处理数值型，max、min、count可以处理任何数据类型

SELECT SUM(last_name),AVG(last_name) FROM employees;
SELECT SUM(hiredate),AVG(hiredate) FROM employees;

SELECT MAX(last_name),MIN(last_name) FROM employees;
SELECT MAX(hiredate),MIN(hiredate) FROM employees;

SELECT COUNT(commission_pct) FROM employees;
SELECT COUNT(last_name) FROM employees;

#3.是否忽略null，分组函数都忽略null，值为 null 的字段都不参与运算 

SELECT SUM(commission_pct),AVG(commission_pct) FROM employees;

SELECT commission_pct FROM employees;

SELECT SUM(commission_pct),AVG(commission_pct),SUM(commission_pct)/35,AVG(commission_pct)/107 FROM employees;

SELECT MAX(commission_pct),MIN(commission_pct) FROM employees;

SELECT COUNT(commission_pct) FROM employees;

#4.和distinct搭配，实现去重的统计

SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;

SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;

#5.count函数详解
# 使用COUNT(column)对特定列中具有值的行进行计数，忽略 NULL 值。
SELECT COUNT(salary) FROM employees;
# 统计行数 使用COUNT(*)对表中行的数目进行计数， 不管表列中包含的是空值（ NULL）还是非空值。
SELECT COUNT(*) FROM employees;
# 统计行数 COUNT(1) 1 可以是任意常量
SELECT COUNT(1) FROM employees;
/*
效率上：
MyISAM存储引擎，count(*)最高
InnoDB存储引擎，count(*)和count(1)效率>count(字段)
*/

#6.和分组函数一同查询的字段有限制
#和分组函数一同查询的字段，一般要求是 group by 后出现的字段
SELECT AVG(salary),employee_id FROM employees;
```

![Snipaste_2023-10-19_13-01-46](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697691725128-4db0c864-df15-4593-ba0f-54a5949ecae8.png)

**对非数值数据使用 MAX() 和 MIN()**

虽然 MAX() 一般用来找出最大的数值或日期值，

但 MySQL 允许将它用来返回任意列中的最大值，包括返回文本列中的最大值。

在用于文本数据时，如果数据按相应的列排序，则 MAX() 返回最后一行。

MIN() 函数与 MAX() 函数类似，MySQL允许将它用来返回任意列中的最小值，包括返回文本列中的最小值。

在用于文本数据时，如果数据按相应的列排序，则 MIN() 返回最前面的行。

## 分组查询

分组允许把数据分为多个逻辑组，以便能对每个组进行计算

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697692724100-a516520a-da03-41d1-9110-27c9fe9149c9.png" alt="Snipaste_2023-10-19_13-18-17" style="zoom: 67%;" />

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1679916218753-bc35a2c1-f3da-4f05-8f4e-d2737b99ccad.png" alt="img" style="zoom: 50%;" />

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1679916218831-90883c02-72b4-4795-b134-33980b961958.png" alt="img" style="zoom: 50%;" />

语法：

```sql
select 分组函数,列（即查询列表或分组的字段，要求出现在 group by 的后面）
from 表
[where 筛选条件]
group by 分组的字段
[having 分组后的筛选]
[order by 子句]
```

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697694501172-7cfdc710-d990-4c6b-afd0-e4be0920693b.png" alt="Snipaste_2023-10-19_13-48-04" style="zoom:67%;" />

**执行顺序**

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680153183714-712e2ea4-ba3d-45ce-900c-05e3f69cb646.png" alt="img" style="zoom: 67%;" />

**LIMIT 要检索的行数 ⑦**

查询列表必须特殊,要求是分组函数和group by后出现的字段

```txt
			使用关键字	筛选的表	 位置
分组前筛选	where		原始表		  group by的前面
分组后筛选	having		分组后的结果	group by的后面
```

1、能用分组前筛选的，就优先考虑使用分组前筛选

2、分组函数做条件时要放在 having 子句中

3、group by子句支持单个字段分组，多个字段分组(多个字段之间用逗号隔开没有顺序要求),表达式或函数(使用较少)

4、也可以添加排序(排序放在整个分组查询的最后)

HAVING 非常类似于 WHERE 。事实上，所有类型的 WHERE 子句都可以用 HAVING 来替代。唯一的差别是 WHERE 过滤行，而 HAVING 过滤分组。

**WHERE子句的条件（包括通配符条件和带多个操作符的子句）。**

**所学过的有关 WHERE 的所有这些技术和选项都适用于 HAVING 。它们的句法是相同的，只是关键字有差别。**

**WHERE 在数据分组前进行过滤， HAVING 在数据分组后进行过滤。这是一个重要的区别。**

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697693895853-4d5a27bc-f105-4b78-97c9-05526b3eb65a.png" alt="Snipaste_2023-10-19_13-38-03" style="zoom:67%;" />

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697693968373-d840dacb-ed7d-4e39-8038-140d96b0199b.png" alt="Snipaste_2023-10-19_13-39-15" style="zoom: 67%;" />

案例

```sql
#引入:查询每个部门的平均工资
SELECT AVG(salary) FROM employees;

#案例1:查询每个工种的最高工资
SELECT MAX(salary),job_id FROM employees 
GROUP BY job_id;


#案例2:查询每个位置上的部门个数
SELECT COUNT(*),location_id
FROM departments
GROUP BY location_id;

#添加筛选条件
#案例1:查询邮箱中包含a字符的，每个部门的平均工资
SELECT AVG(salary),department_id FROM employees
WHERE email LIKE '%a%' GROUP BY department_id;

#案例2:查询有奖金的每个领导手下员工的最高工资
SELECT MAX(salary),manager_id FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;

#添加复杂的筛选条件
#案例1:查询哪个部门的员工个数>2
#1.查询每个部门的员工个数
SELECT COUNT(*),department_id FROM employees
GROUP BY department_id;

#2.根据1的结果进行筛选，查询哪个部门的员工个数大于2
SELECT COUNT(*),department_id FROM employees
GROUP BY department_id HAVING COUNT(*)>2;


#案例2:查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资 
#1.查询每个工种有奖金的员工的最高工资 
SELECT MAX(salary),job_id FROM employees 
WHERE commission_pct IS NOT NULL GROUP BY job_id; 

#2.根据结果继续筛选，最高工资>12000 
 
SELECT MAX(salary), job_id FROM employees 
WHERE commission_pct IS NOT NULL GROUP BY job_id 
HAVING MAX(salary)>12000; 

#按表达式或函数分组

#案例:按员工姓名的长度分组,查询每一组的员工个数,筛选员工个数>5

#1.查询每个长度的员工个数 
SELECT COUNT(*),LENGTH(last_name) len_name 
FROM employees GROUP BY LENGTH(last_name); 

#2.添加筛选条件
SELECT COUNT(*),LENGTH(last_name) len_name 
FROM employees GROUP BY len_name HAVING COUNT(*)>5;
#别名
SELECT COUNT(*) c,LENGTH(last_name) len_name 
FROM employees GROUP BY len_name HAVING c>5;

#按多个字段查询
#案例:查询每个部门每个工种的员工的平均工资

SELECT AVG(salary),department_id,job_id
FROM employees GROUP BY department_id,job_id;

#添加排序
#案例:查询每个部门每个工种的员工的平均工资,按平均工资的高低查询

SELECT AVG(salary),department_id,job_id
FROM employees GROUP BY department_id,job_id
ORDER BY AVG(salary) DESC;

#查询员工最高工资和最低工资的差距 (DIFFERENCE)
SELECT MAX(salary)-MIN(salary) DIFFERENCE
FROM employees 
```

## 连接查询（多表查询）

含义：又称多表查询,当查询的数据来自多个表时,就会用到连接查询

连接表示多表合成一张表，列的字段名可以相同（如果冲突需要为表起别名）

**由没有联结条件的表关系返回的结果为笛卡儿积。**

**检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。**

**笛卡尔乘积现象：表1 有m行，表2有n行，结果=m*n行**

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680151227492-f6f68732-bd2b-4ced-b0b2-68b16d67acbd.png" alt="img" style="zoom:50%;" />

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680151227561-e71be16c-4b9e-43f8-b9c7-65e9c6e8bd2b.png" alt="img" style="zoom:50%;" />

笛卡尔乘积发生原因：没有有效的连接条件

如何避免：添加有效的连接条件

**应该保证所有连接都有 WHERE 子句，否则 MySQL 将返回比想要的数据多得多的数据。**

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680151227441-0ae4d731-e0ea-4d74-a1e6-c520f2a44491.png" alt="img" style="zoom:50%;" />

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680151227398-0ebca3a5-dc5e-491d-92ee-ab1d3f53335e.png" alt="img" style="zoom:50%;" />

连接查询分类

按年代分类：

- sql92 标准：仅支持内连接
- sql99 标准【推荐】：支持内连接+外连接（左外和右外）+交叉连接

按功能分类：

- 内连接

  1. 等值连接 

     使用 = 号的连接，基于两个表之间的相等测试

     ![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1680153577853-3ea5a503-f02b-449a-8f88-15b227702596.png)

  2. 非等值连接

     不使用 = 号的连接

     ![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1680153577962-66bd323e-e1fe-476a-b58e-7968603b3fd0.png)

     查询员工的工资和工资级别

     <img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680151228872-8dd0b7b2-3cc0-4439-ab16-bb927f49074a.png" alt="img"  />

  3. 自连接

     FROM 相同的表1 相同的表2 WHERE

     ![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1680153577878-c540fbaf-c841-4a23-b0e7-f9d167313cdf.png)

     查询员工名和上级的名称

     <img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680151228099-5f10aec0-c0c5-4458-a548-3bcd00ccedc1.png" alt="img"  />

     先从 employees表 中找到员工名以及他所对应的领导编号 
     
     然后再通过领导编号找到领导名 (相当于要查询两次employees表,所以将一张表起两个别名,当作两张表来操作)
     
     <img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680151471810-93601075-8356-424a-94f9-42c79864cd22.png" alt="img"  />

- 外连接：查询一个表中有，另一个表没有的记录

  1. 左外连接

  2. 右外连接

  3. 全外连接
	
- 交叉连接：99 语法实现的笛卡尔乘积

### sql92 和 sql99 对比

- 功能：sql99 支持的较多
- 可读性：sql99 实现连接条件和筛选条件的分离，可读性较高

### sql92 标准案例 (不推荐)

内连接案例

```sql
#1、等值连接

/*
1.多表等值连接的结果为多表的交集部分（使用 = 号连接表）
2.n表连接，至少需要n-1个连接条件
3.多表的顺序没有要求
4.一般需要为表起别名
5.都可以搭配前面介绍的所有子句使用，比如排序、分组、筛选（所有连接都可以）
*/

#案例1：查询女神名和对应的男神名
SELECT NAME,boyName FROM boys,beauty
WHERE beauty.boyfriend_id= boys.id;

#案例2：查询员工名和对应的部门名

SELECT last_name,department_name 
FROM employees,departments
WHERE employees.`department_id`=departments.`department_id`;

#2、为表起别名
/*
1.提高语句的简洁度
2.区分多个重名的字段

注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定，因为先执行 FROM employees  e,jobs j
*/
#查询员工名、工种号、工种名
SELECT e.last_name,e.job_id,j.job_title
FROM employees  e,jobs j
WHERE e.`job_id`=j.`job_id`;

#3、两个表的顺序是否可以调换
#查询员工名、工种号、工种名

SELECT e.last_name,e.job_id,j.job_title
FROM jobs j,employees e
WHERE e.`job_id`=j.`job_id`;

#4、可以加筛选
#案例：查询有奖金的员工名、部门名

SELECT last_name,department_name,commission_pct
FROM employees e,departments d
WHERE e.`department_id`=d.`department_id`
AND e.`commission_pct` IS NOT NULL;

#案例2：查询城市名中第二个字符为o的部门名和城市名

SELECT department_name,city
FROM departments d,locations l
WHERE d.`location_id` = l.`location_id`
AND city LIKE '_o%';

#5、可以加分组
#案例1：查询每个城市的部门个数

SELECT COUNT(*) 个数,city
FROM departments d,locations l
WHERE d.`location_id`=l.`location_id`
GROUP BY city;

#案例2：查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
SELECT department_name,d.`manager_id`,MIN(salary)
FROM departments d,employees e
WHERE d.`department_id`=e.`department_id`
AND commission_pct IS NOT NULL
GROUP BY department_name,d.`manager_id`;

#6、可以加排序
#案例：查询每个工种的工种名和员工的个数，并且按员工个数降序

SELECT job_title,COUNT(*)
FROM employees e,jobs j
WHERE e.`job_id`=j.`job_id`
GROUP BY job_title
ORDER BY COUNT(*) DESC;

#7、可以实现三表连接
#案例：查询员工名、部门名和所在的城市

SELECT last_name,department_name,city
FROM employees e,departments d,locations l
WHERE e.`department_id`=d.`department_id`
AND d.`location_id`=l.`location_id`
AND city LIKE 's%'
ORDER BY department_name DESC;

#2、非等值连接
#案例1：查询员工的工资和工资级别

SELECT salary,grade_level
FROM employees e,job_grades g
WHERE salary BETWEEN g.`lowest_sal` AND g.`highest_sal`
AND g.`grade_level`='A';

#3、自连接（同一张表当成多张表使用）
#案例：查询 员工名和上级的名称

SELECT e.employee_id,e.last_name,m.employee_id,m.last_name
FROM employees e,employees m
WHERE e.`manager_id`=m.`employee_id`;
```

### sql99 标准案例(推荐)

语法

```
select 查询列表
from 表1 别名 
【连接类型】 join 表2 别名 
on 连接条件
【where 筛选条件】
【group by 分组】
【having 筛选条件】
【order by 排序列表】
```

分类

```
内连接（★重点）：inner
外连接
	左外(★重点): left 【outer】
	右外(★重点)：right 【outer】
	全外：full【outer】
交叉连接：cross 
```

查询男朋友不在男神表的的女神名

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1680160891386-f034a3e0-21bd-43f1-ab1f-becf0c56f587.png)

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680162650000-f188fd7c-c8ce-411c-a6dc-dce0f68174d0.png" alt="img" style="zoom: 80%;" />

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1680162650230-2b5037a5-fe13-4b4f-a337-4ae7bd7da652.png" alt="img" style="zoom:80%;" />

案例

```sql
#一、内连接
#只连接连接条件匹配的行
/*
语法：

select 查询列表
from 表1 别名
inner join 表2 别名
on 连接条件;

分类：
等值
非等值
自连接

特点：
①添加排序、分组、筛选
②inner可以省略
③筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读
④inner join连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集

*/

#1、等值连接
#案例1.查询员工名、部门名

SELECT last_name,department_name FROM departments d
INNER JOIN  employees e
ON e.`department_id` = d.`department_id`;

#案例2.查询名字中包含e的员工名和工种名（添加筛选）
SELECT last_name,job_title FROM employees e
INNER JOIN jobs j ON e.`job_id`=  j.`job_id`
WHERE e.`last_name` LIKE '%e%';

#案例3.查询部门个数>3的城市名和部门个数，（添加分组+筛选）

#1.查询每个城市的部门个数
#2.在1结果上筛选满足条件的
SELECT city,COUNT(*) 部门个数
FROM departments d
INNER JOIN locations l
ON d.`location_id`=l.`location_id`
GROUP BY city
HAVING COUNT(*)>3;

#案例4.查询哪个部门的员工个数>3的部门名和员工个数，并按个数降序（添加排序）

#1.查询每个部门的员工个数
SELECT COUNT(*),department_name
FROM employees e
INNER JOIN departments d
ON e.`department_id`=d.`department_id`
GROUP BY department_name;

#2.在1结果上筛选员工个数>3的记录，并排序

SELECT COUNT(*) 个数,department_name
FROM employees e
INNER JOIN departments d
ON e.`department_id`=d.`department_id`
GROUP BY department_name
HAVING COUNT(*)>3
ORDER BY COUNT(*) DESC;

#案例5.查询员工名、部门名、工种名，并按部门名降序（添加三表连接）

SELECT last_name,department_name,job_title
FROM employees e
INNER JOIN departments d ON e.`department_id`=d.`department_id`
INNER JOIN jobs j ON e.`job_id` = j.`job_id`
ORDER BY department_name DESC;

#二、非等值连接

# join 和 inner join是一样的，为了简写省掉 inner 可直接写为 join

#查询员工的工资级别

SELECT salary,grade_level
FROM employees e
JOIN job_grades g
ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`;
 
#查询工资级别的个数>20的个数，并且按工资级别降序
SELECT COUNT(*),grade_level
FROM employees e
JOIN job_grades g
ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`
GROUP BY grade_level
HAVING COUNT(*)>20
ORDER BY grade_level DESC;

#三、自连接（同一张表当成多张表使用）
 
#查询员工的名字、上级的名字
SELECT e.last_name 员工名,m.last_name 上级名称 
FROM employees e
INNER JOIN employees m
ON e.`manager_id`= m.`employee_id`;
 
#查询姓名中包含字符k的员工的名字、上级的名字
SELECT e1.last_name 员工名,e2.last_name 上级名称 
FROM employees e1
INNER JOIN employees e2 
ON e1.manager_id = e2.employee_id 
WHERE e1.last_name LIKE "%k%"

#二、外连接
 
/*
应用场景：用于查询一个表（主表）中有，另一个表（从表）没有的记录
 
特点：
1、外连接的查询结果为主表中的所有记录
	如果从表中有和它匹配的，则显示匹配的值
	如果从表中没有和它匹配的，则显示null
	外连接查询结果=内连接结果+主表中有而从表没有的记录
	查出主表所有数据以及主表和从表有交集的数据
2、左外连接，left join  左边的是主表  保留左表所有的行，即使在右表中没有匹配的行；以及右边表中全部匹配的行。
   右外连接，right join 右边的是主表  保留右表所有的行，即使在左表中没有匹配的行；以及左边表中全部匹配的行。
   LEFT JOIN 是额外返回左表中没有关联上的数据。（额外返回左表上没有右表的数据）
   RIGHT JOIN 是额外返回右表（主表）中没有关联上的数据。（额外返回右表上没有左表的数据）
3、左外和右外交换两个表的顺序，可以实现同样的效果 
4、全外连接=内连接的结果+表1中有但表2没有的+表2中有但表1没有的，mysql 不支持

left join 是 left outer join 的缩写，所以作用是一样的。
*/
#引入：查询男朋友不在男神表的的女神名

SELECT * FROM beauty;
SELECT * FROM boys;
 
#左外连接
SELECT b.name FROM beauty b
LEFT OUTER JOIN boys bo
ON b.`boyfriend_id` = bo.`id`
WHERE bo.`id` IS NULL;
 
#案例1：查询哪个部门没有员工
#左外
SELECT d.*,e.employee_id
FROM departments d
LEFT OUTER JOIN employees e
ON d.`department_id` = e.`department_id`
WHERE e.`employee_id` IS NULL;

#右外
 
SELECT d.*,e.employee_id
FROM employees e
RIGHT OUTER JOIN departments d
ON d.`department_id` = e.`department_id`
WHERE e.`employee_id` IS NULL;

# 案例2：查询哪个城市没有部门
SELECT city,d.* 
FROM locations l
LEFT JOIN departments d 
ON l.location_id = d.location_id 
WHERE department_id IS NULL

# 案例3：查询部门名为SAL或IT的员工信息
SELECT d.department_id 部门编号,department_name 部门名,e.* 
FROM departments d
LEFT JOIN employees e 
ON d.department_id = e.department_id 
WHERE department_name IN ("SAL","IT")

#全外 包含左、右两个表的全部行，不管另外一边的表中是否存在与它们匹配的行;         

USE girls;
SELECT b.*,bo.* FROM beauty b
FULL OUTER JOIN boys bo
ON b.`boyfriend_id` = bo.id;

#交叉连接 生成笛卡尔积，它不使用任何匹配或者选取条件，而是直接将一个数据源中的每个行与另一个数据源的每个行都一一匹配(开发中避免使用);

SELECT b.*,bo.* FROM beauty b
CROSS JOIN boys bo;
```

## 子查询

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1681645559080-4b43cdae-d63d-4a29-899e-f4742dbaacb3.png" alt="img" style="zoom:50%;" />

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681645558914-ec51dddf-43a7-4e48-8b95-c5b5cbd05f20.png)

含义：

**出现在其他语句中的select语句,称为子查询或内查询**

**外部的查询语句，称为主查询或外查询。**

**在 SELECT 语句中，子查询总是从内向外处理。**

**在 WHERE 子句中使用子查询能够编写出功能很强并且很灵活的 SQL 语句。**

**对于能嵌套的子查询的数目没有限制，不过在实际使用时由于性能的限制，不能嵌套太多的子查询。**

订单存储在两个表中。对于包含订单号、客户ID、订单日期的每个订单， orders 表存储一行。

各订单的物品存储在相关的 orderitems 表中。 orders 表不存储客户信息。

它只存储客户的 ID。实际的客户信息存储在 customers 表中。

现在，假如需要列出订购物品 TNT2 的所有客户，应该怎样检索？下面列出具体的步骤。

(1) 检索包含物品 TNT2 的所有订单的编号。

(2) 检索具有前一步骤列出的订单编号的所有客户的 ID。

(3) 检索前一步骤返回的所有客户 ID 的客户信息。

上述每个步骤都可以单独作为一个查询来执行。

可以把一条 SELECT 语句返回的结果用于另一条 SELECT 语句的 WHERE 子句。

也可以使用子查询来把 3 个查询组合成一条语句。

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697695633718-661bc86d-a56d-4989-b723-a7f6209e6581.png" alt="Snipaste_2023-10-19_14-07-07" style="zoom: 80%;" />

分析：

![Snipaste_2023-10-19_14-08-44](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697695744559-4d37f479-4ae8-4088-9515-cef9e95e1b4b.png)

![Snipaste_2023-10-19_14-08-52](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697695760001-ab0219f2-581c-4e3c-bb43-4cabb27bf825.png)

**包含子查询的 SELECT 语句难以阅读和调试，特别是它们较为复杂时更是如此。**

**如上所示把子查询分解为多行并且适当地进行缩进，能极大地简化子查询的使用。**

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697698199259-7197bea3-15a7-45e6-8477-716ad16cb927.png" alt="Snipaste_2023-10-19_14-49-24" style="zoom:50%;" />

```sql
#一、where或having后面
/*
1、标量子查询（单行子查询）
2、列子查询（多行子查询）
3、行子查询（多列多行）

特点：
①子查询放在小括号内
②子查询一般放在条件的右侧
③标量子查询，一般搭配着单行操作符使用
> < >= <= = <>

列子查询，一般搭配着多行操作符使用
in、any/some、all

④子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果
*/

#1.标量子查询★

#案例1：谁的工资比 Abel 高?

#①查询Abel的工资
SELECT salary
FROM employees
WHERE last_name = 'Abel';

#②查询员工的信息，满足 salary>①结果
SELECT *
FROM employees
WHERE salary>(
	SELECT salary
	FROM employees
	WHERE last_name = 'Abel'
);

#案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id 和工资

#①查询141号员工的job_id
SELECT job_id FROM employees
WHERE employee_id = 141;

#②查询143号员工的salary
SELECT salary FROM employees
WHERE employee_id = 143;

#③查询员工的姓名，job_id 和工资，要求job_id=①并且salary>②

SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
	SELECT job_id
	FROM employees
	WHERE employee_id = 141
) AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id = 143
);

#案例3：返回公司工资最少的员工的last_name,job_id和salary

#①查询公司的最低工资
SELECT MIN(salary) FROM employees;

#②查询last_name,job_id和salary，要求salary=①
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);

#案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资

#①查询50号部门的最低工资
SELECT  MIN(salary)
FROM employees
WHERE department_id = 50;

#②查询每个部门的最低工资

SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id;

#③ 在②基础上筛选，满足min(salary)>①
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  MIN(salary)
	FROM employees
	WHERE department_id = 50
);

#非法使用标量子查询

SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  salary
	FROM employees
	WHERE department_id = 250
);

#2.列子查询（多行子查询）★
#案例1：返回location_id是1400或1700的部门中的所有员工姓名

#①查询location_id是1400或1700的部门编号
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700);

#②查询员工姓名，要求部门号是①列表中的某一个

SELECT last_name
FROM employees
WHERE department_id=ANY(
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)
);


#案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary

#①查询job_id为‘IT_PROG’部门任一工资

SELECT DISTINCT salary FROM employees
WHERE job_id = 'IT_PROG';

#②查询员工号、姓名、job_id 以及salary，salary<(①)的任意一个
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ANY(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MAX(salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';


#案例3：返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工   的员工号、姓名、job_id 以及salary

SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#或

SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MIN( salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#3、行子查询（结果集一行多列或多行多列）

#案例：查询员工编号最小并且工资最高的员工信息

SELECT * FROM employees
WHERE (employee_id,salary)=(
	SELECT MIN(employee_id),MAX(salary)
	FROM employees
);

#①查询最小的员工编号
SELECT MIN(employee_id) FROM employees;

#②查询最高工资
SELECT MAX(salary) FROM employees;

#③查询员工信息
SELECT * FROM employees
WHERE employee_id=(
	SELECT MIN(employee_id)
	FROM employees
)AND salary=(
	SELECT MAX(salary)
	FROM employees
);


#二、select后面
/*
仅仅支持标量子查询
*/

#案例：查询每个部门的员工个数

SELECT d.*,(
	SELECT COUNT(*)
	FROM employees e
	WHERE e.department_id = d.`department_id`
 ) 个数
 FROM departments d;
 
 
#案例2：查询员工号=102的部门名
 
SELECT (
	SELECT department_name,e.department_id
	FROM departments d
	INNER JOIN employees e
	ON d.department_id=e.department_id
	WHERE e.employee_id=102
	
) 部门名;

#三、from后面
/*
将子查询结果充当一张表，要求必须起别名
*/

#案例：查询每个部门的平均工资的工资等级
#①查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees GROUP BY department_id;

SELECT * FROM job_grades;

#②连接①的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal

SELECT  ag_dep.*,g.`grade_level`
FROM (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep
INNER JOIN job_grades g
ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;

#四、exists后面（相关子查询）
/*
语法：
exists(完整的查询语句)
结果：
1或0
*/
SELECT EXISTS(SELECT employee_id FROM employees WHERE salary=300000);

#案例1：查询有员工的部门名

#in
SELECT department_name
FROM departments d
WHERE d.`department_id` IN(
	SELECT department_id
	FROM employees
);

#exists
SELECT department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE d.`department_id`=e.`department_id`
);

#案例2：查询没有女朋友的男神信息

#in
SELECT bo.*
FROM boys bo
WHERE bo.id NOT IN(
	SELECT boyfriend_id
	FROM beauty
);

#exists
SELECT bo.*
FROM boys bo
WHERE NOT EXISTS(
	SELECT boyfriend_id
	FROM beauty b
	WHERE bo.`id`=b.`boyfriend_id`
);
```

## 分页查询 (LIMIT)

**分页查询的应用场景和特点**

```sql
应用场景：当要显示的数据,一页显示不全,需要分页提交sql请求

limit offset,size
offset：要显示条目的起始索引(起始索引从0开始) 若为0可以省略
size：要显示的条目个数

特点：
1、 limit语句放在查询语句的最后面
2、 公式：
当要显示的页数为page, 每页的条目数为size
limit (page-1)*size , size

例如size=10
page   起始
0       0
1       10
2       20
1234567891011121314151617
```

练习一 

```sql
# 案例1：查询前五条员工信息
SELECT * FROM employees LIMIT 5
```

![在这里插入图片描述](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697702887925-7743521e-6187-48a1-9f1c-843f81720f31.png)

------

练习二 

```sql
# 案例2：查询第11条 - 第25条员工信息
SELECT * FROM employees LIMIT 10,15 
```

![在这里插入图片描述](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697702887944-bdf87746-776f-4e1f-af9c-600e891bd35e.png)

------

练习三 

```sql
# 案例3：查询有奖金的员工信息,并且工资较高的前10名显示出来
SELECT * FROM employees WHERE commission_pct IS NOT NULL 
ORDER BY salary DESC 
LIMIT 10
```

![在这里插入图片描述](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697702887969-cec9039b-8cf8-422f-a0b9-d7d153e4538c.png)

## 联合查询 (UNION)

```sql
联合查询的应用场景特点：

联合查询：将多条查询语句的结果合并为一个结果

语法：
查询语句1
union
查询语句2
union
查询语句3
.....

特点：
1、要求多条查询语句的查询列数是一致的
2、要求多条查询语句的每一列的类型和顺序最好一致
3、union 关键字默认去重，如果使用 union all 可以包含重复项
```

案例

```sql
#引入的案例：查询部门编号>90或邮箱包含a的员工信息

SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90;

SELECT * FROM employees  WHERE email LIKE '%a%'
UNION
SELECT * FROM employees  WHERE department_id>90;


#案例：查询中国用户中男性的信息以及外国用户中年男性的用户信息

SELECT id,cname,csex FROM t_ca WHERE csex='男'
UNION
SELECT t_id,tName,tGender FROM t_ua WHERE tGender='male';
```

此处需要的数据库源文件

```sql
/*
 Navicat MySQL Data Transfer

 Source Server         : localhost
 Source Server Type    : MySQL
 Source Server Version : 50145
 Source Host           : localhost:3306
 Source Schema         : test

 Target Server Type    : MySQL
 Target Server Version : 50145
 File Encoding         : 65001

 Date: 03/07/2020 11:08:32
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_ca
-- ----------------------------
DROP TABLE IF EXISTS `t_ca`;
CREATE TABLE `t_ca`  (
  `id` int(20) NOT NULL,
  `cname` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `csex` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- Records of t_ca
-- ----------------------------
INSERT INTO `t_ca` VALUES (1, '韩梅梅', '女');
INSERT INTO `t_ca` VALUES (2, '李雷', '男');
INSERT INTO `t_ca` VALUES (3, '李明', '男');

SET FOREIGN_KEY_CHECKS = 1;


/*
 Navicat MySQL Data Transfer

 Source Server         : localhost
 Source Server Type    : MySQL
 Source Server Version : 50145
 Source Host           : localhost:3306
 Source Schema         : test

 Target Server Type    : MySQL
 Target Server Version : 50145
 File Encoding         : 65001

 Date: 03/07/2020 11:09:05
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_ua
-- ----------------------------
DROP TABLE IF EXISTS `t_ua`;
CREATE TABLE `t_ua`  (
  `t_id` int(11) NOT NULL,
  `tName` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `tGender` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`t_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- Records of t_ua
-- ----------------------------
INSERT INTO `t_ua` VALUES (1, 'john', 'male');
INSERT INTO `t_ua` VALUES (2, 'lucy', 'female');
INSERT INTO `t_ua` VALUES (3, 'lily', 'female');
INSERT INTO `t_ua` VALUES (4, 'jack', 'male');
INSERT INTO `t_ua` VALUES (5, 'rose', 'female');

SET FOREIGN_KEY_CHECKS = 1;
```

##  插入 (INSERT)

方式一：经典的插入（推荐）

- 语法：INSERT INTO 表名(字段名,…) values(值,…);

- 特点

  - 1、要求值的类型和字段的类型要一致或兼容；
  - 2、字段的个数和顺序不一定与原始表中的字段个数和顺序一致但必须保证值和字段一一对应；
  - 3、假如表中有可以为 null 的字段，注意可以通过以下两种方式插入 null 值
    - ①字段和值都省略
    - ②字段写上，值使用 null

  - 4、字段和值的个数必须一致

  - 5、字段名可以省略，默认所有列

案例：

```sql
SELECT * FROM beauty;
#1.插入的值的类型要与列的类型一致或兼容
INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);

#2.不可以为null的列必须插入值。可以为null的列如何插入值？
    #方法一：
    INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
    VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);

    #方法二：
    INSERT INTO beauty(id,NAME,sex,phone)
    VALUES(15,'娜扎','女','1388888888');

#3.列的顺序是否可以调换
INSERT INTO beauty(NAME,sex,id,phone)
VALUES('蒋欣','女',16,'110');

#4.列数和值的个数必须一致

INSERT INTO beauty(NAME,sex,id,phone)
VALUES('关晓彤','女',17,'110');

#5.可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致

INSERT INTO beauty
VALUES(18,'张飞','男',NULL,'119',NULL,NULL);
```

方式二

语法：INSERT INTO 表名 SET 列名=值,列名=值,…

```sql
INSERT INTO beauty SET id=19,NAME='刘涛',phone='999';
```

两种方式的区别

```sql
1.方式一支持一次插入多行，语法如下：
insert into 表名【(字段名,..)】 values(值，..),(值，...),...;
2.方式一支持子查询，语法如下：
insert into 表名 查询语句;
    
#1、方式一支持插入多行,方式二不支持
#插入多行，使用逗号隔开
INSERT INTO beauty
VALUES(23,'唐艺昕1','女','1990-4-23','1898888888',NULL,2)
,(24,'唐艺昕2','女','1990-4-23','1898888888',NULL,2)
,(25,'唐艺昕3','女','1990-4-23','1898888888',NULL,2);

#2、方式一支持子查询，方式二不支持
#注意：values 没了
INSERT INTO beauty(id,NAME,phone) 
SELECT 26,'宋茜','11809866';

INSERT INTO beauty(id,NAME,phone) 
SELECT id,boyname,'1234567' FROM boys WHERE id<3;  
```

## 修改 (UPDATE)

### 修改单表的记录

- 语法：update 表名 set 列=新值,列=新值,… where 筛选条件;

案例

```sql
#1.修改单表的记录【★】
#案例1：修改beauty表中姓唐的女神的电话为13899888899
UPDATE beauty 
SET phone = '13899888899'
WHERE NAME LIKE '唐%';

#案例2：修改boys表中id好为2的名称为张飞，魅力值 10
UPDATE boys 
SET boyname='张飞',usercp=10
WHERE id=2;
```

### 修改多表的记录

- sql92语法：update 表1 别名,表2 别名 set 列=值,… where 连接条件 and 筛选条件;

- sql99语法：update 表1 别名 left|right|inner join 表2 别名 on 连接条件  set 字段=值,字段=值 【where 筛选条件】;

案例

```sql
#2.修改多表的记录【补充】
#案例 1：修改张无忌的女朋友的手机号为114
UPDATE boys bo
INNER JOIN beauty b 
ON bo.`id`=b.`boyfriend_id`
SET b.`phone`='119',bo.`userCP`=1000
WHERE bo.`boyName`='张无忌';

#案例2：修改没有男朋友的女神的男朋友编号都为2号
UPDATE boys bo
RIGHT JOIN beauty b 
ON bo.`id`=b.`boyfriend_id`
SET b.`boyfriend_id`=2 
WHERE bo.`id` IS NULL;
```

## 删除 (DELETE)

方式一：delete

语法

```sql
1、单表的删除【★】
delete from 表名 where 筛选条件

2、多表的删除【补充】

sql92语法：
delete 表1的别名,表2的别名
from 表1 别名,表2 别名
where 连接条件
and 筛选条件;

sql99语法：
delete 表1的别名,表2的别名
from 表1 别名
inner|left|right join 表2 别名 on 连接条件
where 筛选条件;
```

案例

```sql
#1.单表的删除
#案例：删除手机号以9结尾的女神信息
DELETE FROM beauty WHERE phone LIKE '%9';
SELECT * FROM beauty;

#2.多表的删除
#案例：删除张无忌的女朋友的信息
DELETE b
FROM beauty b
INNER JOIN boys bo 
ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName`='张无忌';

#案例：删除黄晓明的信息以及他女朋友的信息
DELETE b,bo
FROM beauty b
INNER JOIN boys bo 
ON b.`boyfriend_id`=bo.`id`
WHERE bo.`boyName`='黄晓明';
```

方式二：truncate

语法：truncate table 表名;

```sql
#案例：将魅力值>100的男神信息删除
TRUNCATE TABLE boys ;
```

两种方式的区别★

```java
1.delete 可以加 where 条件，truncate 不能加
2.truncate 删除，效率高一点
3.假如要删除的表中有自增长列，如果用 delete 删除后，再插入数据，自增长列的值从断点开始，而 truncate 删除后，再插入数据，自增长列的值从 1 开始。
4.truncate 删除没有返回值，delete 删除有返回值
5.truncate 删除不能回滚，delete 删除可以回滚（在事务中）
```

## 库的管理

### 创建库

```sql
#语法:
create database 【if not exists】 库名【 character set 字符集名】;

#案例：创建库Books
CREATE DATABASE IF NOT EXISTS books ;
```

### 修改库

```sql
#语法:
alter database 库名 character set 字符集名;

#案例：更改库的字符集
ALTER DATABASE books CHARACTER SET gbk;
```

### 删除库

```sql
#语法:
drop database 【if exists】 库名;

#案例：库的删除
DROP DATABASE IF EXISTS books;
```

## 表的管理

### 创建表

```sql
/*
语法：
create table 表名(
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	...
	列名 列的类型【(长度) 约束】
)
*/
#案例：创建表Book
CREATE TABLE book (
  id INT,
  #编号
  bName VARCHAR (20),
  #图书名
  price DOUBLE,
  #价格
  authorId INT,
  #作者编号
  publishDate DATETIME#出版日期
) ;
DESC book;

#案例：创建表author
CREATE TABLE IF NOT EXISTS author (
  id INT,
  au_n`author`ame VARCHAR (20),
  nation VARCHAR (10)
);
DESC author ;
```

### 修改表

```sql
#语法:
1.添加列
alter table 表名 add column 列名 类型 【first|after 字段名】;
2.修改列的类型或约束
alter table 表名 modify column 列名 新类型 【新约束】;
3.修改列名
alter table 表名 change column 旧列名 新列名 类型;
4 .删除列
alter table 表名 drop column 列名;
5.修改表名
alter table 表名 rename 【to】 新表名;

#案例：
#①修改列名
ALTER TABLE book CHANGE COLUMN publishdate pubDate DATETIME ;

#②修改列的类型或约束
ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;

#③添加新列
ALTER TABLE author ADD COLUMN annual DOUBLE; 

#④删除列
ALTER TABLE book_author DROP COLUMN  annual;

#⑤修改表名
ALTER TABLE book_author RENAME TO author;

DESC book;
```

### 删除表

```sql
#语法:
drop table【if exists】 表名;

#案例：
DROP TABLE IF EXISTS book_author;

SHOW TABLES;

#通用的写法：
DROP TABLE IF EXISTS 旧表名;
CREATE TABLE  表名();
#类似于
DROP DATABASE IF EXISTS 旧库名;
CREATE DATABASE 新库名 ;
```

### 复制表

```sql
#语法:
1、复制表的结构
create table 表名 like 旧表;
2、复制表的结构+数据
create table 表名 
select 查询列表 from 旧表【where 筛选】;

#案例
INSERT INTO author VALUES
(1,'村上春树','日本'),
(2,'莫言','中国'),
(3,'冯唐','中国'),
(4,'金庸','中国');

SELECT * FROM Author;
SELECT * FROM copy2;

#1.仅仅复制表的结构
CREATE TABLE copy LIKE author;

#2.复制表的结构+数据
CREATE TABLE copy2 SELECT * FROM author;

#只复制部分数据
CREATE TABLE copy3 SELECT id,au_name
FROM author WHERE nation='中国';

#仅仅复制某些字段
CREATE TABLE copy4 SELECT id,au_name
FROM author WHERE 0;
```

## 数据类型

### 数值型

整型

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697865358917-05843bdf-9290-409d-a575-644ba21b88a9.png" alt="20210225184506108" style="zoom:67%;" />

特点：

- 都可以设置无符号和有符号，默认有符号，通过 unsigned 设置无符号


- 如果超出了范围，会报 out of range 异常，插入临界值


- 长度可以不指定，默认会有一个长度


- 长度代表显示的最大宽度，如果不够则左边用 0 填充，但需要搭配 zerofill，并且默认变为无符号整型

案例：

```sql
DROP TABLE IF EXISTS tab_int ;
#1.如何设置无符号和有符号
CREATE TABLE tab_int (t1 INT, t2 INT UNSIGNED) ;
DESC tab_int ;

CREATE TABLE tab_int (t1 INT(7) ZEROFILL, t2 INT(7) ZEROFILL) ;
DESC tab_int ;

INSERT INTO tab_int VALUES (-123456) ;

INSERT INTO tab_int VALUES (-123456, -123456) ;

INSERT INTO tab_int VALUES (2147483648, 4294967296) ;

INSERT INTO tab_int VALUES (123, 123) ;

SELECT * FROM tab_int ;
```

浮点型

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697865370416-3b574461-7164-47e2-aa73-771c3865cc3d.png" alt="2021022518454880" style="zoom:67%;" />

定点数：dec(M,D)；decimal(M,D)

浮点数：float(M,D) 4；double(M,D) 

特点：

- M:代表整数部位+小数部位的个数  D:代表小数部位


- 如果超出范围，则报 out of range 异常，并且插入临界值


- M 和 D 都可以省略，但对于定点数，M 默认为10，D 默认为0


- 如果精度要求较高，则优先考虑使用定点数

案例：

```sql
#测试M和D
DROP TABLE tab_float ;

CREATE TABLE tab_float (f1 FLOAT, f2 DOUBLE, f3 DECIMAL) ;

SELECT * FROM tab_float ;

DESC tab_float ;

INSERT INTO tab_float VALUES(123.4523,123.4523,123.4523);
INSERT INTO tab_float VALUES(123.456,123.456,123.456);
INSERT INTO tab_float VALUES(123.4,123.4,123.4);
INSERT INTO tab_float VALUES(1523.4,1523.4,1523.4);

#原则：
#所选择的类型越简单越好，能保存数值的类型越小越好
```

### 字符型

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697865379479-49b7abdd-4432-417c-b8b6-234d392ab823.png" alt="20210225184829188" style="zoom:67%;" />

特点：

- char：固定长度的字符，写法为 char(M)           最大长度不能超过 M，其中 M 可以省略，默认为1

- varchar：可变长度的字符，写法为 varchar(M)  最大长度不能超过 M，其中 M 不可以省略


```
     写法			          M的意思						特点		 空间的耗费	 效率
char	char(M)		最大的字符数，可以省略，默认为1	 固定长度的字符	比较耗费	 高

varchar	varchar(M)	 最大的字符数，不可以省略		   可变长度的字符	   比较节省	    低
```
案例：

```sql
CREATE TABLE tab_char(
	c1 ENUM('a','b','c')
);

INSERT INTO tab_char VALUES('a');
INSERT INTO tab_char VALUES('b');
INSERT INTO tab_char VALUES('c');
INSERT INTO tab_char VALUES('m');
INSERT INTO tab_char VALUES('A');

SELECT * FROM tab_set;

CREATE TABLE tab_set(
	s1 SET('a','b','c','d')
);
INSERT INTO tab_set VALUES('a');
INSERT INTO tab_set VALUES('A,B');
INSERT INTO tab_set VALUES('a,c,d');
```

### 日期型

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1697865386429-613c6839-db25-4127-bb15-6c4f1f42c55c.png" alt="20210225184930618" style="zoom:67%;" />

分类：

- date只保存日期；
- time 只保存时间；
- year只保存年；
- datetime保存日期+时间；
- timestamp保存日期+时间

特点：

```
			字节		范围			时区等的影响
datetime	 8		1000——9999	       不受
timestamp	 4	    1970-2038	        受
```

案例：

```sql
CREATE TABLE tab_date(
	t1 DATETIME,
	t2 TIMESTAMP
);

INSERT INTO tab_date VALUES(NOW(),NOW());

SELECT * FROM tab_date;

SHOW VARIABLES LIKE 'time_zone';

SET time_zone='+9:00';
```

**datetime 和 timestamp 的区别**

```sql
1、
Timestamp 支持的时间范围较小，取值范围：19700101080001——2038年的某个时间
Datetime 的取值范围：1000-1-1 ——9999—12-31

2、
timestamp 和实际时区有关，更能反映实际的日期
而 datetime 则只能反映出插入时的当地时区

3、
timestamp 的属性受 Mysql 版本和 SQLMode 的影响很大
```

## 约束

含义：一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性。

分类

```sql
六大约束
	NOT NULL：非空，用于保证该字段的值不能为空
	比如姓名、学号等
	DEFAULT:默认，用于保证该字段有默认值
	比如性别
	PRIMARY KEY:主键，用于保证该字段的值具有唯一性，并且非空
	比如学号、员工编号等
	UNIQUE:唯一，用于保证该字段的值具有唯一性，可以为空
	比如座位号
	CHECK:检查约束【mysql中不支持】
	比如年龄、性别
	FOREIGN KEY:外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值
		在从表添加外键约束，用于引用主表中某列的值
	比如学生表的专业编号，员工表的部门编号，员工表的工种编号
```

添加约束的时机（添加数据前）：1.创建表时；2.修改表时

约束的添加分类：

- 列级约束：六大约束语法上都支持，但外键约束没有效果
- 表级约束：除了非空、默认，其他的都支持

语法

```sql
CREATE TABLE 表名{
	字段名 字段类型 列级约束,
	字段名 字段类型,
	表级约束
};
```

### 创建表时添加约束

```sql
#先新建一个库
CREATE DATABASE students;
```

1.添加列级约束

语法：直接在字段名和类型后面追加 约束类型即可。只支持：默认、非空、主键、唯一

案例

```sql
USE students;

DROP TABLE stuinfo;

CREATE TABLE stuinfo(
	id INT PRIMARY KEY,#主键
	stuName VARCHAR(20) NOT NULL UNIQUE,#非空
	gender CHAR(1) CHECK(gender='男' OR gender ='女'),#检查，mysql不支持
	seat INT UNIQUE,#唯一
	age INT DEFAULT  18,#默认约束
	majorId INT REFERENCES major(id)#外键
);

CREATE TABLE major(
	id INT PRIMARY KEY,
	majorName VARCHAR(20)
);

#查看stuinfo中的所有索引，包括主键、外键、唯一
SHOW INDEX FROM stuinfo;
```

2.添加表级约束

- 语法：语法：在各个字段的最下面 【constraint 约束名】 约束类型(字段名)

```sql
TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT,
	
	CONSTRAINT pk PRIMARY KEY(id),#主键
	CONSTRAINT uq UNIQUE(seat),#唯一键
	CONSTRAINT ck CHECK(gender ='男' OR gender  = '女'),#检查
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)#外键	
);

SHOW INDEX FROM stuinfo;
```

通用的写法

```sql
CREATE TABLE IF NOT EXISTS stuinfo(
	id INT PRIMARY KEY,
	stuname VARCHAR(20),
	sex CHAR(1),
	age INT DEFAULT 18,
	seat INT UNIQUE,
	majorid INT,
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)
);

create table 表名(
  	字段名 字段类型 not null,#非空
  	字段名 字段类型 primary key,#主键
  	字段名 字段类型 unique,#唯一
  	字段名 字段类型 default 值,#默认
  	constraint 约束名 foreign key(字段名) references 主表（被引用列）
);

注意：
  			   支持类型		      可以起约束名			
列级约束		除了外键		     不可以
表级约束		除了非空和默认	  可以，但对主键无效
  
列级约束可以在一个字段上追加多个，中间用空格隔开，没有顺序要求
```

主键和唯一的区别

```
		保证唯一性  是否允许为空                 一个表中可以有多少个      是否允许组合
主键		√		    ×			                 至多有1个           √，但不推荐
唯一		√		    √，但是只能有一个为 NULL 	   可以有多个          √，但不推荐
```

### 修改表时添加约束

语法

```sql
1、添加列级约束
alter table 表名 modify column 字段名 字段类型 新约束;

2、添加表级约束
alter table 表名 add 【constraint 约束名】 约束类型(字段名) 【外键的引用】;
```

案例

```sql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT
);

DESC stuinfo;
#1.添加非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20)  NOT NULL;

#2.添加默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;

#3.添加主键
    #①列级约束
    ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
    #②表级约束
    ALTER TABLE stuinfo ADD PRIMARY KEY(id);

#4.添加唯一
    #①列级约束
    ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
    #②表级约束
    ALTER TABLE stuinfo ADD UNIQUE(seat);

#5.添加外键
ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id); 
```

### 修改表时删除约束

```sql
#1.删除非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;

#2.删除默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT ;

#3.删除主键
ALTER TABLE stuinfo DROP PRIMARY KEY;

#4.删除唯一
ALTER TABLE stuinfo DROP INDEX seat;

#5.删除外键
ALTER TABLE stuinfo DROP FOREIGN KEY fk_stuinfo_major;

SHOW INDEX FROM stuinfo;
```

修改表时添加或删除约束的语法总结

```sql
1、非空
添加非空
alter table 表名 modify column 字段名 字段类型 not null;
删除非空
alter table 表名 modify column 字段名 字段类型 ;

2、默认
添加默认
alter table 表名 modify column 字段名 字段类型 default 值;
删除默认
alter table 表名 modify column 字段名 字段类型 ;

3、主键
添加主键
alter table 表名 add【 constraint 约束名】 primary key(字段名);
删除主键
alter table 表名 drop primary key;

4、唯一
添加唯一
alter table 表名 add【 constraint 约束名】 unique(字段名);
删除唯一
alter table 表名 drop index 索引名;

5、外键
添加外键
alter table 表名 add【 constraint 约束名】 foreign key(字段名) references 主表（被引用列）;
删除外键
alter table 表名 drop foreign key 约束名;
```

## 自增长列(标识列)

含义：可以不用手动的插入值，系统提供默认的序列值

语法

```sql
一、创建表时设置自增长列
create table 表(
	字段名 字段类型 约束 auto_increment
);

二、修改表时设置自增长列
alter table 表 modify column 字段名 字段类型 约束 auto_increment;

三、删除自增长列
alter table 表 modify column 字段名 字段类型 约束;
```

特点

```
1.不用手动插入值，可以自动提供序列值，默认从 1 开始，步长为 1
  auto_increment_increment
  如果要更改起始值：手动插入值
  如果要更改步长：更改系统变量
  set auto_increment_increment=值;
2.一个表至多有一个自增长列
3.自增长列只能支持数值型
4.自增长列必须为一个key（主键、外键、唯一键）
```

案例

```sql
#一、创建表时设置标识列
DROP TABLE IF EXISTS tab_identity;

CREATE TABLE tab_identity(
	id INT  ,
	NAME FLOAT UNIQUE AUTO_INCREMENT,
	seat INT 
) TRUNCATE TABLE tab_identity;

INSERT INTO tab_identity(id,NAME) VALUES(NULL,'john');

INSERT INTO tab_identity(NAME) VALUES('lucy');

SELECT * FROM tab_identity;
#查看自动增长的值
SHOW VARIABLES LIKE '%auto_increment%';

SET auto_increment_increment=3;
```

## 事务

含义：一个或一组 sql 语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行。

```
案例：转账

张三丰  1000
郭襄	1000

update 表 set 张三丰的余额=500 where name='张三丰'
意外
update 表 set 郭襄的余额=1500 where name='郭襄'
```

事务的特性(ACID)

原子性：一个事务不可再分割，要么都执行要么都不执行

一致性：一个事务执行会使数据从一个一致状态切换到另外一个一致状态

隔离性：一个事务的执行不受其他事务的干扰

持久性：一个事务一旦提交，则会永久的改变数据库的数据.

事务的使用步骤 ★

隐式（自动）事务：没有明显的开启和结束，本身就是一条事务可以自动提交，比如insert、update、delete

```sql
隐式事务：事务没有明显的开启和结束的标记

比如 insert、update、delete语句
delete from 表 where id =1;
```

显式事务：事务具有明显的开启和结束的标记；前提：必须先设置自动提交功能为禁用

```sql
显式事务：事务具有明显的开启和结束的标记
前提：必须先设置自动提交功能为禁用

set autocommit=0;

步骤1：开启事务
set autocommit=0;
start transaction;可选的

步骤2：编写事务中的 sql 语句(select insert update delete)
语句1;
语句2;
...

步骤3：结束事务
commit;提交事务
rollback;回滚事务
```

案例

```sql
SHOW VARIABLES LIKE 'autocommit';
SHOW ENGINES;

#1.演示事务的使用步骤
DROP TABLE IF EXISTS account;
CREATE TABLE account(
	id INT PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(20),
	balance DOUBLE
);
INSERT INTO account(username,balance)
VALUES('张无忌',1000),('赵敏',1000);

#1、开启事务
SET autocommit=0;
START TRANSACTION;
#2、编写一组事务的语句
UPDATE account SET balance = 1000 WHERE username='张无忌';
UPDATE account SET balance = 1000 WHERE username='赵敏';

# 回滚事务
ROLLBACK;
# 3、提交，结束事务
#COMMIT;

SELECT * FROM account;
```

### 事务的隔离

并发事务

1、事务的并发问题是如何发生的？

多个事务同时操作 同一个数据库的相同数据时

2、并发问题都有哪些？

对于同时运行的多个事务, 当这些事务访问数据库中相同的数据时, 如果没有采取必要的隔离机制, 就会导致各种并发问题：

脏读：一个事务读取了其他事务还没有提交的数据，读到的是其他事务“更新”的数据；

不可重复读：一个事务多次读取，结果不一样；

幻读：一个事务读取了其他事务还没有提交的数据，只是读到的是 其他事务“插入”的数据。

![20210301142100325](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697888598576-3790576d-3866-47a1-b4ad-0c850713d400.png)

数据库事务的隔离性: 数据库系统必须具有隔离并发运行各个事务的能力, 使它们不会相互影响, 避免各种并发问题.

一个事务与其他事务隔离的程度称为隔离级别.，数据库规定了多种事务隔离级别, 

不同隔离级别对应不同的干扰程度, 隔离级别越高, 数据一致性就越好, 但并发性越弱

3、如何解决并发问题？

通过设置隔离级别来解决并发问题

4、隔离级别

![Snipaste_2023-10-21_19-55-38](https://cdn.nlark.com/yuque/0/2023/png/1614731/1697889348137-225e8b5f-c67e-48e7-9cf9-4d39c066d428.png)

| 事务隔离级别                  | 脏读 | 不可重复读 | 幻读 |
| ----------------------------- | ---- | ---------- | ---- |
| read uncommitted 读未提交     | √    | √          | √    |
| read committed     读已提交   | ×    | √          | √    |
| repeatable read    可重复读   | ×    | ×          | √    |
| serializable           串行化 | ×    | ×          | ×    |

```sql
mysql  中默认第三个隔离级别 repeatable read
oracle 中默认第二个隔离级别 read committed
mysql  支持 4 种事务隔离级别
oracle 支持 2 种事务隔离级别：READ COMMITED, SERIALIZABLE。

#查看隔离级别
mysql 8.0 以下：SELECT @@tx_isolation;
mysql 8.0 以上：SELECT @@transaction_isolation;

#设置隔离级别 session 设置当前 mysql 连接的隔离级别， global 设置数据库系统全局的隔离级别
set session|global transaction isolation level 隔离级别;
```

### 回滚点

savepoint 节点名;   设置保存点

```sql
#3.演示savepoint 的使用
SET autocommit=0;

START TRANSACTION;
DELETE FROM account WHERE id=25;
SAVEPOINT a;  #设置保存点
DELETE FROM account WHERE id=28;
ROLLBACK TO a;#回滚到保存点       id 28 的记录不会删除，id 25 的记录会删除

SELECT * FROM account;
```

## 视图

含义：虚拟表,和普通表一样使用。mysql5.1版本出现的新特性，是通过表动态生成的数据。

应用场景：多个地方用到同样的查询结果，该查询结果使用的 sql 语句较复杂

视图的好处

- 重用 sql  语句

- 简化复杂的 sql 操作，不必知道它的查询细节

- 保护数据，提高安全性

案例

```sql
SELECT stuname,majorname FROM stuinfo s
INNER JOIN major m ON s.`majorid`= m.`id`
WHERE s.`stuname` LIKE '张%';

#使用视图
CREATE VIEW v1 AS
SELECT stuname,majorname
FROM stuinfo s
INNER JOIN major m ON s.`majorid`= m.`id`;

SELECT * FROM v1 WHERE stuname LIKE '张%';
```

### 视图的创建

语法：create view 视图名 as 查询语句;

案例

```sql
USE myemployees;

#1.查询姓名中包含a字符的员工名、部门名和工种信息
#①创建
CREATE VIEW myv1 AS
SELECT last_name,department_name,job_title
FROM employees e
JOIN departments d ON e.department_id  = d.department_id
JOIN jobs j ON j.job_id  = e.job_id;

#②使用
SELECT * FROM myv1 WHERE last_name LIKE '%a%';

#2.查询各部门的平均工资级别
#①创建视图查看每个部门的平均工资
CREATE VIEW myv2 AS
SELECT AVG(salary) ag,department_id
FROM employees GROUP BY department_id;

#②使用视图
SELECT myv2.`ag`,g.grade_level FROM myv2
JOIN job_grades g
ON myv2.`ag` BETWEEN g.`lowest_sal` AND g.`highest_sal`;

#3.查询平均工资最低的部门信息
SELECT * FROM myv2 ORDER BY ag LIMIT 1;

#4.查询平均工资最低的部门名和工资
#①创建视图
CREATE VIEW myv3 AS
SELECT * FROM myv2 ORDER BY ag LIMIT 1;

#②使用视图
SELECT d.*,m.ag FROM myv3 m
JOIN departments d
ON m.`department_id`=d.`department_id`;
```

### 视图的修改

语法

```sql
方式一：
create or replace view 视图名 as 查询语句;
方式二：
alter view 视图名 as 查询语句;
```

案例

```sql
#方式一：
/*
create or replace view  视图名 as 查询语句;
*/
SELECT * FROM myv3 

CREATE OR REPLACE VIEW myv3 AS
SELECT AVG(salary),job_id
FROM employees GROUP BY job_id;

#方式二：
/*
语法：
alter view 视图名 as 查询语句;
*/
ALTER VIEW myv3 AS SELECT * FROM employees;
```

### 视图的删除

用户可以一次删除一个或者多个视图，前提是必须有该视图的drop权限。

语法：drop view 视图1，视图2,…;

案例

```sql
/*
语法：drop view 视图名,视图名,...;
*/
DROP VIEW emp_v1,emp_v2,myv3;
```

### 视图的查看

语法

```sql
desc 视图名;
show create view 视图名;
```

案例

```sql
DESC myv3;
SHOW CREATE VIEW myv3;
```

### 视图的更新

- 1.插入：insert
- 2.修改：update
- 3.删除：delete
- 4.查看：select

```sql
CREATE OR REPLACE VIEW myv1 AS
SELECT last_name,email,salary*12*(1+IFNULL(commission_pct,0)) "annual salary"
FROM employees;

CREATE OR REPLACE VIEW myv1 AS
SELECT last_name,email
FROM employees;

SELECT * FROM myv1;
SELECT * FROM employees;
#1.插入

INSERT INTO myv1 VALUES('张飞','zf@qq.com');

#2.修改
UPDATE myv1 SET last_name = '张无忌' WHERE last_name='张飞';

#3.删除
DELETE FROM myv1 WHERE last_name = '张无忌';
```

**注意：视图一般用于查询的，而不是更新的！**

所以具备以下特点的视图都不允许更新

- 包含以下关键字的 sq 语句：分组函数、distinct、group by、having、union 或者 union all
- 常量视图
- Select 中包含子查询
- join
- from 一个不能更新的视图
- where 子句的子查询引用了 from 子句中的表

```sql
#具备以下特点的视图不允许更新
#①包含以下关键字的sql语句：分组函数、distinct、group  by、having、union或者union all

CREATE OR REPLACE VIEW myv1
AS
SELECT MAX(salary) m,department_id
FROM employees
GROUP BY department_id;

SELECT * FROM myv1;

#更新
UPDATE myv1 SET m=9000 WHERE department_id=10;

#②常量视图
CREATE OR REPLACE VIEW myv2
AS
SELECT 'john' NAME;

SELECT * FROM myv2;

#更新
UPDATE myv2 SET NAME='lucy';

#③Select中包含子查询

CREATE OR REPLACE VIEW myv3
AS
SELECT department_id,(SELECT MAX(salary) FROM employees) 最高工资
FROM departments;

#更新
SELECT * FROM myv3;
UPDATE myv3 SET 最高工资=100000;

#④join
CREATE OR REPLACE VIEW myv4
AS
SELECT last_name,department_name
FROM employees e
JOIN departments d
ON e.department_id  = d.department_id;

#更新
SELECT * FROM myv4;
UPDATE myv4 SET last_name  = '张飞' WHERE last_name='Whalen';
INSERT INTO myv4 VALUES('陈真','xxxx');

#⑤from一个不能更新的视图
CREATE OR REPLACE VIEW myv5
AS SELECT * FROM myv3;

#更新
SELECT * FROM myv5;
UPDATE myv5 SET 最高工资=10000 WHERE department_id=60;

#⑥where子句的子查询引用了from子句中的表

CREATE OR REPLACE VIEW myv6
AS
SELECT last_name,email,salary
FROM employees
WHERE employee_id IN(
	SELECT  manager_id
	FROM employees
	WHERE manager_id IS NOT NULL
);

#更新
SELECT * FROM myv6;
UPDATE myv6 SET salary=10000 WHERE last_name = 'k_ing';
```

### 视图和表的对比

|      | 创建语法的关键字 | 是否实际占用物理空间 | 使用                         |
| ---- | ---------------- | -------------------- | ---------------------------- |
| 视图 | create view      | 只是保存了 sql 逻辑  | 增删改查，只是一般不能增删改 |
| 表   | create table     | 保存了数据           | 增删改查                     |

## 变量

 变量的分类：

- 系统变量：
  - 全局变量
  - 会话变量
- 自定义变量：
  - 用户变量
  - 局部变量

### 系统变量

说明：变量由系统定义，不是用户定义，属于服务器层面

注意：全局变量需要添加global关键字，会话变量需要添加session关键字，如果不写，默认会话级别

使用步骤：

1、查看所有系统变量

```sql
show global|【session】variables;
```

2、查看满足条件的部分系统变量

```sql
show global|【session】 variables like '%char%';
```

3、查看指定的系统变量的值

```sql
select @@global|【session】系统变量名;
```

4、为某个系统变量赋值

```sql
方式一：
set global|【session】系统变量名=值;
方式二：
set @@global.|【session】系统变量名=值;
```

#### 全局变量

作用域：针对于所有会话（连接）有效，但不能跨重启，重启后失效

```sql
#查看所有全局变量
SHOW GLOBAL VARIABLES;

#查看满足条件的部分系统变量
SHOW GLOBAL VARIABLES LIKE '%char%';

#查看指定的系统变量的值
SELECT @@global.autocommit;

#为某个系统变量赋值
SET @@global.autocommit=0;
SET GLOBAL autocommit=0;
```

#### 会话变量

作用域：针对于当前会话（连接）有效

```sql
#查看所有会话变量
SHOW SESSION VARIABLES;

#查看满足条件的部分会话变量
SHOW SESSION VARIABLES LIKE '%char%';

#查看指定的会话变量的值
SELECT @@autocommit;
#mysql8.0以下
SELECT @@session.tx_isolation;
#mysql8.0以上
SELECT @@session.TRANSACTION_isolation;

#为某个会话变量赋值
SET @@session.tx_isolation='read-uncommitted';
SET SESSION tx_isolation='read-committed';
```

### 自定义变量

说明：变量由用户自定义，而不是系统提供的

使用步骤：
1、声明
2、赋值
3、使用（查看、比较、运算等）

#### 用户变量

作用域：针对于当前会话（连接）有效，作用域同于会话变量

```sql
#赋值操作符：=或:=
#①声明并初始化
SET @变量名=值;
SET @变量名:=值;
SELECT @变量名:=值;

#②赋值（更新变量的值）
#方式一：
SET @变量名=值;
SET @变量名:=值;
SELECT @变量名:=值;
#方式二：
SELECT 字段 INTO @变量名
FROM 表;
#③使用（查看变量的值）
SELECT @变量名;
```

案例

```sql
SET @a=10;
SET @b=20;

SET @sum=@a+@b;

SELECT @sum;
```

#### 局部变量

作用域：仅仅在定义它的 begin end 块中有效

应用在 begin end 中的第一句话

```sql
#①声明
DECLARE 变量名 类型;
DECLARE 变量名 类型 【DEFAULT 值】;


#②赋值（更新变量的值）
#方式一：
SET 局部变量名=值;
SET 局部变量名:=值;
SELECT 局部变量名:=值;
#方式二：
SELECT 字段 INTO 具备变量名
FROM 表;
#③使用（查看变量的值）
SELECT 局部变量名;
```

案例：声明两个变量，求和并打印

```sql
#用户变量
SET @m=1;
SET @n=1;
SET @sum=@m+@n;
SELECT @sum;

#局部变量
DECLARE m INT DEFAULT 1;
DECLARE n INT DEFAULT 1;
DECLARE SUM INT;
SET SUM=m+n;
SELECT SUM;
```

#### 用户变量和局部变量的对比

|          | 作用域                | 定义位置             | 语法                        |
| -------- | --------------------- | -------------------- | --------------------------- |
| 用户变量 | 当前会话              | 会话的任何地方       | 加 @ 符号，不用指定类型     |
| 局部变量 | 定义它的 BEGIN END 中 | BEGIN END 的第一句话 | 一般不用加 @ ，需要指定类型 |

## 存储过程


含义：一组预先编译好的SQL语句的集合，理解成批处理语句

好处：

1、提高代码的重用性

2、简化操作

3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

创建语法

```sql
CREATE PROCEDURE 存储过程名(参数列表)
BEGIN
	存储过程体（一组合法的SQL语句）
END


1、参数列表包含三部分
	  参数模式  参数名  参数类型
举例： in   stuname   varchar(20)


参数模式：
in：该参数可以作为输入，也就是该参数需要调用方传入值
out：该参数可以作为输出，也就是该参数可以作为返回值
inout：该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值
又可以返回值

2、如果存储过程体仅仅只有一句话，begin end可以省略
存储过程体中的每条sql语句的结尾要求必须加分号。
存储过程的结尾可以使用 delimiter 重新设置
语法：
delimiter 结束标记
案例：
delimiter $
```

调用语法

```sql
CALL 存储过程名(实参列表);

举例：
调用in模式的参数：call sp1（‘值’）;
调用out模式的参数：set @name; call sp1(@name);select @name;
调用inout模式的参数：set @name=值; call sp1(@name); select @name;
```

案例

```sql
#1.空参列表
#案例：插入到admin表中五条记录

SELECT * FROM admin;

DELIMITER $
CREATE PROCEDURE myp1()
BEGIN
	INSERT INTO admin(username,`password`) 
	VALUES('john1','0000'),('lily','0000'),('rose','0000'),('jack','0000'),('tom','0000');
END $

#调用
CALL myp1()$

#2.创建带in模式参数的存储过程

#案例1：创建存储过程实现 根据女神名，查询对应的男神信息

CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
BEGIN
	SELECT bo.*
	FROM boys bo
	RIGHT JOIN beauty b ON bo.id = b.boyfriend_id
	WHERE b.name=beautyName;
END $

#调用
CALL myp2('柳岩')$

#案例2 ：创建存储过程实现，用户是否登录成功

CREATE PROCEDURE myp3(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
BEGIN
	DECLARE result VARBINARY(20) DEFAULT '';#声明并初始化
	
	SELECT COUNT(*) INTO result#赋值
	FROM admin
	WHERE admin.username = username
	AND admin.password = PASSWORD;
	
	SELECT result;#使用
END $

CALL myp3('张飞','8888')$

CREATE PROCEDURE myp4(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
BEGIN
	DECLARE result INT DEFAULT 0;#声明并初始化
	
	SELECT COUNT(*) INTO result#赋值
	FROM admin
	WHERE admin.username = username
	AND admin.password = PASSWORD;
	
	SELECT IF(result>0,'成功','失败');#使用
END $

#调用
CALL myp4('张飞','8888')$


#3.创建out 模式参数的存储过程
#案例1：根据输入的女神名，返回对应的男神名

CREATE PROCEDURE myp6(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20))
BEGIN
	SELECT bo.boyname INTO boyname
	FROM boys bo
	RIGHT JOIN
	beauty b ON b.boyfriend_id = bo.id
	WHERE b.name=beautyName ;
	
END $

#案例2：根据输入的女神名，返回对应的男神名和魅力值

CREATE PROCEDURE myp7(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20),OUT usercp INT) 
BEGIN
	SELECT boys.boyname ,boys.usercp INTO boyname,usercp
	FROM boys 
	RIGHT JOIN
	beauty b ON b.boyfriend_id = boys.id
	WHERE b.name=beautyName ;
	
END $

#调用
CALL myp7('小昭',@name,@cp)$
SELECT @name,@cp$

#4.创建带inout模式参数的存储过程
#案例1：传入a和b两个值，最终a和b都翻倍并返回

CREATE PROCEDURE myp8(INOUT a INT ,INOUT b INT)
BEGIN
	SET a=a*2;
	SET b=b*2;
END $

#调用
SET @m=10$
SET @n=20$
CALL myp8(@m,@n)$
SELECT @m,@n$
```

查看语法

```sql
show create procedure 存储过程名;

#案例
DESC myp2;
SHOW CREATE PROCEDURE myp2;
```

删除语法

```sql
drop procedure 存储过程名;

#案例:
DROP PROCEDURE p1;
DROP PROCEDURE p2,p3;
```

## 函数

和存储过程相似

含义：一组预先编译好的SQL语句的集合，理解成批处理语句

好处：

1、提高代码的重用性

2、简化操作

3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

**函数与存储过程的区别**

存储过程：可以有0个返回，也可以有多个返回，适合做批量插入、批量更新

函数：有且仅有1 个返回，适合做处理数据后返回一个结果

创建语法

```sql
CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
BEGIN
	函数体
END

/*
注意：
1.参数列表 包含两部分：
参数名 参数类型
2.函数体：肯定会有return语句，如果没有会报错
如果return语句没有放在函数体的最后也不报错，但不建议

return 值;
3.函数体中仅有一句话，则可以省略begin end
4.使用 delimiter语句设置结束标记
*/

DELIMITER $
```

调用语法

```sql
SELECT 函数名(参数列表)
```

报错 This function has none of DETERMINISTIC, NO SQL…：解决

```sql
set global log_bin_trust_function_creators=TRUE;

如果mysqld重启，那个参数又会消失，因此记得在my.cnf配置文件中添加：
log_bin_trust_function_creators=1
```

案例

```sql
use employees $
#1.无参有返回
#案例：返回公司的员工个数
CREATE FUNCTION myf1() RETURNS INT
BEGIN	
	DECLARE c INT DEFAULT 0;
	SELECT COUNT(*) INTO c
	FROM employees;
	RETURN c;
END $

SELECT myf1()$


#2.有参有返回
#案例1：根据员工名，返回它的工资

CREATE FUNCTION myf2(empName VARCHAR(20)) RETURNS DOUBLE
BEGIN
	SET @sal=0;#定义用户变量 
	SELECT salary INTO @sal   #赋值
	FROM employees
	WHERE last_name = empName;
	
	RETURN @sal;
END $

SELECT myf2('kochhor') $

#案例2：根据部门名，返回该部门的平均工资

CREATE FUNCTION myf3(deptName VARCHAR(20)) RETURNS DOUBLE
BEGIN
	DECLARE sal DOUBLE ;
	SELECT AVG(salary) INTO sal
	FROM employees e
	JOIN departments d ON e.department_id = d.department_id
	WHERE d.department_name=deptName;
	RETURN sal;
END $

SELECT myf3('IT')$
```

查看函数

```sql
show create function 函数名;

SHOW CREATE FUNCTION myf3 $
```

删除函数

```sql
drop function 函数名;

DROP FUNCTION myf3 $
```

## 流程控制结构

- 顺序结构：程序从上往下依次执行
- 分支结构：程序按条件进行选择执行，从两条或多条路径中选择一条执行
- 循环结构：程序满足一定条件下，重复执行一组语句

### 分支结构

#### if 函数

- 语法：if(条件,值1，值2)；
- 功能：实现双分支；
- 应用：可以作为表达式放在任何位置

#### case 结构

语法：

```sql
情况1：类似于switch，一般用于实现等值判断。
语法：
case 变量或表达式
when 值1 then 语句1;
when 值2 then 语句2;
...
else 语句n;
end 

情况2：类似于多重if语句，一般用于实现区间判断。
语法：
case 
when 条件1 then 语句1;
when 条件2 then 语句2;
...
else 语句n;
end 
```

特点

- 可以作为表达式，嵌套在其他语句中使用。

- 可以放在任何地方，BEGIN END 中或 BEGIN END 的外面可以作为独立的语句去使用，

  只能放在 BEGIN END 中如果 WHEN 中的值满足或条件成立，则执行对应的 THEN 后面的语句，

  并且结束 CASE 如果都不满足，则执行 ELSE 中的语句或值。

- ELSE 可以省略，如果 ELSE 省略了，并且所有 WHEN 条件都不满足，则返回 NULL 。

位置

- 可以放在任何位置
- 如果放在 begin end 外面，作为表达式结合着其他语句使用
- 如果放在 begin end 里面，一般作为独立的语句使用

案例

```sql
#创建存储过程，根据传入的成绩，来显示等级，比如传入的成绩：90-100, 显示A，80-90，显示B，60-80，显示c，否则，显示D

CREATE PROCEDURE test_case (IN score INT) 
BEGIN 
	CASE 
	WHEN score>=90 AND score<=100 THEN SELECT 'A'; 
	WHEN score>=80 THEN SELECT 'B';
	WHEN score>=60 THEN SELECT 'C'; 
	ELSE SELECT 'D';
	END CASE; 
END $
CALL test_case(95)$
```

#### if 结构

语法：

```sql
if 条件1 then 语句1;
elseif 条件2 then 语句2;
....
else 语句n;
end if;
```

功能：类似于多重if；只能应用在begin end 中

案例

```sql
#案例：创建函数，实现传入成绩，如果成绩>90,返回A
#如果成绩>80,返回B，如果成绩>60,返回C，否则返回D

CREATE FUNCTION t_if(score INT) RETURNS CHAR
BEGIN
			IF score>90 AND score<=100 THEN RETURN 'A';
			ELSEIF score>80 THEN RETURN 'B';
			ELSEIF score>60 THEN RETURN 'C';
			ELSE RETURN 'D';
			END IF ;
END;


SELECT t_if(85);
```

```sql
#案例：创建存储过程，如果工资<2000,则删除
#如果5000>工资>2000,则涨工资1000，否则涨工资500
CREATE PROCEDURE p_if(IN sal DOUBLE)
BEGIN
			IF sal<2000 THEN DELETE FROM employees WHERE salary=sal;
			ELSEIF sal>2000 AND sal<5000 
					THEN 
					UPDATE employees 
					SET salary=salary+1000 
					WHERE salary=sal;
			ELSE  
					UPDATE employees 
					SET salary=salary+500 
					WHERE salary=sal;
			END IF;
END;

CALL p_if(2100);
```

```sql
#案例：创建函数，实现传入成绩，如果成绩>90,返回A
#如果成绩>80,返回B，如果成绩>60,返回C，否则返回D

CREATE FUNCTION t_case(score INT) RETURNS CHAR
BEGIN
			CASE	
			WHEN score>90 AND score<=100 THEN RETURN 'A';
			WHEN score>80 THEN RETURN 'B';
			WHEN score>60 THEN RETURN 'C';
			ELSE RETURN 'D';
			END CASE;
END;

SELECT t_case(95);
```

### 循环结构

分类：

while、loop、repeat

循环控制：

iterate类似于 continue，继续，结束本次循环，继续下一次

leave 类似于  break，跳出，结束当前所在的循环

<img src="https://article.biliimg.com/bfs/article/c3bcd40fe9b18655c6afe060af5c6722104215921.png" alt="img" style="zoom: 80%;" />

案例

没有添加循环控制语句

```sql
#批量插入，根据次数插入到admin表中多条记录
CREATE PROCEDURE p_while(IN cnt INT)
BEGIN
			DECLARE i INT DEFAULT 0;
			WHILE i<cnt DO
					INSERT INTO admin(username,password)  #插入语句
					VALUES(CONCAT("ttt",i),111);
					
					SET i=i+1; #i++
			
			END WHILE;
END;

CALL p_while(10);

/*
int i=1;
while(i<=insertcount){
	//插入
	i++;

}
*/

select * from admin $
```

添加 leave 语句

```sql
#案例：批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
CREATE PROCEDURE t_while1(IN cnt INT)
BEGIN 
			SET @i=0; #定义局部变量
			
			a:WHILE @i<cnt DO  #循环插入
					INSERT INTO admin(username,`password`) 
					VALUES(CONCAT("xxx",@i),222);
					
					SET @i=@i+1; #i++
					
					IF @i=20 THEN LEAVE a; #当i=20,结束循环
					END IF; 
					
			END WHILE a;
END;

# 测试插入100条数据,实则插入20条就停止
CALL t_while1(100);

select * from admin $
```

添加 iterate 语句

```sql
#案例：批量插入，根据次数插入到admin表中多条记录，只插入偶数次
CREATE PROCEDURE t_while2(IN cnt INT)
BEGIN
		DECLARE i INT DEFAULT -1;
			
		a:WHILE i<cnt DO
				SET i=i+1; #i++
				
				IF MOD(i,2)=1 THEN ITERATE a; #如果为奇数次,则跳过
				END IF;
				
				INSERT INTO admin(username,`password`) #偶数次则插入
				VALUES(CONCAT("bbb",i),333);
				
			END WHILE a;					
END;

CALL t_while2(20);

/*
int i=0;
while(i<=insertCount){
	i++;
	if(i%2==0){
		continue;
	}
	插入
}
*/

select * from admin $
```



















