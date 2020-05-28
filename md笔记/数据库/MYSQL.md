# MySQL

## 1、MySQL入门

### 1.1 数据库概念

概念：按照数据结构来组织、存储和管理数据的仓库。是一个长期存储在计算机内的、有组织的、可共享的、统一管理的大量数据的集合。

作用：存储数据、管理数据

### 1.2 数据库分类

关系型数据库：（SQL)

- MySQL、Oracle、Sql Server、SQLlite等。

- 通过表和表之间，行和列之间的关系进行数据的存储。

非关系型数据库：(NoSQL)  Not Only SQL

- Redis、MongDB

- 非关系型数据库，对象存储，通过对象的自身属性来决定

  

**DBMS(Database Management System)**

- 数据库的管理软件，科学有效的管理我们的数据。获取和维护数据。
- MySQL，数据库管理数据。

### 1.3 MySQL概念

- MySQL是一种关系型数据库管理系统，关系数据库将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。

- MySQL是最好的 [RDBMS](https://baike.baidu.com/item/RDBMS/1048260) (Relational Database Management System，关系数据库管理系统) 应用软件之一。
- 体积小、速度快、总体拥有成本低、招人成本低。
- 中小型网站、或者大型网站，集群。

- 官网：https://www.mysql.com/

### 1.4 MySQL连接数据库：

```sql
mysql -uroot -p123456 --连接数据库

ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';--修改用户密码(5.7版本之后)

flush privileges; --刷新权限

------------------------------------------------
--所有语句以 ; 结尾
show databases; --查看所有数据库

mysql> use news; --切换到news数据库
Database changed
mysql> show tables; --查看news数据库中所有的表
mysql> describe weibo; --查看news数据库中weibo表的信息

create database test; --创建test数据库

exit; --退出连接

-- 单行注释（SQL的单行注释）
/* (SQL的多行注释)
...
*/
```

### 1.5 MySQL基本操作

- DDL 	 定义

  (Data Definition Language 数据定义语言) 

  用于操作对象及对象本身，这种对象包括数据库,表对象，及视图对象

- DML 	操作

  (Data Manipulation Language 数据操控语言) 

  用于操作数据库对象对象中包含的数据

- DQL	 查询

  (Data Query Language 数据查询语言 )

  用于查询数据

- DCL 	 控制

  (Data Control Language 数据控制语句)

  用于操作数据库对象的权限

## 2、数据库操作

### 2.1 数据库基本操作

1、创建数据库

```sql
CREATE DATABASE IF NOT EXISTS spring
```

2、移除数据库

```sql
DROP DATABASE IF EXISTS test
```

3、使用数据库

```sql
-- 如果表名或者字段名是一个特殊字符，就需要带``(tab键上面)
USE `spring`
```

4、查看数据库

```sql
SHOW DATABASES
```

### 2.2  常用列类型

>数值

- int	
- float
- double
- decimal    字符串形式浮点数，金融计算常用

>字符串

- char          字符串大小   0~255
- varchar    可变字符串    0~65535   String
- tinytext    微型文本        2^8 - 1       博客文章
- text           文本串            2^16 - 1     大文本

> 时间日期

​	java.util.Date

- date    YYYY-MM-DD     日期格式
- time    HH : mm : ss      时间格式
- datetime    YYYY-MM-DD  HH : mm : ss    最常用时间格式
- timestamp   时间戳     1970.1.1  到现在的毫秒数
- year               年份表示

> null

-  没有值，未知

### 2.3 字段属性

**无符号**（Unsigned)：声明该列不能声明为负数。

**填充零**（zerofill）：不足的位数，使用0来填充。int(3)  , 5 : 005。

**自动递增**：

​	自动在上一条基础上 + 1 （默认）；

​	通常用来设计唯一的主键，必须为整数；

### 2.4 创建数据表

```sql
--使用英文()，表的名称和字段尽量用``括起来
--AUTO_INCREMENT 自增
--字符串尽量使用单引号括起来
--所有语句后加 , （英文逗号），最后一个不用加
--PRIMARY KEY主键，一般一个表只有一个唯一的主键
CREATE TABLE `student` (
  `id` int unsigned NOT NULL AUTO_INCREMENT COMMENT 'id',
  `name` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '姓名',
  `age` int NOT NULL COMMENT '年龄',
  `password` varchar(18) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '密码',
  PRIMARY KEY (`id`,`password`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8
```

