---
layout: post
author: yangtze736
title: 实现shell脚本中的转圈、进度条等一些效果
category: 技术
tags: shell script
keywords: 
description: 
---

要将shell脚本做的好玩好看，核心就是echo的使用问题，还有就是一些实现上的小技巧，从网上学习整理了一些做个备份。

### 一、Echo


- 开始shell光标闪烁

`echo -e "\033[5m . \033[0m" `


### 二、转圈效果

```
function waiting()
{
    i=0
    while [ $i -le 100 ]
    do
        for j in '\\' '|' '/' '-'
        do
            printf "\t\t\t\t%c%c%c%c%c test waiting %c%c%c%c%c\r" \
            "$j" "$j" "$j" "$j" "$j" "$j" "$j" "$j" "$j" "$j"
            sleep 0.1
        done
        let i=i+4
    done
}
waiting
```

### 三、进度条效果

1.  通过符号#填充[ ]完成进度

```
#!/bin/bash
i=0
str=""
arry=("\\" "|" "/" "-")
while [ $i -le 100 ]
do
    let index=i%4
    printf "[%-100s] %d %c\r" "$str" "$i" "${arry[$index]}"
    sleep 0.1
    let i=i+1
    str+="#"
done
echo ""
```

2.  每个阶段有不同颜色区分进度

```
#!/bin/bash
i=0
str=""
arry=("\\" "|" "/" "-")
while [ $i -le 100 ]
do
    let index=i%4
    if [ $i -le 20 ]; then
        let color=44
        let bg=34
    elif [ $i -le 45 ]; then
        let color=43
        let bg=33
    elif [ $i -le 75 ]; then
        let color=41
        let bg=31
    else
        let color=42
        let bg=32
    fi
    printf "\033[${color};${bg}m%-s\033[0m %d %c\r" "$str" "$i" "${arry[$index]}"
    usleep 30000
    let i=i+1
    str+="#"
done
echo ""
```

3. 按照百分比显示进度

*printf %3d*为显示百分比预留了3位，循环条件i每次递增5主要是为了减少进度条显示的长度

*i每次递增5，取模之后的index值还是依次循环，保证了转圈圈的方向和频率*

改用其他数字，转圈的显示效果没有add=4n+1时的好

```
#!/bin/bash
i=0
str=""
array=("|" "/" "-" "\\")
while [ $i -le 100 ]
do
    let index=i%4
    printf "%3d%% %c%-20s%c\r" "$i" "${arry[$index]}" "$str" "${arry[$index]}"
    sleep 0.2
    let i=i+5
    str+="*"
done
echo ""
```
