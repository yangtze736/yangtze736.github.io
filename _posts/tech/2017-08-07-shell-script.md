---
layout: post
author: yangtze736
title: Shell脚本
category: 技术
tags: shell script
keywords: 
description: 
---

### 一个脚本骨架

自己写了个脚本框架，实现了简单日志定位和异常捕获，以下几点可增强脚本健壮性

1) set -o nounset 

当你使用未初始化的变量时让bash自动退出

2) set -o errtrace 

告诉bash一旦有任何语句出错则退出

3) command ; if [ "S?" -ne 0 ]; then ... fi

使用$?判断上一句执行结果

4) command `||` echo "command failed"

使用 `||`号

5) trap func ERR 

使用trap捕获异常

---

**脚本实现如下，供参考：**

```

#!/bin/sh -
# Description: 脚本框架示例，注意外部调用时return值
# Author: jiangzhe

IFS='
    '
UMASK=002
umask $UMASK

#set -o nounset
set -o errtrace
set -o errexit

START_DIR=`pwd`
trap "traperror $LINENO ${FUNCNAME} $BASH_LINENO" ERR
trap "cd $START_DIR" EXIT

SUCCESS='[  \033[1;32mOK\033[0;39m  ]'
FAILURE='[\033[1;31mFAILED\033[0;39m]'

# 日志文件，推荐使用绝对路径
LOG_FILE_PATH=$HOME
LOG_FILE=$LOG_FILE_PATH/shell.log

# 是否打印屏幕
SHOW_CONSOLE=Y

function usage()
{
    cat <<EOF
Usage:
    脚本使用说明：
        -d | --debug    脚本调试
        -v | --version  获取版本信息
        -h | -- help    获取帮助
    执行：
        sh skeleton.sh start
    
EOF
}

function version()
{
    echo "Skeleton 1.0.0"
    echo ""
    echo "Copyright (C) 2017, Jiangzhe."
    echo "All rights Reserved."
    echo ""
}

function log_info()
{
    if [ $# -lt 1 ]; then
        return
    fi
    echo "$(date +"%F %T") [INFO] $*" >> $LOG_FILE
    [ $SHOW_CONSOLE == Y ] && echo "$(date +"%F %T") [INFO] $*"
}

function traperror()
{
    local err=$?
    local line=$1
    [ "$2" != "" ] && local funcstack=$2
    [ "$3" != "" ] && local linecallfunc=$3
    log_info "<-----"
    log_info "Error: line $line - command exit with status: $err"
    if [ "funcstack" != "" ];then
        log_info "   ... Error at function ${funcstack[0]}() "
        if [ "$linecallfunc" != "" ];then
            log_info "called at line $3"
        fi
        echo ""
    fi
    log_info "----->"
｝

function do_kill()
{
    ps -ef| grep $@ ｜awk '{print $2}'| xargs kill -KILL
}

function do_start()
{
    #ulimit -c unlimited -n 81920 -s 1024
    #./test &
    # TODO
    log_info "trying to starting test ..."
    echo -e "starting test:        $SUCCESS"
}

function do_stop()
{
    #do_kill test
    # TODO
    log_info "trying to stopping test ..."
    echo -e "stopping test:        $SUCCESS"
}

function parseArgs()
{
    while [ $# -gt 0 ]
    do
        case "$1" in
        -d | -debug | --d | --debug )
            set -x
            ;;
        -h | -help | --h | --help )
            usage
            ;;
        -v | -version | --v | --version )
            version
            ;;
        start)
            do_start
            ;;
        stop)
            do_stop
            ;;
        restart)
            do_stop
            sleep 2
            do_start
            ;;
        status)
            ;;
        esac
        
        shift
    done
}

#### MAIN ####

echo `date +"[%Y-%m-%d %H:%M]"`: $0 $@

if [ $# -eq 0 ]; then
    usage
    exit 255
fi

parseArgs $*

exit 0

```

<!-- more -->

### 修改Json配置脚本

这些配置文件结构简单，所以shell脚本写的也很简单，手写了一份做备忘，脚本大致思路就这样了，没有做测试。脚本运行环境CentOS 6.5

shell脚本参考:

```
#!/bin/sh

CONF_FILE=./test.json

function modifyConf()
{
    local val=`grep \"$1\" $CONF_FILE|awk -F " " '{print$3}'`
    
    local test1=`expr index $val ','`
    local test2=`expr index $val '\"'`
    if [ $test1 -eq 0 ]; then
        if [ $test2 -eq 0 ]; then
            `sed -i "s#"$val"#"$2"#g" $CONF_FILE`
        else
            `sed -i "s#"$val"#\""$2"\"#g" $CONF_FILE`
        fi
    else
        if [ $test2 -eq 0 ]; then
            `sed -i "s#"$val"#"$2",#g" $CONF_FILE`
        else
            `sed -i "s#"$val"#\""$2"\",#g" $CONF_FILE`
        fi
    fi
}

function setConfig()
{
    #结尾追加逗号
    local content="$1,"
    
    #解析参数，格式 "key1=value1,key2=value2..."
    local arr=$(echo $content|tr "," "\n")
    for i in $arr; do
        local key=`echo $i|awk -F "=" '{print $1}'`
        local val=`echo $i|awk -F "=" '{print $2}'`
        modifyConf $key $val
    done
}

setConfig $1

```

---

test.json文件参考：

```
｛
   "server_virtual_ip" : "192.168.1.1",
   "server_port" : 8080,
   "conf_file_path" : "/etc/",
   "conf_file_name" : "ser.conf",
   "cluster_ip" : "",
   "cluster_port" : 12800
 ｝
```