格式

```sql
CREATE TABLE [IF NOT EXISTS] `表名`(
	'字段名' 列类型 [属性] [索引] [注释],
    '字段名' 列类型 [属性] [索引] [注释],
    ...
    '字段名' 列类型 [属性] [索引] [注释]
)[表类型][字符集设置][注释]
```

常用命令：

```sql
SHOW CREATE DATABASE spring --查看创建数据库语句
SHOW CREATE TABLE student   --查看student数据表的定义语句
DESC student                --显示表的结构
```

### 2.5 数据库引擎

```sql
--关于数据库引擎
/*
InnoDB 默认使用
MYISAM 早些年使用的
*/
```

|              | MYISAM     | INNODB         |
| ------------ | ---------- | -------------- |
| 事务支持     | 最新版支持 | 支持           |
| 数据行锁定   | 不支持     | 支持           |
| 外键约束     | 不支持     | 支持           |
| 全文索引     | 支持       | 不支持         |
| 表空间的大小 | 较小       | 较大，约为两倍 |

常规使用操作：

- MYISAM：节约空间，速度较快
- INNODB：安全性高，事物的处理，多表多用户操作

> 在物理空间存在的位置

- 所有的数据库文件都在data目录下

- 本质上还是文件的存储

> 设置数据库表的字符集编码

```sql
CHARSET=utf8
```

不设置的话，会用数据库默认的字符串编码

(可在ini文件中更改，但不建议)

### 2.6 修改删除表

```sql
--  修改表名
ALTER TABLE student RENAME AS teacher
--增加表字段
ALTER TABLE teacher ADD adress VARCHAR(11)
--修改表字段
ALTER TABLE teacher MODIFY adress VARCHAR(10) --修改约束
ALTER TABLE teacher CHANGE age age1 INT(4)    --字段重命名
--删除表字段
ALTER TABLE teacher DROP adress
--删除表
DROP TABLE IF EXISTS teacher
```

change 用来字段重命名

modify 不用来字段重命名，只能修改字段类型和约束

## 3、MySQL数据管理

### 3.1 关系模型

#### 3.1.1、主键

- 主键是关系表中记录的唯一标识

- 主键最好是完全业务无关的字段

- 主键也不应该允许`NULL`

- 字段类型：

  自增整数类型：数据库会在插入数据时自动为每一条记录分配一个自增整数，这样我们就完全不用担心主键重复，也不用自己预先生成主键；

  全局唯一GUID类型：使用一种全局唯一的字符串作为主键，类似`8f55d96b-8acc-4636-8cb8-76bf8abc2f57`。GUID算法通过网卡MAC地址、时间戳和随机数保证任意计算机在任意时间生成的字符串都是不同的，大部分编程语言都内置了GUID算法，可以自己预算出主键。

- 可以使用多个列作为联合主键，但不建议也不常用

#### 3.1.2、外键

```sql
--添加外键
ALTER TABLE students
ADD CONSTRAINT fk_class_id
FOREIGN KEY (class_id)
REFERENCES classes (id);
```

- 关系数据库通过外键可以实现一对多、多对多和一对一的关系。外键既可以通过数据库来约束，也可以不设置约束，仅依靠应用程序的逻辑来保证。
- 外键约束会降低数据库的性能，大部分互联网应用程序为了追求速度，并不设置外键约束，而是仅靠应用程序自身来保证逻辑的正确性。



### 3.2 DQL语言

DML语言：（Data Query Language）数据库查询语言

#### 3.2.1 基本查询

```sql
--语法
SELECT * FROM <表名>;
--查询students表全部数据
SELECT * FROM students;
SELECT 100+200;
--去重查找
SELECT DISTINCT stdentNo FROM result
--查询系统版本
SELECT VERSION();
```

- SELECT查询的结果是一个二维表。
- 虽然`SELECT`可以用作计算，但它并不是SQL的强项。但是，不带`FROM`子句的`SELECT`语句有一个有用的用途，就是用来判断当前到数据库的连接是否有效。许多检测工具会执行一条`SELECT 1;`来测试数据库连接。

#### 3.2.2 条件查询

