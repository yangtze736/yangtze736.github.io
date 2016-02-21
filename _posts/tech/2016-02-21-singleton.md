---
layout: post
author: yangtze736
title: C++单例模式的简单实现
category: 技术
tags: CPlusPlus
keywords: 
description: 
---

*我不想说话；直接上代码了 :( * 将Singleton类的构造函数定义为private，这样就不能实例化为一个对象（抽象类），用户访问唯一实例的方法只有通过getInstance()成员函数。
**加入Garbo做为Singleton的内嵌类，程序运行结束时，系统会调用CSingleton的静态成员Garbo的析构函数，该析构函数会删除单例的唯一实例。**那么问题来了，如何写代码测试验证？我不知道啊 -。-

```c
/*
 * Copyright (c) 2016 xxxx Inc, All rights reserved.
 * Created: 2016-02-21
 */
#include <iostream>

class Singleton{

class Garbo{
public:
	Garbo(){std::cout << "Garbo" << std::endl;}
	~Garbo(){
		if(Singleton::m_instance != NULL)
		{
			std::cout << "delete m_instance" << std::endl;
			delete m_instance;
		}
	}
};

public:
	static Singleton *getInstance()
	{
		if(m_instance == NULL){
			std::cout << "m_instance = new Singleton();" <<std::endl;
			m_instance = new Singleton();
		}
	}

private:
	Singleton(){}

private:
	static Singleton *m_instance;
	static Garbo m_garbo;
};

Singleton *Singleton::m_instance = NULL;

int main()
{
	std::cout << "getInstance first" << std::endl;
	Singleton *p = Singleton::getInstance();
	std::cout << "address: " << static_cast<const void *>(p) << std::endl;

	std::cout << "getInstance second" << std::endl;
	Singleton *q = Singleton::getInstance();
	std::cout << "address: " << static_cast<const void *>(q) << std::endl;

	return 0;
}

```
