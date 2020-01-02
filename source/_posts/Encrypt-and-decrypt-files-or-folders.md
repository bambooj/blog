---
title: Encrypt and decrypt files or folders
date: 2020-01-02 12:23:54
tags:
---

#### 一、对文件或文件夹加密解密
##### 1.1 使用zip对文件和文件夹进行加密

for file: zip -e target.zip source.txt

for dir : zip -re target.zip source

##### 1.2 使用gpg

只能对文件加密

加密： 
> gpg -c source.txt       # 生成文件 gpg source.txt.pgp

解密： 
> gpg source.txt.pgp      # 生成文件 source.txt

-c 使用对称加密

可以使用非对称个加密

##### 1.3 使用encfs对文件夹加密

安装encfs
> sudo apt-get install encfs

加密visible文件夹
> encfs ~/.encrypted ~/visible

把需要加密的文件放入visible文件夹

隐藏文件
> fusermount -u ~/visible 

重新查看文件

> encfs ~/.encrypted ~/visible

这时候需要输入之前设置的密码

参考：https://help.ubuntu.com/community/FolderEncryption
