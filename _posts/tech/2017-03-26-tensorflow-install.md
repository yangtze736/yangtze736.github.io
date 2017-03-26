---
layout: post
author: yangtze736
title: 安装tensorflow
category: 技术
tags: AI
keywords: 人工智能, 深度学习, 神经网络
description: 
---

最近心血来潮想看看google的tensorflow项目，试着在新买的mac本上安装了玩一玩。安装过程也很简单，有丰富的中文版官方文档参考。

只需一行:

> pip install https://storage.googleapis.com/tensorflow/mac/tensorflow-0.5.0-py2-none-any.whl

在此之前，Mac是默认安装了Python 2.7.10，但需要你自己另外安装pip工具。

> sudo easy_install pip

安装过程🈶️报错误，原因是大致就是说numpy已安装，但是呢版本太低了，尝试着卸载和升级numpy就出错，错误信息如下💻

```

192:~ yangtze$ pip install -U numpy
Collecting numpy
  Using cached numpy-1.12.1-cp27-cp27m-macosx_10_6_intel.macosx_10_9_intel.macosx_10_9_x86_64.macosx_10_10_intel.macosx_10_10_x86_64.whl
Installing collected packages: numpy
  Found existing installation: numpy 1.8.0rc1
    DEPRECATION: Uninstalling a distutils installed project (numpy) has been deprecated and will be removed in a future version. This is due to the fact that uninstalling a distutils project will only partially uninstall the project.
    Uninstalling numpy-1.8.0rc1:
Exception:
Traceback (most recent call last):
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/basecommand.py", line 215, in main
    status = self.run(options, args)
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/commands/install.py", line 342, in run
    prefix=options.prefix_path,
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_set.py", line 778, in install
    requirement.uninstall(auto_confirm=True)
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_install.py", line 754, in uninstall
    paths_to_remove.remove(auto_confirm)
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_uninstall.py", line 115, in remove
    renames(path, new_path)
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/utils/__init__.py", line 267, in renames
    shutil.move(old, new)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 302, in move
    copy2(src, real_dst)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 131, in copy2
    copystat(src, dst)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 103, in copystat
    os.chflags(dst, st.st_flags)
OSError: [Errno 1] Operation not permitted: '/var/folders/l0/gc9k2fw13d5f5jvl3l2yh8hm0000gn/T/pip-4wa7Fs-uninstall/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/numpy-1.8.0rc1-py2.7.egg-info'

```

----------

查了半天资料原来是Mac系统开启的SIP导致的，据说是因为XCode编译器代码被注入的事件后，Mac OS X El Capitan系统的升级，启用了更高的安全性保护机制：系统完整性保护System Integrity Protection (SIP)。简单来讲就是更加强制性的保护系统相关的文件夹，开发者不能直接操作相关的文件内容。

那么解决方案就是暂时的关闭系统SIP，操作步骤如下；

* 点击Mac电脑的苹果图标 

* 选择 重新启动

* 按住 command＋R，直到进入还原模式 

* 选择实用工具，然后点击 终端 

* 输入 csrutil disable 按下回车 

* 重启电脑

棘手的问题解决，之后你只要执行`pip install https://storage.googleapis.com/tensorflow/mac/tensorflow-0.5.0-py2-none-any.whl`就可以顺利安装tensorflow了。(现在你也可以再次的开启SIP保护，步骤类似，只要在终端输入 csrutil enable)

最后以官方用例做为结尾

```

192:~ yangtze$ python
Python 2.7.10 (default, Jul 30 2016, 19:40:32) 
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.34)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print sess.run(hello)
Hello, TensorFlow!
>>> a = tf.constant(10)
>>> b = tf.constant(32)
>>> print sess.run(a+b)
42
>>> quit()

```



