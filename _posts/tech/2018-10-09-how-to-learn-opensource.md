---
layout: post
author: yangtze736
title: 如何高效学习github开源项目
category: 技术
tags: 
keywords: 
description: 
---

这一篇整理本来是为组内分享准备的（十分钟分享），后来换了一个话题来说，这部分内容就一直放在了MRU文件，重新梳理出来

### 写代码 VS 读代码

提高编码能力，主要是靠写代码和读代码，读代码能力在一定程度上还要重要于写代码

### github上值得关注的公司和个人

Facebook google alibaba tencent douban qiniu etc

云风 陈皓 陶叔度 庄晓丹 司徒正美 etc

### 整理认识开源项目 

开源项目整个目录架构，大体上是 src test doc include lib tests perf build 其核心实现一般都是在src下面需要主看

工程构建工具大致上有几种： bazel cmake makefile libtool shell 

编译安装过程大致： ./configure && make && make install

### 如何阅读项目代码

README文件一般都是整个项目的总结归纳，包含了基础的编译安装使用的说明信息，是首先需要看的内容

从main函数看起，熟悉大的框架，再入手自己感兴趣的点，不断深入学习

利用版本控制系统，可以从项目最开始提交的简单实现看起，还可以结合branch tag来看

著名的项目一般都有书籍和文档总结，结合docs学习

让代码跑起来，结合日志学习

### 开源可以学到的东西

源码之下 了无秘密（看别人如何制造轮子）

学习别人细节上的优秀的点，不断提高完善自己细节处理能力

把握技术潮流和趋势（docker travis）

开源项目多框架少业务，代码实现多是优美，设计模式学习

