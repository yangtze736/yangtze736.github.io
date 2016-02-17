---
layout: post
author: yangtze736
title: Git常用命令速查表
category: 技术
tags: Git Linux
keywords: 
description: 
---

现在越来越多的开源项目也都迁移到GIT上了，可以说它是一个旅行必备、居家良
伴、送礼自用两相宜的VCS（版本控制系统），下文涵盖了一些基础的和较常用的
git操作，应该足以应对日常的工作需要。

### GIT配置
使用Git的第一件事就是设置你的名字和email,这些就是你在提交commit时的签名。

{% highlight c %}

$ git config --global user.name “your_name”
$ git config --global user.email “your_name@company.com”
$ git config --global color.ui true
$ git config --global core.editor vi

{% endhighlight %}

### SSH-KEY

{% highlight c %}

$ ssh-keygen -t rsa -C "your_email@youremail.com"
$ cat ~/.ssh/id_rsa.pub

{% endhighlight %}

将内容复制下来贴到Github帐号->Account Settings -> SSH Keys里
这样在Github开专案，就可以push和pull下来了。

### GIT ALIAS
一些好用的git alias，让我想起了项目中之前使用过的CVS版本控制系统

{% highlight c %}

$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.st status
$ git config --global alias.br branch

{% endhighlight %}

这样只要输入git st 就是git status 了,git 设定档的位置在~/.gitconfig，
你也可以直接修改这个档案。

### 忽略某些文件

项目中经常会生成一些Git系统不需要追踪(track)的文件，典型的是在编译生成
过程中产生的文件或是编程器生成的临时备份文件。当然，你不追踪(track)这些
文件，可以平时不用"git add"去把它们加到索引中。但是这样会很快变成一件烦
人的事，你发现项目中到处有未追踪(untracked)的文件; 这样也使"git add ." 
和"git commit -a" 变得实际上没有用处，同时"git status"命令的输出也会有
它们。你可以在你的顶层工作目录中添加一个叫".gitignore"的文件，来告诉Git
系统要忽略掉哪些文件。

你也可以把".gitignore" 这个文件放到工作树(working tree)里的其它目录中，这
就会在它和它的子目录起忽略(ignore) 指定文件的作用。.gitignore文件同样可以
像其它文件一样加到项目仓库里(直接用git add .gitignore 和 git commit等命令),
这样项目里的其它开发者也能共享同一套忽略文件规则。

<!-- more -->

### 标准工作流

{% highlight c %}

$ vi test.cpp
$ git add test.cpp
$ git commit -m "commit info"

{% endhighlight %}

### 撤销

{% highlight c %}

$ git reset HEAD^              #留着修改在working tree
$ git reset HEAD^ --soft       #修改放到staging area
$ git reset HEAD^ --hard       #完全清除

{% endhighlight %}

快速修正上一个commit（比如说之前的commit有typo），我们可以

{% highlight c %}

$ git reset HEAD^
edit files, modify typo and somethings.
$ git commit -am"commit info"

{% endhighlight %}

### 标签（TAG）

{% highlight c %}

$ git tag PROJECT_NAME_20130513                    #轻量级标签
$ git push origin PROJECT_NAME_20130513            #推到远端repo中
$ git checkout  PROJECT_NAME_20130513              #貌似和分支间切换一样
$ git tag -d PROJECT_NAME_20130513                 #删除本地tag
$ git push origin :refs/tags/PROJECT_NAME_20130513 #删除远端tag

{% endhighlight %}

### 分支操作

{% highlight c %}

$ git branch                           #列出目前有哪些branch以及目前在那个branch
$ git checkout <branch_name>           #切换到指定分支
$ git branch <new_branch_name>         #建立本地local branch
$ git branch -m <old_name> <new_name>  #改名字，如果有同名会失败，改用-M可以强制覆盖
$ git branch -d <branch_name>          #删除local branch
$ git push origin :branch_name         #删除remote branch，注意origin和冒号间有空格
$ git checkout --track -b branch_20130415 origin/branch_20130415   #从远程仓库拉分支并立即切换到该分支
$ git branch --set-upstream branch_20130210 origin/branch_20130210 #将一个已经存在的branch设定成tracking远端的branch
$ git push origin local_branch_name: remote_branch_name            #将本地的分支push到remote

{% endhighlight %}

### 合并与衍合

{% highlight c %}

$ git merge <branch>           #合并指定分支到当前分支
$ git rebase <branch>          #衍合指定分支到当前分支

{% endhighlight %}

### 其他

{% highlight c %}

$ git log -p          #列出不同提交之间的差异内容
$ git log --stat      #显示每次提交中哪些文件被修改,分别添加或删除了多少行内容
$ git log --pretty=format:'%h : %s' --topo-order --graph

{% endhighlight %}

---

### 资料链接
1. [Git Book](http://git-scm.com/book/zh/v2)
2. [Git 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)