```sql
--语法
SELECT * FROM <表名> WHERE <条件表达式>
--查询分数在80分或以上的学生
SELECT * FROM students WHERE score >= 80;
--查询符合条件“分数在80分或以上”，并且还符合条件“男生”的学生
SELECT * FROM students WHERE score >= 80 AND gender = 'M';
--查询符合条件“分数在80分或以上”，或者符合条件“男生”的学生
SELECT * FROM students WHERE score >= 80 OR gender = 'M';
--查询班号不为2的学生
SELECT * FROM students WHERE NOT class_id = 2;
SELECT * FROM students WHERE  class_id <> 2;
--多条件查询
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M';



```

#### 3.2.3 投影查询

```sql
--返回指定列
SELECT id, score, name FROM students;
--返回指定列并将score重命名为points
SELECT id, score points, name FROM students;
--复杂查询
SELECT id, score points, name FROM students WHERE gender = 'M';
```

#### 3.2.4 排序查询

```sql
--按score从低到高
SELECT id, name, gender, score FROM students ORDER BY score;
-- 按score从高到低
SELECT id, name, gender, score FROM students ORDER BY score DESC;
-- 按score, gender排序:
SELECT id, name, gender, score FROM students ORDER BY score DESC, gender;
-- 带WHERE条件的ORDER BY:
SELECT id, name, gender, score
FROM students
WHERE class_id = 1
ORDER BY score DESC;
```

- 默认的排序规则是`ASC`：“升序”，即从小到大。`ASC`可以省略，即`ORDER BY score ASC`和`ORDER BY score`效果一样。

#### 3.2.5 分页查询

```sql
-- 把结果集分页，每页3条记录
-- 查询第1页
SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 0;
-- 查询第二页
SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 3;
--查询第三页
SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 6;
```

- `LIMIT 3 OFFSET 0`表示，对结果集从0号记录开始，最多取3条。注意SQL记录集的索引从0开始。
- `LIMIT`总是设定为`pageSize`；
- `OFFSET`计算公式为`pageSize * (pageIndex - 1)`。
- `OFFSET`超过了查询的最大数量并不会报错，而是得到一个空的结果集。
- `OFFSET`是可选的，如果只写`LIMIT 15`，那么相当于`LIMIT 15 OFFSET 0`。
- 在MySQL中，`LIMIT 15 OFFSET 30`还可以简写成`LIMIT 30, 15`。
- 使用`LIMIT  OFFSET `分页时，随着`N`越来越大，查询效率也会越来越低。

#### 3.2.6 聚合查询

```sql
--查询students表一共有多少条记录
SELECT COUNT(*) FROM students;
-- 使用聚合查询并设置结果集的列名为num
SELECT COUNT(*) num FROM students;
-- 使用聚合查询并设置WHERE条件
SELECT COUNT(*) boys FROM students WHERE gender = 'M';
--按class_id分组
SELECT COUNT(*) num FROM students GROUP BY class_id;
--按class_id分组并显示班级名
SELECT class_id, COUNT(*) num FROM students GROUP BY class_id;
```

- `COUNT(*)`表示查询所有列的行数，要注意聚合的计算结果虽然是一个数字，但查询的结果仍然是一个二维表，只是这个二维表只有一行一列，并且列名是`COUNT(*)`。
- 聚合查询的列中，只能放入分组的列。

| 函数 | 说明                                   |
| :--- | :------------------------------------- |
| SUM  | 计算某一列的合计值，该列必须为数值类型 |
| AVG  | 计算某一列的平均值，该列必须为数值类型 |
| MAX  | 计算某一列的最大值                     |
| MIN  | 计算某一列的最小值                     |

- `MAX()`和`MIN()`函数并不限于数值类型。如果是字符类型，`MAX()`和`MIN()`会返回排序最后和排序最前的字符。
- 如果聚合查询的`WHERE`条件没有匹配到任何行，`COUNT()`会返回0，而`SUM()`、`AVG()`、`MAX()`和`MIN()`会返回`NULL`。

#### 3.2.7 多表查询

```sql
--从students表和classes表的“乘积”查询
SELECT * FROM students, classes;
--设置列的别名来给两个表各自的id和name列起别名
SELECT
    students.id sid,
    students.name,
    students.gender,
    students.score,
    classes.id cid,
    classes.name cname
FROM students, classes;
--设置表的别名
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c;
--where查询
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c
WHERE s.gender = 'M' AND c.id = 1;
```

