---
layout: '../../layouts/MarkdownPost.astro'
title: '基础知识'
pubDate: 2023-03-19
description: '基础知识学习'
author: 'Aaron'
cover:
    url: '/preview/csapp.jpg'
    alt: 'cover'
tags: ["basic", "interview"]
theme: 'light'
featured: false
---

## dfs

bool dfs()
什么时候return dfs(next condition); 什么时候 if(dfs(next condition)) { return true; }

看路径是否唯一：前者是唯一路径的情况；后者是枚举所有可能性找一种可能的正确路径的时候。只有前者才可以使用备忘录。



## 进程上下文
进程上下文是进程执行活动全过程的静态描述。我们把已执行过的进程指令和数据在相关寄存器与堆栈中的内容称为进程上文，把正在执行的指令和数据在寄存器与堆栈中的内容称为进程正文，把待执行的指令和数据在寄存器与堆栈中的内容称为进程下文。

实际上linux内核中，进程上下文包括进程的`虚拟地址空间`和`硬件上下文`。

进程上下文切换主要涉及到两部分主要过程：进程地址空间切换和处理器状态切换。地址空间切换主要是针对用户进程而言，而处理器状态切换对应于所有的调度单位。

所以，对于线程和进程，我们可以这么理解： - 当进程只有一个线程时，可以认为进程就等于线程。 - 当进程拥有多个线程时，这些线程会共享相同的虚拟内存和全局变量等资源。这些资源在上下文切换时是不需要修改的。 - 另外，线程也有自己的私有数据，比如栈和寄存器等，这些在上下文切换时也是需要保存的。

## 虚拟内存
使用虚拟寻址，CPU 通过生成一个虚拟地址（Virtual Address）来访问主存，这个虚拟地址在被送到内存之前先被CPU上的`内存管理单元（Memory Management Unit）`转换为适当的物理地址。

主存中的`每个字节`都有一个选自虚拟地址空间的`虚拟地址`和一个选自物理地址空间的`物理地址`.

`SRAM`缓存表示位于 CPU 和主存之间的L1、L2、L3 高速缓存，`DRAM` 表示虚拟内存系统的缓存，它在主存中缓存虚拟页。SRAM不命中就要由DRAM服务；DRAM不命中就要由磁盘服务，这是很昂贵的。所以说，DRAM 缓存的组织结构完全是由巨大的不命中开销驱动的。

`页表`将虚拟页映射到物理页。缺页时触发缺页异常，调用内核中的缺页异常处理程序。
每个进程有一个独立的页表，因而也就是一个独立的虚拟地址空间，多个虚拟页面可以映射到同一个共享物理页面上。

## 多线程

## 网络