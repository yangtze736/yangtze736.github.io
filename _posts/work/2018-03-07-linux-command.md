---
layout: post
author: yangtze736
title: 进程分析工具lsof strace pstack pstree使用
category: 工作
tags: 
keywords: 
description: 
---

Linux环境中下面的这几个命令都很重要，特别是在跟踪定位后台问题时候，需要熟练运用。

![1](/public/img/LinuxPerfTool.png)

strace命令是一种强大的工具，能够显示任何由用户空间发出的系统调用。Strace显示这些调用的参数并返回符号形式的值。

下面记录几个常用的option：

-f选项告诉strace同时跟踪fork和vfork出来的进程。

-o**.txt输出到某个文档。

-e execve只记录execve这类系统调用。

-p pid trace一个现有的进程。

`strace -fp $pid`

---


pstack是一个脚本工具，其核心实现是使用了gdb以及threadapply all bt 命令，下面使用pstack查看进程函数堆栈。

`pstack pid`


---

pstree命令以树状图显示进程间的关系（displaya tree of processes）。ps命令可以显示当前正在运行的那些进程的信息，但是对于它们之间的关系显示的并不清晰。在linux系统中，系统调用fork可以创建子进程，通过shell可以创建子进程，**Linux系统中进程间的关系天生是一棵树**，树的根就是进程PID为1的init进程。

`pstree -p 以树状图显示系统进程关系`

`pstree -pa 以树状图显示进程PID为<pid>的进程以及子进程`

---

lsof(list open files)是一个列出当前系统打开文件的工具。在linux环境下，任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。所以如传输控制协议 (TCP) 和用户数据报协议 (UDP) 套接字等，系统在后台都为该应用程序分配了一个文件描述符，无论这个文件的本质如何，该文件描述符为应用程序与基础操作系统之间的交互提供了通用接口。因为应用程序打开文件的描述符列表提供了大量关于这个应用程序本身的信息，因此通过lsof工具能够查看这个列表对系统监测以及排错很有帮助。

* losf常用指令

```
lsof <filename>    显示打开某个文件的进程
lsof +D <DIR>      显示所有打开该目录的进程，注意，如果不想显示其子目录的进程，可以使用+d。
lsof -u <username>   显示某个用户启动进程所打开的文件
lsof -u^<username>   显示除了某个用户外的其他所以用户的启动进程打开的文件
lsof -i [4|6][@hostname|ip][:service|port]       显示打开某ip或者端口的进程 （可以和netstat 配合使用）, 4和6 用来区分ip 协议
lsof -i    列出所有的网络连接（注意，网络连接也是文件）
lsof -i TCP:1-1024   列出所有使用TCP 协议，端口范围是1-1024 的网络连接
```

* lsof输出列表的含义

```
FD – Represents the file descriptor. Some of the values of FDs are

cwd – Current Working Directory
txt – Text file
mem – Memory mapped file
mmap – Memory mapped device
NUMBER – Represent the actual file descriptor. The character after the number i.e ‘1u’, represents the mode in which the file is opened. r for read, w for write, u for read and write.

TYPE – of files and it’s identification.

DIR – Directory
REG – Regular file
CHR – Character special file.
FIFO – First In First Out
```

---
**参照链接**

[shawnwang_vip](https://www.jianshu.com/p/b57a88e00765)

[5 simple ways to troubleshoot using Strace](http://hokstad.com/5-simple-ways-to-troubleshoot-using-strace)

[strace介绍](https://linuxblog.info/strace/)

