---
layout: post
author: yangtze736
title: 引用计数
category: 技术
tags: CPlusPlus
keywords: 
description: 
---

最近在看《提高c++性能的编程技术》一书，第十二章提到引用计数，要开始学习新知识了~

学习之前先抄录书中一段精彩的言论

> 于自然界，熵的原理亦可用于软件工程之中--所有实体都会随着时间的消逝而消失。一个软件项目最初可能源起于某个设计清晰、实现简单的小型原型系统。只包含基本功能的原型系统，要使它们满足上市需求，往往都要经历急速扩展的过程。这通常是为了满足客户对于新功能（有时是难解的）的突发性需求以及对原有瑕疵的改进需要。新功能的开发加上旧错误的修补会对原先清晰明了的设计造成重大破坏。随着时间的推移，设计和实现的清晰性也会随着代码维护及频繁的发布周期而不复存在，软件不可避免的变得紊乱不堪，区分软件好坏的唯一标准就是衰退率。

---------------

*引用计数是对共享的动态内存的一种管理方法，STL库的string就是用到了引用计数的方法*

###1. 概念

引用计数用来记录当前有多少指针指向同一块动态分配的内存。当有指针指向这块内存时，计数器加1；当指向此内存的指针销毁时，计数器减1。当引用计数为0时，表示此块内存没有被任何指针指向，此块被共享的动态内存才能被释放。


###2. STL库的string利用引用计数共享内存

```
#include <stdio.h>
#include <string>

using namespace std;

int main()
{
	string str1("aaa");
	printf("the address of str1 is: %x\n",str1.c_str());
	string str2("bbb");
	printf("the address of str2 is: %x\n",str2.c_str());
	string str3("aaa");
	printf("the address of str3 is: %x\n",str3.c_str());
	string str4 = str1;
	printf("the address of str4 is: %x\n",str4.c_str());
	str1 = str2;
	printf("the address of str1 is: %x\n",str1.c_str());
	str2[2] = 'a';
	printf("the address of str2 is: %x\n",str2.c_str());
}

```

结果如下：

the address of str1 is: 1816028

the address of str2 is: 1816058

the address of str3 is: 1816088    //str3和str1并不共享一块内存

the address of str4 is: 1816028    //str4和str1共享一块内存，所以初始化（string（const char*））和赋值（operator=（const string&））是不一样的

the address of str1 is: 1816058    //str1和str2共享一块内存

the address of str2 is: 18160b8    //写str2，因为引用计数的关系，要分配新的内存给str2

*STL string共享内存的原则(copy on write)：*

*利用初始化或赋值生成多个内容相同的string，当没有对这些string进行写操作时，它们会共享同一块内存。当有写操作出现时，才会有新的内存重新分配。*

###3. 利用引用计数实现简易String

问题：我们对String类的内存进行计数，但引用计数这个变量是类的什么类型的成员变量？

解答：

1) 普通私有成员变量，每个类都有一个独立的计数器变量，在新的类对象拷贝构造和赋值的时候需要进入原来的类对象中去获取这个计数器，再进行修改，在同一个类对象之间缺乏通用性。

2) static变量，类的所有对象公用一个计数器变量。字符串”aaaa”和”bbbb”使用不同的内存，它们应该分别有对应自己内存的计数器变量。

结论，对每一块内存分配一个变量，可以在动态分配字符串内存是多分配一个字节，将计数器的值放在第一个字节中；也可以在动态分配内存前先动态分配一个int型的内存来存放这个计数器。


---------------

在《More Effective C++》的Item 29中有关于引用计数的实现，可供参考
