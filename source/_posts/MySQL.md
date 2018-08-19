---
title: MySQL踩过的坑
date: 2018-08-16 17:14:31
tags: [MySQL]
categories: MySQL
---

工作中遇到MySQL的一些坑
<!--more-->
## 1、MySQL分页数据顺序错乱重复
存储引擎为MyISAM时，按照物理存储顺序
存储引擎为InnoDB时，一般会按照主键排序，但并总不如此。
> Re: What is The Default Sort Order of SELECTS with no ORDER BY Clause?
> Do not depend on order when ORDER BY is missing. 
> Always specify ORDER BY if you want a particular order -- in some situations the engine can eliminate the ORDER BY because of how it does some other step. 
> GROUP BY forces ORDER BY. (This is a violation of the standard. It can be avoided by using ORDER BY NULL.) 
> SELECT * FROM tbl -- this will do a "table scan". If the table has never had any DELETEs/REPLACEs/UPDATEs, the records will happen to be in the insertion order, hence what you observed. 
> If you had done the same statement with an InnoDB table, they would have been delivered in PRIMARY KEY order, not INSERT order. Again, this is an artifact of the underlying implementation, not something to depend on.

不能过分依赖MySQL默认排序
带有distinct，order by的查询，可以写成这种形式避免：`SELECT * FROM (SELECT DISTINCT ... FROM ORDER BY ) LIMIT `

## 2、自增id批量插入不连续
这个问题得从MySQL InnoDB自增锁说起，自增锁是一种专门针对AUTO_INCREMENT列插入的表级别锁，为了提高MySQL的并发能力，在插入数据的行数确定时，如：`insert...`和`replace...`，如已知的插入行数位5，则InnoDB会预分配5个自增值，当有一个新的insert语句时，会读取AUTO_INCREMENT=5，这样就可以并发地执行insert语句。
但如果无法提前获取插入行数，如`insert...select...`,`replace...select`,`loda data...`,这个时候就使用表锁。
insert语句中有时会显示的设置自增字段的值，对于这种情况innodb还是会预分配给语句总行数的自增值而不是只有实际使用系统自增的行。因而有可能会造成自增字段的值不连续。
设置新自增互斥方式：通过配置选项：`innodb_autoinc_lock_mode`，它是专门用来在使用AUTO_INCREMENT的情况下调整锁策略的，目前有三种选择：
`innodb_autoinc_lock_mode = 0` (“traditional” lock mode：全部使用表锁)
`innodb_autoinc_lock_mode = 1` (默认)(“consecutive” lock mode：可预判行数时使用新方式，不可时使用表锁) 
`innodb_autoinc_lock_mode = 2` (“interleaved” lock mode：全部使用新方式，不安全，不适合replication)
(insert select语句对于自增列会预分配  如果不够就翻倍所以你插入4条语句就分配自增列到8  插入10条语句肯定就分配到16  插入20条语句就分配到32)