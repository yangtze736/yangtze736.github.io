---
layout: post
author: yangtze736
title: 我的GIT配置文件
category: 工作
tags: 
keywords:
description:
---

这是我的GIT文件配置，如下：

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
