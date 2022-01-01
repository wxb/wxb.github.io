title: MySQL中的各种join
categories:
  - SQL
tags:
  - join
  - MySQL
toc: true
author: 虢國技酱
comments: true
date: 2016-12-15 10:27:11
description:
original:
permalink:
---

数据库中的`join`成为**连接**，连接的主要作用是根据两个或多个表中的列之间的关系，获取存在于不同表中的数据。连接分为三类：**内连接**、**外连接**、**全连接**

<!-- more -->

# 上图
![](/images/mysql/01.jpg)
***图片来自网络***

# 数据准备

```sql
-- 创建测试数据库
CREATE DATABASE join_test CHARSET UTF8;

-- 人员信息表
CREATE TABLE `Persons` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `LastName` char(16) NOT NULL DEFAULT '',
  `FirstName` char(16) NOT NULL DEFAULT '',
  `Address` varchar(128) NOT NULL DEFAULT '',
  `City` varchar(128) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 订单表
CREATE TABLE `Orders` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `OrderNo` int(11) NOT NULL DEFAULT '0',
  `Pid` int(11) NOT NULL DEFAULT '0',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO `Persons` (`LastName`, `FirstName`, `Address`, `City`)
VALUES
('Adams', 'John', 'Oxford Street', 'London'),
('Bush', 'George', 'Fifth Avenue', 'New York'),
('Carter', 'Thomas', 'Changan Street', 'Beijing');

INSERT INTO `Orders` (`OrderNo`, `Pid`)
VALUES (77895, 3), (44678, 3), (22456, 1), (24562, 1), (34764, 65);

```
Persons表
![](/images/mysql/02.jpg)

Orders表
![](/images/mysql/03.jpg)

**ok，准备工作完成，现在来说说mysql中的四中三类连接**

# 内连接(图-3)
内连接在SQL中是`JOIN`|`INNER JOIN`; `INNER JOIN` 与 `JOIN` 的查询结果都是相同的，也就是说两个的作用都是一样的。
内连接查询返回两个表中在`ON`后面指定的列条件相同时的行;
例如：
```sql
SELECT Persons.id, Persons.LastName, Persons.FirstName, Orders.id,Orders.OrderNo,Orders.Pid
FROM Persons
INNER JOIN Orders
ON Persons.id = Orders.Pid
```
查询的结果：
![](/images/mysql/04.jpg)
或者
```sql
SELECT Persons.id, Persons.LastName, Persons.FirstName, Orders.id,Orders.OrderNo,Orders.Pid
FROM Persons
JOIN Orders
ON Persons.id = Orders.Pid
```
结果：
![](/images/mysql/05.jpg)

**从这结果中可以看到：**
* 首先，我们发现 `INNER JOIN` 和 `JOIN` 的查询结果确实是一致的；
* 其次，`INNER JOIN` 和 `JOIN` 关键字在表中存在至少一个匹配时返回行。如果 `Persons` 中的行在 `Orders` 中没有匹配，就不属于内连接查询的结果，就不会列出相应的`Persons`或`Orders`这些行。简言之，查询的结果集是符合`ON`之后条件时的所有行，A表中的某一条记录对应B表中的多个记录会以重复的方式对应不同的多条B表记录出现在结果集中，B表中的一条记录对应A表中的多条记录时会以多条A表记录对应相同B表记录的方式出现在结果集中。

# 外连接(图-1、2)
外连接分为 **左外连接**`LEFT JOIN` 和 **右外连接**`RIGHT JOIN`。虽然是两种写法，但他们之间是可以转换的，其实是互相的变形。
* `LEFT JOIN` 关键字会从左表 (table_name1) 那里返回所有的行，即使在右表 (table_name2) 中没有匹配的行。***LEFT JOIN 也写作 LEFT OUTER JOIN***
* `RIGHT JOIN` 关键字会右表 (table_name2) 那里返回所有的行，即使在左表 (table_name1) 中没有匹配的行。***RIGHT JOIN 也写作 RIGHT OUTER JOIN***

我们以左外连接示例说明，然后写一个查询结果一样的右外连接说明他两确实是可以转化的；左外连接和右外连接关键的区别实在 **主表的确定**:
* 左外连接是以 `LEFT JOIN` 左侧也就是前面的表(FROM后面的跟的表)作为主表，返回所有左侧表中的记录，右侧表中不存在匹配时在相应字段不`NULL`。 左侧表可能在右侧表中对应多条记录，那么就在结果集中以相同左侧表记录不同右侧表记录多条显示在结果集中;也可能左侧表记录在右侧没有一条匹配，那么就会返回所有左侧表记录，右侧表的字段都以`NULL`替代；所以在没有`WHERE`条件时，mysql查询的结果集或者说临时表的记录一定是大于或等于左侧表记录数的。
* 右外连接是以 `RIGHT JOIN` 右侧也就是后面的表(RIGHT JOIN后面的跟的表)作为主表，返回所有右侧表中的记录，左侧表中不存在匹配时在相应字段不`NULL`。

