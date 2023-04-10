---
title: Database-Key-and-Index
tags:
  - 个人总结
categories:
  - Database
date: 2023-04-11 00:53:04
---

> 在 Youtube 上刷到了一个关于数据库设计的视频，看了一下感觉还不错，这几天应该都会看一点然后每天做一下记录。视频地址：[Database Design Course](https://www.youtube.com/watch?v=ztHopE5Wnpc&list=PLo47zzoJD98Jn5Z3e3003jzqTaATKuDnI)

## 什么是键（Key）

> KEY is something on the logical level, describes your table and database design (i.e. enforces referential integrity …)

键（KEY）是数据库中常见的概念，通常用来描述数据库中表字段的性质和它们之间的关系，是逻辑层面的概念。比如有以下用户表：
| user_id | user_name | age       | sex       | email   |
|---------|-----------|-----------|-----------|-----------|
| 001     | John      | 24        | man       | abc@gmail.com  |
| 002     | Mary      | 22        | female    | bcd@gmail.com | 

如果想要从表中识别出每一条数据，那么必定会存在一个字段，它的每一行的值都不一样，就比如表中的 `user_id` 字段，而这个字段常常被称之为用户表的 主键（PRIMARY_KEY）。

假如还有一个卡片表：
| card_id   | card_name | belong_user_id |
|-----------|-----------|---------|
| 1         | GoldCard  | 001       |

它的某个字段（belong_user_id）关联着其他表的主键（user_id），那么这个字段常常称之为表的 外键（FOREIGN_KEY）。

在数据库设计与实践中，往往会遵循 键（Key）的三个特征：

1. 唯一性（Unique）。
2. 不可修改性（Nerver Changing）。
3. 不能为空（Never Null）。

	**唯一性（Unique）**： 因为 **键（KEY) 的作用是帮助我们可以在数据库中找出对应的数据** ，所以如果想要从表中的海量数据找到对应的一条数据，那么 键（KEY)  必定是表中不能重复的字段，否则使用这个 键（KEY)  就不能找出对应的一条数据。

	**不可修改性（Nerver Changing）**：我们除了使用 键（KEY)  来查找表中对应的数据，也常常会使用 键（KEY) 来关联不同表中的数据。比如在上面的用户表外，还有一个 课程表 关联了这个学生表，还有 教师表 关联了 学生表等等，如果我们修改了学生表的 键（KEY) ，那么其他以这个键（KEY)  关联学生表的关联关系都要做改变，这些改变往往会容易引起程序错误，在迫不得已的时候，我们应该遵循 键（KEY)  的不可修改性。

	**不能为空（Never Null）**：这个很容易理解，如果 键（KEY)  为空，那么我们便从表中查找不出任何数据，也关联不上任务数据。 

## 什么是索引（Index）

>INDEX is something on the physical level, helps improve access time for table operations. Behind every PK there is (usually) unique index created (automatically).

索引（INDEX) 也是数据库中另一种常见的概念，通常用来优化数据库的查询工作，是数据库物理层面上的概念，索引（INDEX) 通常记录在数据库的默认表中，由数据库程序自己维护。而刚刚我们提到的 键（KEY）其实是 索引（INDEX）的一种实现。

我们可以使用表中的一个字段作为索引（INDEX) ，也可以使用多个字段的组合成为索引（INDEX) ，例如在下方的用户表中， 可以使用 `user_id（主键）` 作为索引（INDEX) ，也可以使用  `user_name` 和 `age`  的组合来作为表的 索引（INDEX) 。

| user_id | user_name | age       | sex       | email   |
|---------|-----------|-----------|-----------|-----------|
| 001     | John      | 24        | man       | abc@gmail.com  |
| 002     | Mary      | 22        | female    | bcd@gmail.com | 

理论上我们可以随意挑选表中的字段作为 索引（INDEX），但是在实践中需要选择那些可以识别出每行数据的字段或字段组合作为 索引（INDEX) ，否则便失去了 索引（INDEX) 的意义。

要想从数据库中的表查找出其中一行数据，常用 SELECT * FROM `table_name` WHERE `column` = `condition` 。在 SELECT 语句中的 WHERE 条件就用到 **索引（INDEX）**。而使用 SELECT 语句查找数据最基本的方法就是从表的第一行找到表的最后一行。如果事先明确知道要查找用户的 `user_id（主键）` ，那么是可以通过查找一遍所有的表数据来找到想要的数据。但是如果假设我们只知道用户的叫 `Jonh` 且他的邮箱是 `adc@gmail.com`，那么就需要最多查找 m^2 次：第一遍找出符合用户名的人，第二遍找出符合邮箱的人，最后把两遍的结果组合起来。

如果我们给用户表设置了以用户用户名和邮箱这两个字段组合而成的 索引（INDEX)，那么在数据库内部就已经存放着对应索引所代表的记录，当要查询时只需要查找一遍便可得到想要的结果。

因此，在数据库的表中选用哪些字段作为 键（KEY），往往会影响整个表的查询效率。