- 一次查询两个表的数据，查询的结果也是一个二维表，它是`students`表和`classes`表的“乘积”，即`students`表的每一行与`classes`表的每一行都两两拼在一起返回。结果集的列数是`students`表和`classes`表的列数之和，行数是`students`表和`classes`表的行数之积。
- 使用多表查询可以获取M x N行记录。
- 多表查询的结果集可能非常巨大，要小心使用。

#### 3.2.8 连接查询

```sql
--选出students表的所有学生信息
SELECT s.id, s.name, s.class_id, s.gender, s.score FROM students s;
--使用INNER JOIN 
--选出所有学生，同时返回班级名称
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
INNER JOIN classes c
ON s.class_id = c.id;
--使用OUTER JOIN
--选出所有学生，同时返回班级名称
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
RIGHT OUTER JOIN classes c
ON s.class_id = c.id;
```

- 连接查询是另一种类型的多表查询。连接查询对多个表进行JOIN运算，简单地说，就是先确定一个主表作为结果集，然后，把其他表的行有选择性地“连接”在主表结果集上。

- INNER JOIN查询的写法是：

  1. 先确定主表，仍然使用`FROM <表1>`的语法；
  2. 再确定需要连接的表，使用`INNER JOIN <表2>`的语法；
  3. 然后确定连接条件，使用`ON <条件...>`，这里的条件是`s.class_id = c.id`，表示`students`表的`class_id`列与`classes`表的`id`列相同的行需要连接；
  4. 可选：加上`WHERE`子句、`ORDER BY`等子句。

