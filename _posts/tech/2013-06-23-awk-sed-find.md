---
layout: post
title: Awk、Sed、Find工具的使用
category: 技术
tags: Linux
keywords: 
description: 
---

最近在写shell脚本，主要工作就是解析配置文件，那么学习sed与awk就在所难免了，另外还系统的看了find命令的使用，现在就将这些成果汇总起来，以便今后参考。

### Awk命令

**awk 是一种编程语言，对文本和数据进行处理，支持正则表达式，突出特点是对文本列的操作。**

  *1 常见操作*

	awk ‘{print $1,$3}’ tmp.txt

	awk –F ‘:’ ‘{print $1”|”$3}’ tmp.txt  //以冒号作为域分割符并且输出的分割符为|

  *2 打印记录*

	awk ‘BEGIN {print “Begin   \n-----------------”}

	{print $0}’ tmp.txt //打印头

	awk ‘BEGIN {print “Begin   \n-----------------”}

	{print $0}

	END {print” -----------------\nEnd”}’ tmp.txt    //打印头尾

  *3 条件运算符*

	awk ‘{if ($1~/tomy/) print $0}’ tmp.txt //匹配文本域1为tomy的记录

	awk ‘$2==”30” {print $1,”age is 30.”}’ tmp.txt   //精确匹配

  *4 利用正则表达式*

	awk ‘/[Gg]reen/’ tmp.txt    //匹配Green或者green

	awk ‘$4~/[Gg]reen/’ tmp.txt

	awk ‘$4~/([Gg]reen|[Rr]ed)/’ tmp.txt    //或关系匹配

  *5 使用内置变量*

	awk ‘{print NF,NR,$0}

	END {print FILENAME}’ tmp.txt //NF为列数，NR为行号

  *6 使用内置字符串函数*

	awk ‘gsub (/30/,thirty) {print $0}’ tmp.txt    //把有30的行中的30改为thirty

	awk ‘BEGIN {print split(“123#456#789”,m_array,”#”)}’  //split返回数组m_array的下标数3，m_array取值：m_array[1]为123，m_array[2]为456，m_array[3]为789

  *7 输出函数printf*

	awk ‘{printf “%-10s---%s\n”,$1,$3}’ tmp.txt      //类似C语言printf函数

### Sed命令

**sed是非交互文本流编辑器，使用sed可以从文件和字符串中抽取所需信息。**

  *1 打印范围*

	sed –n ‘1,3p’ sed.txt > sed.tmp

	sed –n ‘1,$p’ sed.txt > sed.tmp

  *2 模式查询*

	sed –n ‘/should/p’ sed.txt //查找should的行

	sed –n ‘/should/=’ sed.txt //输出匹配的行号

  *3 特殊字符查询*

	sed –n ‘/\./’p sed.txt        //查找有.的行

  *4 删除文本*

	sed ‘1,3d’ sed.txt

	sed ‘/should/d’ sed.txt

  *5 替换文本*

	sed ‘s/PS/ps/’ sed.txt       //替换每行第一个匹配

	sed ‘s/PS/ps/g’ sed.txt           //全文替换

	sed ‘1,3 s/should/SHOULD/g’ sed.txt         //指定范围替换

	sed ‘1 s/should/SHOULD/g’ sed.txt      //指定行替换

  *6 sed脚本文件*

    6.1 含sed命令的脚本

```
　　$ cat append.sed

　　#！/bin/sed –f

　　/should/ a\

	Just a test.

	$ chmod +x append.sed

	$ ./append.sed sed.txt
```

    6.2 不含sed命令脚本

```
	$ cat append.sed

	/should/ a\

	Just a test.

	$ chmod +x append.sed

	$ sed -f append.sed sed.txt
```

### Find命令

  *1 name选项*

	find ~ -name “*.txt” –print

	find . –name “[A-Z]*” –print

  *2 perm选项*

	find . –perm 755 -print

  *3 按更改时间查找*

	find / -mtime -5 –print     //更改时间在五日以内

	find / -mtime +3 –print    //更改时间在三日之前

  *4 type选项*

	find /etc –type d –print    //查找/etc目录下所有的目录

	find /etc –type l –print     //查找/etc目录下所有的符号链接文件

  *5 使用exec或ok*

	find . –type f –exec ls –l {} \; //注意{}和\;之间有空格

	find . -name “*.log” –mtime +5 -exec rm {} \;


