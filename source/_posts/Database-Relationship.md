---
title: Database Relationship
tags:
  - 个人总结
categories:
  - Database
date: 2023-04-10 17:38:51
---

> 在 Youtube 上刷到了一个关于数据库设计的视频，看了一下感觉还不错，这几天应该都会看一点然后每天做一下记录。视频地址：[Database Design Course](https://www.youtube.com/watch?v=ztHopE5Wnpc&list=PLo47zzoJD98Jn5Z3e3003jzqTaATKuDnI)

Database Design Course：

https://www.youtube.com/watch?v=ztHopE5Wnpc&list=PLo47zzoJD98Jn5Z3e3003jzqTaATKuDnI

## 为什么要使用关系？

使用关系关联的目的是为了减少**数据冗余**，比如我们要记录用户和用户卡片相关的信息，如果不使用关联关系，则数据存储如下：

| user_id | user_name | age       | sex       | card_id   | card_name |
|---------|-----------|-----------|-----------|-----------|-----------|
| 001     | John      | 24        | man       | 1         | GoldCard  |
| 002     | Mary      | 22        | female    | null      | null      |

不难发现，因为不是每个人都会拥有卡片，当遇到那些没有卡片的人时，卡片信息的相关字段便会**记录为没有意义的null，空间被白白浪费掉**。这时如果使用关联关系则会如下：

| user_id | user_name | age       | sex       |
|---------|-----------|-----------|-----------|
| 001     | John      | 24        | man       |
| 002     | Mary      | 22        | female    |

| card_id   | card_name | belong_user_id |
|-----------|-----------|---------|
| 1         | GoldCard  | 001       |

用户表通过 foreign key(belong_card_id)与卡片表的primary key(card_id)相关联，从而达到减少数据冗余的情况。

当然除了一对一的关系，也可能存在一个人有多张卡的情况，也有可能一张卡被多个人共用的情况，这就是一对多和多对一的关联关系了。

**在有关联关系的两个表之间，必定有一个是父表（parent table），另一个为子表（child table），父表不知道有哪些子表关联着它，而子表可以通过 foregin key 从父表中查找出对于关联的信息。**

一般情况下，关系型数据可以通过使用 Primary Key 和 Foreign Key 来很好地处理 One to One（一对一）, One to Many（一对多）, Many to One（多对一）的关系，但是对于 Many to Many（多对多）的关系是需要一些技巧来处理的。

对于多对多的关联关系，我们通常使用 **中间表（Intermediary Table）** 的技巧来优化我们的表数据。

我们假设一个人可以有多张卡，一张卡也可以被多个人共同使用，那么表结构如下：  

用户表：
| user_id | user_name | age       | sex       |
|---------|-----------|-----------|-----------|
| 001     | John      | 24        | man       |
| 002     | Mary      | 22        | female    |

卡片表：
| card_id   | card_name |
|-----------|-----------|
| 1         | GoldCard  |

用户、卡片关系表：
| user_id   | card_id |
|-----------|---------|
| 001       | 1       |

通过用户、卡片关系表可以得知，001用户拥有编号为1的卡片，如果001还拥有其他卡片，都可以通过该表查出对应的卡片信息。