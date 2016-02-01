---
layout: post
author: yangtze736
title: 谈谈C++的异常处理
category: 技术
tags: CPlusPlus
keywords: 
description: 
---

C语言里面的错误处理使用不同的数值表示不同类型的错误，传统的错误处理方法存在很大缺点：其表达能力有限；状态编码与错误码难以形成统一标准，例如同样用途的程序库，不同的开发商可能采用不同的错误编码方案来标识不同类型错误；有些函数没有返回值（例如构造函数和析构函数）等等。

C++异常处理机制将异常**类型化**，显然一个类型比一个数字包含的信息量大得多。C++保证，如果一个异常在抛出点没有得到处理，那么它将一直被抛向上层调用者，直至main函数，指导找到一个类型匹配的异常处理器，否则调用terminate结束程序。可以看出：**异常处理机制实际上是一种运行时通知机制**。

任何类型都可以当做异常类型，异常仅仅通过类型而不是通过值来匹配的。所以下面这段代码是OK的，只是我们一般不使用基本数据类型的对象作为异常。

{% highlight c %}

try{
 if(..) throw 0;
}
catch(int){
 cerr << "exception!" << endl;
}

try{
 if(..) throw "error msg";
}
catch(const char *){
 cerr << "exception!" << endl;
}

{% endhighlight %}

<!-- more -->

在使用异常处理机制的环境，应当使用函数异常说明，如示例所示：

`double Devide(double x, double y) throw(DevidedByZero);` // 只抛出这类异常

`bool func1(const char *p) throw(T1, T2, T3);` // 可能抛出三种异常

`void func2() throw();` // 不抛出任何异常 

`void func3();`         // 可能抛出任何异常，也可能不抛出异常

---

下面我就写一个工程上使用异常处理的demo做为文章的结束，比较简陋但已经展示了异常处理的用法。

{% highlight c %}

/*
 * Copyright (c) 2016 xxxx Inc, All rights reserved.
 * Created: 2016-02-01
 */

#include <iostream>
#include <exception>
#include <execinfo.h>
#include <stdlib.h>

#define DEFAULT_PROGRAM_NAME "TEST-PROGRAM"

class TestException : public std::exception{
public:
  TestException() {}
  virtual ~TestException() {}

  TestException(
    const char *errMsg ):
    m_program( DEFAULT_PROGRAM_NAME ),
    m_errmsg( errMsg )
  {
    getStackTrace();
  }

  /* Obtain a backtrace and print it to stdout. */
  void getStackTrace(void)
  {
    void *array[10];
    size_t size;
    char **strings;
    size_t i;

    size = backtrace(array, 10);
    strings = backtrace_symbols(array, size);

    for(i = 0; i < size; i++)
    {
      m_callStack += strings[i];
      m_callStack.push_back('\n');
    }
    free(strings);
  }

  virtual const char *what() { return m_errmsg.c_str(); }
  virtual const char *where() { return m_callStack.c_str(); }

protected:
  std::string m_program;
  std::string m_errmsg;
  std::string m_callStack;

}; 

void func(void)
{
  if(true)
    throw TestException("throw an exception in func.");
}

int main(int argc, char *argv[])
{
  try{
    func();
  }
  catch(TestException &e){
    std::cout << "catch TestException, " << e.what() << std::endl;
    std::cout << e.where() << std::endl;
  }

  return 0;
}

{% endhighlight %}
