---
title: mysql基础
copyright: true
date: 2018-12-21 16:16:13
tags: mysql基础
categories: mysql
---
>拿着我写好的文档给工作室成员上mysql课程
>被一些人吐槽我的文档写的很详细了，我完全可以不用讲
>也看到一些人我还在讲上面，他已经默默的看着文档做到最后了
>手动比一个无奈的表情 

## 概览
* 需要做的准备
* 数据库的基本操作
* 库的操作
* 表的操作

>感觉格式上有一点点乱，还需要多加练习:)
***
<!-- more -->

## 需要做的准备
**[+] 安装好mysql(xampp之类的都可以)**
**[+] 将mysql加至环境变量中(方便直接在命令行里使用mysql)**
**[+] 文中斜杠表示选择其一，而不是加上斜杠照打-.-**

***
## 数据库的基本操作

- 登陆(默认情况)

```sh
> mysql -u root -p	#默认没有密码
```

- 修改密码(shell模式下)

```sh
> mysqladmin -u root -p 旧密码(默认为空) password 123456	# -p后面为旧密码，password后面为新密码
```

- 退出

```sh
quit
```

### 用户管理

>mysql关键字不区分大小写，但是一般推荐关键字用大写以便于区分
>
>用户相关数据存储在mysql库中的user表内，可自行查看~~或修改~~

- 创建用户

```sql
CREATE USER 'admin'/'admin'@'localhost' IDENTIFIED BY 'password';	#单引号中内容按需求自行替换	
```

- 删除用户

```sql
DROP USER admin;
```

- 赋予权限

```sql
GRANT select/insert/delete/update/ALL ON test1/ *.* TO 'admin';		#赋予什么权限用于什么库上
```

### 数据库的备份与恢复

- 备份数据库

> 在shell模式下

```sh
> mysqldump -u root -p test1>test1.sql 		#任意后缀都可以(貌似)，用txt后缀可看出实际是如何备份的
```

- 恢复数据库

```sh
> mysql -u root -p test1<test1.sql
```

### 显示库/表

```sql
SHOW DATABASES/TABLES;		#SHOW之类用复数，创建修改之类用单数
```

***

## 库的操作


1. 创建数据库

```sql
CREATE DATABASE test1;		#创建一个名为test的数据库
```

2. 删除数据库

```sql
DROP DATABASE test1;		#删库然后跑路
```

3. 打开数据库

```sql
USE test1;			#引用指定库
```

>有人会问怎么退出这个库。实际上这个操作更像是指向一个库而不是进入一个库，还是可以用SHOW DATABASES之类的

***

## 表的操作 

>主要类型: INT(整数),CHAR/VARCHAR(字符串),DATE(日期)  
>
>CHAR和VARCHAR的区别:  
>>CHAR长度固定，设了CHAR(10)的情况，不满会用空格补足到10位  
>>VARCHAR长度可变，如数据长度为X，实际存储为X+1/2位(标识你输入的长度,超过255用2位,小于则1位)
>
>主要参数: NOT NULL(不能为空),AUTO_INCREMENT(自增),PRIMARY KEY(主键)

1. 创建表

>创建顺序:字段名 类型 额外选项(是否为空，默认值，是否自增，主键以及外键等等)

```sql
CREATE table studio1(
	id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(10) NOT NULL DEFAULT 'noob',
	age int(3) NOT NULL DEFAULT 18,
	birth DATE NOT NULL,
	kind CHAR(15)		#最后一句则不需要逗号
);
```

2. 删除表

```sql
DROP TABLE studio1;
```

### 对表结构的修改(ALTER)

1. 查看表字段

```sql
SHOW COLUMNS FROM studio1;
```

2. 删除字段

```sql
ALTER TABLE studio1 DROP kind;
```

3. 增加字段

```sql
ALTER TABLE studio1 ADD sex INT(1);		#默认插在最后
ALTER TABLE studio1 ADD sex INT(1) FIRST;	#插在最前
ALTER TABLE studio1 ADD sex INT(1) AFTER age;	#插在age字段后面
```

4. 修改字段

> 可用MODIFY或CHANGE子句

```sql
ALTER TABLE studio1 MODIFY age INT(2);			#MODIFY修改属性
ALTER TABLE studio1 CHANGE sex gender VARCHAR(6);	#CHANGE重命名及更改类型
```

5. 修改默认值

```sql
ALTER TABLE studio1 ALTER birth SET DEFAULT '2000-1-1' ;
ALTER TABLE studio1 ALTER age DROP DEFAULT;
```

6. 修改表名

```sql
ALTER TABLE studio1 RENAME TO xxx;
```

### 对表内容的操作(增删改查)

1. 增

```sql
INSERT INTO studio1
	(name,age,birth,gender)
	VALUES
	("tom",17,'2000-1-1','male');
#两种方式皆可，第一种比较美观
INSERT INTO studio1(name,age,birth) VALUES("sam",16,'2002-2-2','male');
```

2. 删

>自增情况下删除数据会导致断层，创建的下一条数据不会补上你删除的id

```sql
DELETE FROM studio1;		#删除表内所有数据
DELETE FROM studio1 WHERE id=1;	#主键具有唯一性，用来定位会比较准确
TRUNCATE TABLE studio1;		#清空表，自增也会从重新计数
```

3. 改

```sql
UPDATE studio1 SET age=18 WHERE name='tom';
```

4. 查(花式查法)

```sql
SELECT name,age FROM studio1;
SELECT * FROM studio1 WHERE id IN (3,5,7);
SELECT * FROM studio1 WHERE id BETWEEN 5 AND 10;
SELECT * FROM studio1 LIMIT 5;		#前5行
SELECT * FROM studio1 LIMIT 4,5;	#从第4行开始的5行
```

### 模糊查询

```sql
SELECT * FROM studio1 WHERE birth LIKE '2000%';
```

### 排序查询

```sql
SELECT * FROM studio1 ORDER BY age ASC/DESC;	#默认ASC(升序),DESC为降序
```

### 分组查询

> COUNT(计数),SUM(总和),AVG(平均)

```sql
SELECT name,COUNT(*) FROM studio1 GROUP BY name;  #统计各个name出现次数
```

>GROUP BY必须用在WHERE之后，ORDER BY之前

### 联合查询

> 联合查询用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中
> UNION默认删除重复数据，UNION ALL保留重复数据

```sql
SELECT * FROM studio1 UNION/UNION ALL SELECT * FROM studio2;
```
