---
title: VMVersionMismatchException解决方案
date: 2019-12-11 11:50:57
tags: jvm
---

### 一、执行jmap -heap <pid> 报错

在本机执行jmap -heap <pid> 时，报错，报错信息如下：
```
sun.jvm.hotspot.runtime.VMVersionMismatchException: Supported versions are 25.222-b10. Target VM is 25.161-b12
```

### 二、问题分析

#### 2.1 提示是支持的jvm版本(小版本)是 25.222-b10，目标版本是25.161-b12

#### 2.2 大致可以确定为class文件编译的版本和执行的版本不一致

### 三、检查和解决问题

#### 3.1 检查发现，本地存在两个jdk版本
一个是openjdk，另一个是安装的oracle JDK，大版本均为1.8。所以在简单的使用java和javac时能正常运行。但是想使用bin下其他工具时，由于小版本不一致，可能就无法运行，如jmap可能就有问题了。

#### 3.2 openjdk
openjdk是ubuntu下面默认安装的jdk版本，但是由于openjdk的一些限制(如没有javafx的支持)，所以又安装了Oracle的JDK。

使用update-alternatives工具对本地存在的多个jdk版本进行管理，随时切换。

#### 3.3 检查java -version 和javac -version版本

发现不一致。此时需要配置JAVA_HOME为OracleJDK的安装目录。可以在.bashrc中配置。

#### 3.4 再次使用jmap时，发现还是报错

此时执行whereis jmap，输出如下：

```
hexin@hexin-OptiPlex-3046:~$ whereis jmap
jmap: /usr/bin/jmap /usr/share/man/man1/jmap.1.gz
```

bin下的jmap是一个链接，修改其链接即可。

