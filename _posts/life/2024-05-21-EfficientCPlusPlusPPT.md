---
layout: post
author: yangtze736
title: 提高C++性能的编程技术
category: 技术
tags: Performance Programming Techniques
keywords: Performance 
description: 
---

## 提高C++性能的编程技术

### 导读
![1](/public/img/CPlusPlusPerfProTech.png)

### chapter 1

通过实现Trace跟踪，演示了如何提高性能

### chapter 2

#### 2.1 RAII实现，继承开销

#### 2.2 说明了Trace类实现，String数据成员的复合开销

#### 2.3 使用的地方再创建c++类对象

#### 2.4 冗余构造，使用显示初始化

<!-- more -->

### chapter 3 

c++多态特性，虚函数性能损失主要在无法内联。模板兼具重用和效率，可以提高性能。

### chapter 4

RVO（返回值优化）

### chapter 5

临时对象对性能的影响（临时对象产生原因：定义、类型不匹配、值传递、值返回）

### chapter 6 单线程内存池实现

### chapter 7 多线程内存池实现

### chapter 8 内联

### chapter 9 内联

### chapter 10 内联

### chapter 11 标准模板库 STL

数组array 向量vector list multiset这几种STL容器的插入、删除、遍历、查询操作的性能对比。

### chapter 12 引用计数

### chapter 13 编码优化

### chapter 14 设计优化

### chapter 15 可扩展性

#### 15.1 将任务做分解：将大的任务分为小任务，使线程并发地执行这些小任务。

#### 15.2 缩小临界区：临界区应该只包含关键代码，不直接操作共享资源的代码不要放在临界区内。

#### 15.3 减小锁粒度：不要用同样的锁来保护所有资源，除非这些资源是同时更新的。

#### 15.4 读写锁：读多写少可以考虑使用读写锁。

#### 15.5 伪共享：不要在类定义里把两个使用频度都很高的锁放太靠近，它们共享同一个缓存行可能触发缓存一致性风暴。

### chapter 16 体系结构
