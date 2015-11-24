---
layout: post
author: yangtze736
title: Git Submodule的使用
category: 技术
tags: Git
keywords: 
description: 
---

这两天在看facebook放在github上的开源项目，C++还挺多的。让我发现了git submodule这个东西，这里拿来说说。

Git子模块功能允许你将一个Git仓库当作另外一个Git仓库的子目录，允许你克隆另外一个仓库到你的项目中并且保持你的提交相对独立。这样既不会造成代码的冗余，也不丢弃公共代码库的维护历史，真的很**牛逼**啊

这里拿我的Winterfell工程做测试，来熟悉一下git submodule的操作

###为当前工程添加子模块

> git submodule add https://github.com/google/snappy git_submodule_test

执行此命令后，当前目录下新增.gitmodules文件和git_submodule_test目录，进入目录可以看到snappy的源码。.gitmodules文件内容如下

<!-- more -->

{% highlight bash linenos %}

[submodule "git_submodule_test"]
	path = git_submodule_test
	url = https://github.com/google/snappy

{% endhighlight %}

我们把做的修改提交至服务器端（add，commit，push），然后看看效果

![1](/public/img/git-submodule.png)

**当使用git clone下来的工程中带有submodule时，初始的时候，submodule的内容并不会自动下载下来的，此时，进入工程目录，执行如下命令即可将子模块内容下载下来。**

> git submodule update --init --recursive


###删除子模块

下班不写了，肚子好饿， 呵呵哒~

