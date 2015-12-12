---
layout: post
author: yangtze736
title: Repack a .deb archive
category: 技术
tags: Debian
keywords: 
description: 
---

长话短说，我要将.deb文件解包，替换上我的补丁文件，然后再重新打包回来。这样我可以其它电脑上安装，或者将修正的包文件发给我的朋友。我要怎么做呢？

只需要以下5条命令：

**mkdir -p extract/DEBIAN**

**dpkg-deb -x package.deb extract/**

**dpkg-deb -e package.deb extract/DEBIAN**

[...do something, e.g. edit the control file...]

**mkdir build**

**dpkg-deb -b extract/ build/**

---

- 第一条命令创建了一个名为“extract”文件夹和一个名为“DEBIAN”的子文件夹；

- 第二条命令会从你的.deb包提取一些文件到“extract”文件夹；

- 第三条命令会解压.deb包的内容到“DEBIAN”子文件夹，在那里你就可以修改／补丁你想要的文件；

- 第四条命令建立一个名为“build”的文件夹；

- 而第五条命令会将修改后的文件重新构建到一个新的.deb包中，并在 “build” 文件夹中生成。