左外连接：
```sql
SELECT Persons.id, Persons.LastName, Persons.FirstName, Orders.id,Orders.OrderNo,Orders.Pid
FROM Persons
LEFT JOIN Orders
ON Persons.id = Orders.Pid
```
结果：
![](/images/mysql/06.jpg)

右外连接：
```sql
SELECT Persons.id, Persons.LastName, Persons.FirstName, Orders.id,Orders.OrderNo,Orders.Pid
FROM Persons
RIGHT JOIN Orders
ON Persons.id = Orders.Pid
```
结果：
![](/images/mysql/07.jpg)

**现在我们对上面的左外连接做一个相同结果的右外连接转化**
右外连接：
```sql
SELECT Persons.id, Persons.LastName, Persons.FirstName, Orders.id,Orders.OrderNo,Orders.Pid
FROM Orders
RIGHT JOIN Persons
ON Persons.id = Orders.Pid
```
结果：
![](/images/mysql/08.jpg)

结果是不是和上面的左外连接一样啊！仔细理解就会发现：`Persons`对`Orders` 的左外连接 其实就相当于 `Orders`对`Persons` 的右外连接；两者的主表都是`Persons`表，所以转换的关键是保证左外连接和右外连接的主表是相同的即可。


# 全连接(图-4)
首先注意：**mysql并不支持全连接**，但是有相应的替代策略：利用`UNION`联合查询，下面给出mysql关于连接查询的参考资料：
* [14.2.9 SELECT Syntax](http://dev.mysql.com/doc/refman/5.7/en/select.html)
* [14.2.9.2 JOIN Syntax](http://dev.mysql.com/doc/refman/5.7/en/join.html)

虽然mysql不支持，但还是说明一下`FULL JOIN`：
`FULL JOIN` 全连接会从左表 (Persons) 和右表 (Orders) 那里返回所有的行。如果 `Persons` 中的行在表 `Orders` 中没有匹配，或者如果 `Orders` 中的行在表 `Persons` 中没有匹配，这些行同样会列出,不存在的字段会以`NULL`补充。
也就是说当连接的两个表的记录匹配项时，它们会组合成结果集中的一条记录，如果两张表中的某些记录在对方表中都不存在匹配时，它们也会被查询出来放大结果集中，只是在这些记录的另一个表中的字段信息是`NULL`补充的。

```sql
SELECT Persons.id, Persons.LastName, Persons.FirstName, Orders.id,Orders.OrderNo,Orders.Pid
FROM Persons
RIGHT JOIN Orders
ON Persons.id = Orders.Pid
```

ok，mysql关于FULL JOIN的替代策略：就是左外连接和右外连接的联合查询
```sql
SELECT * FROM Persons
LEFT JOIN Orders ON Persons.id = Orders.Pid
UNION
SELECT * FROM Persons
RIGHT JOIN Orders ON Persons.id = Orders.Pid

-- 模式就是这样
SELECT * FROM t1
LEFT JOIN t2 ON t1.id = t2.id
UNION
SELECT * FROM t1
RIGHT JOIN t2 ON t1.id = t2.id
```
结果：
![](/images/mysql/09.jpg)

# 总结
* 对于三类连接查询，MySQL只支持其中的：**内连接** 和 **外连接**
* 对于所有的连接类型而言，就是将符合关键字 `ON` 后条件匹配的对应组合都成为一条记录出现在结果集中，对于两个表中的某条记录可能存在：**一对多** 或者 **多对一** 的情况会在结果集中形成多条记录，只是另外一个表中查询的字段信息相同而已；千万不要误以为：左外连接查询到的记录数是和左表的记录数一样，对于其他连接一样，不能形成这个误区。
* 对于内连接查询到的是一个符合 `ON` 条件匹配的在两个表中都存在记录的结果集
* 对于外连接，例如左外连接查询到的是一个包含所有左表记录且在右表符合`ON`匹配时的右表信息的结果集，如果左表的某条记录和右表的多条记录匹配，结果集中就存在同一个左表记录对应多个右表记录的多条记录，此时的结果集记录数是大于左表的记录数的。对于右外连接来说同理。
* 对于全连接，就是把左表右表都包含在内，如果符合`ON`条件的匹配在结果集中形成一条记录，如果对左表或者右表中的某一条记录在对方表中不存在`ON`匹配时，就以单独一条记录出现在结果集中，对方表不存在的信息以`NULL`补充。
