---
layout: post
author: yangtze736
title: UEFI安全启动
category: 技术
tags: OS
keywords: 
description: 
---

## 介绍

简言之，Secure Boot的原理就是通过验证机制来确保安全启动的过程，UEFI 规范规定固件可以包含一系列签名，并拒绝运行未签名或签名与固件中包含的签名不一致的 EFI 可执行文件，实践中我们是通过X509证书实现安全启动链。根证书被内嵌在固件中，可用于检验被签名的引导装载程序（bootloader），引导装载程序则用来验证被签名的内核（kernel）或者二级引导加载程序，以此类推。

以下各种数据库，为安全签名提供了灵活性和安全性：

*DB（又名“签名数据库”）：包含的信任密钥可用于验证UEFI环境下执行的应用和驱动程序
*DBX（又名 “禁止签名数据库”或“签名数据库黑名单”）：包含一系列不可信的键和二进制散列，用这些键签署的或是散列与之匹配的应用和驱动程序将不可执行。
*KEK（密钥交换密钥数据库）：包含一组密钥，用以更新DB和DBX
*PK：更新KEK数据库

ubuntu使用以下的信任链：

1. 当开机并开启安全启动，固件核实第一阶段启动加载程序（shim-signed），这时我们就需要用微软公司UEFI证书来给shim做签名。
2. 第二阶段引导装载器（grub-efi-amd64-signed）需要第一阶段引导装载程序验证grub2引导加载是否正确地签署。
3. grub2启动并验证ubuntu内核是否正确签署。
4. 如果支持内核模块的签名，那么内核则在启动之后校验模块。


## 如何签名

使用工具sbkeysync更新签名，维护UEFI密钥数据库之前，首先需要确认：

* 构建secure boot tool（apt-get install sbsigntool）
*efivars文件系统内核
*固件支持UEFI安全启动，并可配置为设置模式（setup mode）


（1）生成密钥

openssl genrsa -out test-key.rsa 2048

（2）生成自签名证书

openssl req -new -x509 -sha256 \
	-subj '/CN=test-key' -key test-key.rsa -out test-cert.pem

openssl x509 -in test-cert.pem -inform PEM \
        -out test-cert.der -outform DER

（3）sbsiglist生成EFI签名列表

sbsiglist --owner $guid --type x509 --output test-cert.der.siglist test-cert.der

（4）sbvarsign生成EFI签名数据库

for n in PK KEK db
> do
>   sbvarsign --key test-key.rsa --cert test-cert.pem \
>     --output test-cert.der.siglist.$n.signed \
>     $n test-cert.der.siglist
> done

（5）创建密钥存储库

我们把之前生成的签名数据库文件拷入相应目录，以便sbkeysync执行时可以找到。

sudo mkdir -p /etc/secureboot/keys/{PK,KEK,db,dbx}

sudo cp *.PK.signed /etc/secureboot/keys/PK/

sudo cp *.KEK.signed /etc/secureboot/keys/KEK/

sudo cp *.db.signed /etc/secureboot/keys/db/

（6）sbkeysync同步固件密钥数据库

sbkeysync --verbose --pk

（7）至此，安全启动已生效，签名引导程序

sbsign --key test-key.rsa --cert test-cert.pem \
        --output grubx64.efi /boot/efi/EFI/ubuntu/grubx64.efi
sudo cp /boot/efi/EFI/ubuntu/grubx64.efi{,.bak}

sudo cp grubx64.efi /boot/efi/EFI/ubuntu/

（8）恢复设置模式

理论上，我们有了PK的私有密钥元件，可以从用户模式恢复到设置模式，这只需写一个空的签名更新PK变量。

: > empty
sbvarsign --key test-key.rsa --cert test-cert.pem \
        --attrs NON_VOLATILE,BOOTSERVICE_ACCESS,RUNTIME_ACCESS \
        --include-attrs --output empty.PK.signed PK empty
sudo dd bs=4k if=empty.PK.signed \
        of=/sys/firmware/efi/vars/PK-8be4df61-93ca-11d2-aa0d-00e098032b8c

secure boot tools resources
The git repository, at [git](git://kernel.ubuntu.com/)

## 使用微软密钥签名引导程序

（1）安装工具包

sudo apt-get install sbsigntool openssl grub-efi-amd64-signed fwts shim-signed linux-signed-generic

（2）生成自签名证书

sudo grub-install –uefi-secure-boot

（3）下载安全启动设置脚本

下载： [sb-setup and keys](http://bazaar.launchpad.net/~ubuntu-bugcontrol/qa-regression-testing/master/view/head:/notes_testing/secure-boot)

需要下载sb-setup文件 和 keys目录

（4）登记密钥

./sb-setup enroll microsoft

（5）重启，核实安全启动

sudo fwts uefidump - | grep Secure
Name: SecureBoot.
  Value: 0x01 (Secure Boot Mode On).
