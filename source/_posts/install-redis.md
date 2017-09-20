---
title: redis安装
date: 2017-05-18 12:35:21
tags: redis
categories: 数据存储
---

按照官网的安装流程就行安装
<!-- more -->
#### 一、执行make命令,报错
```javascript
[root@nq-sz-kf001-01 src]# make
    CC adlist.o
In file included from adlist.c:34:
zmalloc.h:50:31: error: jemalloc/jemalloc.h: No such file or directory
zmalloc.h:55:2: error: #error "Newer version of jemalloc required"
make: *** [adlist.o] Error 1
[root@nq-sz-kf001-01 src]# 
```


#### 二、 报错处理：没有gcc
安装过程
```javascript
    yum install cpp
    yum install binutils
    yum install glibc
    yum install glibc-kernheaders
    yum install glibc-common
    yum install glibc-devel
    yum install gcc
    yum install make
```
   
>注意gcc依赖了很多东西，有些包可能系统已经 装了，有些没有，防止出意外，最好都走一遍

############解决方法#############
make MALLOC=libc


#### 三、window下安装
1、下载地址https://github.com/dmajkic/redis/downloads
2、打开一个cmd窗口，使用cd命令切换到指定目录（D:\redis\64bit）运行 redis-server.exe redis.conf 。运行以后出现如下界面

#### 四、测试Redis服务端是否已经安装成功
1. cmd到**D:\redis\64bit**,运行 
>redis-cli.exe -h 127.0.0.1 -p 6379

其中 127.0.0.1是本地ip，6379是redis服务端的默认端口
2. 测试
```javascript
set test "11111111111111111"
get test
```