- 区别：

  INNER JOIN只返回同时存在于两张表的行数据，由于`students`表的`class_id`包含1，2，3，`classes`表的`id`包含1，2，3，4，所以，INNER JOIN根据条件`s.class_id = c.id`返回的结果集仅包含1，2，3。

  RIGHT OUTER JOIN返回右表都存在的行。如果某一行仅在右表存在，那么结果集就会以`NULL`填充剩下的字段。

  LEFT OUTER JOIN则返回左表都存在的行。如果我们给students表增加一行，并添加class_id=5，由于classes表并不存在id=5的行，所以，LEFT OUTER JOIN的结果会增加一行，对应的`class_name`是`NULL`

  假设查询语句是：

  ```sql
  SELECT ... FROM tableA ??? JOIN tableB ON tableA.column1 = tableB.column2;
  ```

  我们把A看作左表，把B看成右表，那么

  INNER JOIN是选出两张表都存在的记录：

  ![inner-join](https://www.liaoxuefeng.com/files/attachments/1246892164662976/l)

  LEFT OUTER JOIN是选出左表存在的记录：

  ![left-outer-join](https://www.liaoxuefeng.com/files/attachments/1246893588481376/l)

  RIGHT OUTER JOIN是选出右表存在的记录：

  ![right-outer-join](https://www.liaoxuefeng.com/files/attachments/1246893609222688/l)

  FULL OUTER JOIN则是选出左右表都存在的记录：

  ![full-outer-join](https://www.liaoxuefeng.com/files/attachments/1246893632359424/l)

扩展：自连接

#### 3.2.9 模糊查询

| 运算符      | 语法               | 描述                                      |
| ----------- | ------------------ | ----------------------------------------- |
| IS NULL     | a is null          | 如果操作符为NULL，结果为真                |
| IS NOT NULL | a is not null      | 如果操作符不为NULL，结果为真              |
| BETWEEN     | a between b and c  | 如果a 在b 和 c 之间，结果为真（闭区间）   |
| LIKE        | a like b           | SQL匹配，如果a 匹配b ，结果为真           |
| IN          | a in (a1,a2,a3...) | 假设a在a1,或者a2...其中的值之中，结果为真 |

```sql
--查询姓刘的同学
SELECT * FROM teacher WHERE `name` LIKE '张%';
--查询名字中有张的同学
SELECT * FROM teacher WHERE `name` LIKE '%张%';
```

| 通配符                         | 描述                       |
| :----------------------------- | :------------------------- |
| %                              | 替代 0 个或多个字符        |
| _                              | 替代一个字符               |
| [*a-z*]                        | 字符列中的任何单一字符     |
| [^*charlist*] 或 [!*charlist*] | 不在字符列中的任何单一字符 |

### 3.3 DML语言

#### 3.3.1 INSERT

当我们需要向数据库表中插入一条新记录时，就必须使用`INSERT`语句。

```sql
--基本语法
INSERT INTO <表名> (字段1, 字段2, ...) VALUES (值1, 值2, ...);
--添加记录
INSERT INTO teacher(`name`,age,`password`) VALUES ('张三',16,'123456');
--一次性添加多条新记录
INSERT INTO teacher(`name`,age,`password`) 
VALUES 
	('张三',16,'123456'),
	('李四',17,'456');
```

- 注意到我们并没有列出`id`字段，也没有列出`id`字段对应的值，这是因为`id`字段是一个自增主键，它的值可以由数据库自己推算出来。此外，如果一个字段有默认值，那么在`INSERT`语句中也可以不出现。
- 字段顺序不必和数据库表的字段顺序一致，但值的顺序必须和字段顺序一致。

#### 3.3.2 UPDATE

如果要更新数据库表中的记录，我们就必须使用`UPDATE`语句。

```sql
--基本语法
UPDATE <表名> SET 字段1=值1, 字段2=值2, ... WHERE ...;
--更新一条记录
UPDATE teacher SET age=99 WHERE id=1;
--更新多个字段
UPDATE teacher SET age=88,`password`='newpassword' WHERE id=1;
--使用表达式修改字段
UPDATE teacher SET age=age+10 WHERE id=1;
```

- 如果`WHERE`条件没有匹配到任何记录，`UPDATE`语句不会报错，也不会有任何记录被更新。

- 要特别小心的是，`UPDATE`语句可以没有`WHERE`条件，这时，整个表的所有记录都会被更新。

#### 3.3.3 DELETE

如果要删除数据库表中的记录，我们可以使用`DELETE`语句。

```sql
--基本语法
DELETE FROM <表名> WHERE ...;
--删除一条数据
DELETE FROM teacher WHERE id=1;
--删除满足条件的数据
DELETE FROM students WHERE id>=5 AND id<=7;
```

- 如果`WHERE`条件没有匹配到任何记录，`DELETE`语句不会报错，也不会有任何记录被删除。

- 不带`WHERE`条件的`DELETE`语句会删除整个表的数据。

#### 3.3.4 TRUNCATE 命令

作用：完全清空一个数据表，表的结构和约束不会变。

```sql
--清空student表
TRUNCATE student;
```

> delete 和 TRUNCAT 区别

相同点：都能删除数据，都不会删除表结构

不同点：

​				1、TRUNCATE 重新设置自增列，计数器会清零；

​				2、TRUNCATE 不会影响事务。

## 4、事务

### 4.1 概念

- 把多条语句作为一个整体进行操作的功能，被称为数据库事务。
- 数据库事务可以确保该事务范围内的所有操作都可以全部成功或者全部失败。

> 特性（ACID）：

- A：Atomic，原子性，将所有SQL作为原子工作单元执行，要么全部执行，要么全部不执行；
- C：Consistent，一致性，事务完成后，所有数据的状态都是一致的，即A账户只要减去了100，B账户则必定加上了100；
- I：Isolation，隔离性，如果有多个事务并发执行，每个事务作出的修改必须与其他事务隔离；
- D：Duration，持久性，即事务完成后，对数据库数据的修改被持久化存储。

> 事务执行：

- 对于单条SQL语句，数据库系统自动将其作为一个事务执行，这种事务被称为**隐式事务**。

- 要手动把多条SQL语句作为一个事务执行，使用`BEGIN`开启一个事务，使用`COMMIT`提交一个事务，这种事务被称为**显式事务**。

```sql
--转账操作
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
--回滚操作，整个业务失败
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
ROLLBACK;
```

- `COMMIT`是指提交事务，即试图把事务内的所有SQL所做的修改永久保存。如果`COMMIT`语句执行失败了，整个事务也会失败。

- 有些时候，我们希望主动让事务失败，这时，可以用`ROLLBACK`回滚事务，整个事务会失败。

### 4.2 隔离级别

对于两个并发执行的事务，如果涉及到操作同一条记录的时候，可能会发生问题。因为并发操作会带来数据的不一致性，包括脏读、不可重复读、幻读等。数据库系统提供了隔离级别来让我们有针对性地选择事务的隔离级别，避免数据不一致的问题。

| Isolation Level  | 脏读（Dirty Read） | 不可重复读（Non Repeatable Read） | 幻读（Phantom Read） |
| :--------------- | :----------------- | :-------------------------------- | :------------------- |
| Read Uncommitted | Yes                | Yes                               | Yes                  |
| Read Committed   | -                  | Yes                               | Yes                  |
| Repeatable Read  | -                  | -                                 | Yes                  |
| Serializable     | -                  | -                                 | -                    |

#### 4.2.1 Read Uncommitted

Read Uncommitted是隔离级别最低的一种事务级别。在这种隔离级别下，一个事务会读到另一个事务更新后但未提交的数据，如果另一个事务回滚，那么当前事务读到的数据就是脏数据，这就是脏读（Dirty Read）。

首先，我们准备好`students`表的数据，该表仅一行记录：

```
mysql> select * from students;
+----+-------+
| id | name  |
+----+-------+
|  1 | Alice |
+----+-------+
1 row in set (0.00 sec)
```

然后，分别开启两个MySQL客户端连接，按顺序依次执行事务A和事务B：

| 时刻 | 事务A                                             | 事务B                                             |
| :--- | :------------------------------------------------ | :------------------------------------------------ |
| 1    | SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED; | SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED; |
| 2    | BEGIN;                                            | BEGIN;                                            |
| 3    | UPDATE students SET name = 'Bob' WHERE id = 1;    |                                                   |
| 4    |                                                   | SELECT * FROM students WHERE id = 1;              |
| 5    | ROLLBACK;                                         |                                                   |
| 6    |                                                   | SELECT * FROM students WHERE id = 1;              |
| 7    |                                                   | COMMIT;                                           |

当事务A执行完第3步时，它更新了`id=1`的记录，但并未提交，而事务B在第4步读取到的数据就是未提交的数据。

随后，事务A在第5步进行了回滚，事务B再次读取`id=1`的记录，发现和上一次读取到的数据不一致，这就是脏读。

可见，在Read Uncommitted隔离级别下，一个事务可能读取到另一个事务更新但未提交的数据，这个数据有可能是脏数据。

#### 4.2.2 Read Committed

在Read Committed隔离级别下，一个事务可能会遇到不可重复读（Non Repeatable Read）的问题。

不可重复读是指，在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致。

我们仍然先准备好`students`表的数据：

```
mysql> select * from students;
+----+-------+
| id | name  |
+----+-------+
|  1 | Alice |
+----+-------+
1 row in set (0.00 sec)
```

然后，分别开启两个MySQL客户端连接，按顺序依次执行事务A和事务B：

| 时刻 | 事务A                                           | 事务B                                           |
| :--- | :---------------------------------------------- | :---------------------------------------------- |
| 1    | SET TRANSACTION ISOLATION LEVEL READ COMMITTED; | SET TRANSACTION ISOLATION LEVEL READ COMMITTED; |
| 2    | BEGIN;                                          | BEGIN;                                          |
| 3    |                                                 | SELECT * FROM students WHERE id = 1;            |
| 4    | UPDATE students SET name = 'Bob' WHERE id = 1;  |                                                 |
| 5    | COMMIT;                                         |                                                 |
| 6    |                                                 | SELECT * FROM students WHERE id = 1;            |
| 7    |                                                 | COMMIT;                                         |

当事务B第一次执行第3步的查询时，得到的结果是`Alice`，随后，由于事务A在第4步更新了这条记录并提交，所以，事务B在第6步再次执行同样的查询时，得到的结果就变成了`Bob`，因此，在Read Committed隔离级别下，事务不可重复读同一条记录，因为很可能读到的结果不一致。

#### 4.2.3 Repeatable Read

在Repeatable Read隔离级别下，一个事务可能会遇到幻读（Phantom Read）的问题。

幻读是指，在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了。

我们仍然先准备好`students`表的数据：

```
mysql> select * from students;
+----+-------+
| id | name  |
+----+-------+
|  1 | Alice |
+----+-------+
1 row in set (0.00 sec)
```

然后，分别开启两个MySQL客户端连接，按顺序依次执行事务A和事务B：

| 时刻 | 事务A                                               | 事务B                                             |
| :--- | :-------------------------------------------------- | :------------------------------------------------ |
| 1    | SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;    | SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;  |
| 2    | BEGIN;                                              | BEGIN;                                            |
| 3    |                                                     | SELECT * FROM students WHERE id = 99;             |
| 4    | INSERT INTO students (id, name) VALUES (99, 'Bob'); |                                                   |
| 5    | COMMIT;                                             |                                                   |
| 6    |                                                     | SELECT * FROM students WHERE id = 99;             |
| 7    |                                                     | UPDATE students SET name = 'Alice' WHERE id = 99; |
| 8    |                                                     | SELECT * FROM students WHERE id = 99;             |
| 9    |                                                     | COMMIT;                                           |

事务B在第3步第一次读取`id=99`的记录时，读到的记录为空，说明不存在`id=99`的记录。随后，事务A在第4步插入了一条`id=99`的记录并提交。事务B在第6步再次读取`id=99`的记录时，读到的记录仍然为空，但是，事务B在第7步试图更新这条不存在的记录时，竟然成功了，并且，事务B在第8步再次读取`id=99`的记录时，记录出现了。

可见，幻读就是没有读到的记录，以为不存在，但其实是可以更新成功的，并且，更新成功后，再次读取，就出现了。

#### 4.2.4 Serializable

Serializable是最严格的隔离级别。在Serializable隔离级别下，所有事务按照次序依次执行，因此，脏读、不可重复读、幻读都不会出现。

虽然Serializable隔离级别下的事务具有最高的安全性，但是，由于事务是串行执行，所以效率会大大下降，应用程序的性能会急剧降低。如果没有特别重要的情景，一般都不会使用Serializable隔离级别。

#### 4.2.5 默认隔离级别

如果没有指定隔离级别，数据库就会使用默认的隔离级别。在MySQL中，如果使用InnoDB，默认的隔离级别是Repeatable Read。

## 5、索引

索引（index)是帮助MySQL高效获取数据的数据结构

### 5.1 分类

- 主键索引（PRIMARY KEY）
- 唯一索引（UNIQUE INDEX）
- 常规索引（KEY/INDEX）
- 全文索引（FULLTEXT）

```sql
--显示所有索引信息
SHOW INDEX from app_user;
--创建普通索引
CREATE INDEX indexName ON mytable(username(length)); 
--修改表时创建
ALTER table tableName ADD INDEX indexName(columnName);
--创建表时指定
CREATE TABLE mytable(  
ID INT NOT NULL,   
username VARCHAR(16) NOT NULL,  
INDEX [indexName] (username(length))  
);
--删除索引
DROP INDEX [indexName] ON mytable; 
--创建唯一索引
CREATE UNIQUE INDEX indexName ON mytable(username(length));
--修改表时创建
ALTER table mytable ADD UNIQUE [indexName] (username(length));
--创建表时指定
CREATE TABLE mytable(  
ID INT NOT NULL,   
username VARCHAR(16) NOT NULL,  
UNIQUE [indexName] (username(length))  
);  
```

### 5.2 原则

- 索引不是越多越好
- 不要对进程变动数据加索引
- 小数据量的表不需要加索引
- 索引一般加在常用来查询的字段上

> 索引数据结构

Btree： innodb默认索引数据结构

http://blog.codinglabs.org/articles/theory-of-mysql-index.html

## 6、三大范式

为什么需要数据规范化？

- 信息重复
- 更新异常
- 插入异常
- 删除异常

**第一范式（1NF）**：要求数据库表的每一列都是不可分割的原子数据项。

原子性：保证每一列不可再分

**第二范式（2NF）**：在1NF的基础上，非码属性必须完全依赖于候选码（在1NF基础上消除非主属性对主码的部分函数依赖）

前提：满足第一范式

每张表只描述一件事情

**第三范式（3NF）**：在2NF基础上，任何非主[属性](https://baike.baidu.com/item/属性)不依赖于其它非主属性（在2NF基础上消除传递依赖）

前提：满足第一范式和第二范式

第三范式需要确保数据表中的每一列数据都和主键直接相关，而不能间接相关。

**规范和性能问题：**

关联查询的表不能超过三张表

- 考虑商业化的需求和目标（成本，用户体验），数据库的性能更重要。
- 在规范性能的问题的时候，需要适当的考虑一下规范性。
- 故意给某些表增加一些冗余的字段（从多表查询变成单表查询）。
- 故意增加一些计算列（从大数据量降低为小数据量的查询：索引）。