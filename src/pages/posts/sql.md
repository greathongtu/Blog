---
layout: '../../layouts/MarkdownPost.astro'
title: 'sql 总结'
pubDate: 2022-03-14
description: 'SQL 学习的总结'
author: 'Aaron'
cover:
    url: '/preview/sql.png'
    alt: 'cover'
tags: ["sql", "Web"]
theme: 'light'
featured: false
---
# mysql 执行流程
SQL语句 - 查询缓存 - 解析器（语法分析 语意分析） - 优化器（逻辑优化 物理优化） - 执行器

# 为什么说 B+ 树查找行记录，最多只需1~3次磁盘IO
InnoDB 中页大小为16KB，一个键值对大概 8B+8B， 也就是一个页可以存1K个 KV pair，1K约等于1000个。 所以深度为3的 b+ 树索引可以维护 10^3 * 10^3 * 10^3 = 10 亿条记录。 正常每个节点不可能都填满了，所以一般 b+ 树高度在2到4 层。根节点在内存内，所以一般查找某一 KV 的行记录时只需1到3次磁盘IO

# 缓冲池 Checkpoint

缓冲池的设计是为了协调 CPU 速度与磁盘速度的鸿沟。
若每次一个页发生变化，就将新页的版本刷新到磁盘，开销太大。且若刷新时发生宕机，数据就无法恢复了。所以引入 `Write Ahead Log` 策略：事务提交时，先写重做日志，再修改页。这是 ACID 中 Durability 的要求。

Checkpoint： 
    1. 缩短数据库恢复时间。
    2. 缓冲池不够用时，脏页刷新到磁盘
    3. redolog 不可用时，刷新脏页

# InnoDB 关键特性
    * Insert Buffer 升级为 Change Buffer
        对于非聚集索引的插入或更新操作，不是每次直接插入到索引页，而是先判断插入的非聚集索引页是否在缓冲池中，若在则直接插入；若不在，则先放入一个 `Insert Buffer` 对象中，然后再以一定的频率和情况进行 Insert Buffer 和辅助索引页子节点的 merge 操作。如此一来多个插入合并到一个操作中（因为在一个索引页中）。
        条件： 1. 索引是辅助索引； 2. 索引不是唯一的。
        Insert Buffer 是一个 B+ 树
    * Double Write
    * Adaptive Hash Index
    * Async IO
    * Flush Neighbor Page