---
layout: post
author: yangtze736
title: 我的GIT配置文件
category: 工作
tags: 
keywords:
description:
---

Git有一个工具被称为git config，它允许你获得和设置配置变量，用以控制Git的外观和操作的各个方面，这些变量可以被存储在三个不同的位置：

**一** /etc/gitconfig 文件

**二** ~/.gitconfig 文件，这是我的GIT文件配置，如下：

{% highlight bash linenos %}

[user]
    email = jiangzheahu@126.com
    name = jiangzhe

[push]
    default = matching

[color]
    ui = true

[core]
    editor = vim

[alias]
    co = checkout
    ci = commit
    st = status
    br = branch -v
    rt = reset --hard
    unstage = reset HEAD^
    uncommit = reset --soft HEAD^
#l = log --pretty=oneline -- abbrev-commit --graph --decorate
    l = log --pretty=format:'%h : %s' --topo-order --graph
    g = grep -n --color -E
    cp = cherry-pick -x
    nb = checkout -b
    amend = commit --amend
    who = shortlog -n -s --no-merges

#'git add -u' handles deleted files, but not new files
#'git add .' handles any current and new files, but not deleted
#'git addall' noe handles all changes
    addall = !sh -c 'git add . && git add -u'

#Handy shortcuts for rebasing
    rc = rebase --continue
    rs = rebase --skiip
    ra = reabse --abort

{% endhighlight %}


**三** 位于git目录的config文件(也就是 .git/config)，这里稍微瞅瞅：

{% highlight bash linenos %}

[core]
    repositoryformatversion = 0 
    filemode = true
    bare = false
    logallrefupdates = true
[remote "origin"]
    url = https://github.com/yangtze736/yangtze736.github.io
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master

{% endhighlight %}
