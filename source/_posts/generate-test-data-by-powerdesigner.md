---
title: 使用powerdesigner反向工程生成数据表的测试数据
date: 2017-05-13 19:33:34
tags: powerdesigner
categories: 工具
---
前几天在搞一个项目，对接接口的时候发现没有测试数据。通过系统新增测试数据固然是比较正规并且有意义的。但是系统部分功能还未实现或者某一个步骤还没开发好，
于是，想在表里面手动制造大量的数据是不可能的，只能够使用工具来。于是，在网上搜了一些资料，发现不是使用程序来生成就是一些不怎么好用的工具。于是就想到了平时用得最多的，用来做表结构设计的powerdesigner。
下面就powerdesigner反向工程，制造测试数据的过程以及过程中遇到的一些问题说明一下，以便后续需要时查阅。
<!-- more -->
环境：windows10(7,8) powerdesigner15.1

#### 一、 配置odbc数据源
    配置odbc也非常简单，一张图就够了。
    
![01333](/img/2017/5/01.png)
1. mysql的odbc连接数据源；如果没有MySQL的连接选项，是因为还没有安装mysql的odbc连接驱动。此时可以去官网下载连接驱动，安装完，重新打开就有了。
[https://dev.mysql.com/downloads/connector/odbc/](https://dev.mysql.com/downloads/connector/odbc/)
2. 如果要操作的是oracle，没有oracle选项，那么可以从oracle官网下载客户端安装就好了。
下载地址：[http://www.oracle.com/technetwork/topics/winx64soft-089540.html](http://www.oracle.com/technetwork/topics/winx64soft-089540.html)
根据需要下载对于的版本。这里oracle的安装有一些需要注意的地方，可以参考：http://www.cnblogs.com/shelvenn/p/3799849.html
![](/img/2017/5/03.png)
安装完成，再点击添加就有了orcale odbc的选项了
![](/img/2017/5/04.png)
进行连接数据并测试，连接成功就可以了
![](/img/2017/5/05.png)
至此，odbc的配置以及完成。接下来就是powerdegsigner的反向工程了
#### 二、powerdesigner的反向工程
选择： 文件/File> Reverse Engineer> Database,选择对于的数据库类型和版本
![](/img/2017/5/0201.png)
连接数据库
![](/img/2017/5/06.png)
>此时test Connection出现错误，而在添加odbc的时候正确，是因为powerdesigner使用的是32位的执行jvm，而本地是使用64位的。所以连接不成功。此时，可以修改powerdesigner的jdk配置。
工具-->常规选项，配置如下图

![](/img/2017/5/0203.png)
注：确保配置的jdk版本是32位。
如果发现没有工具菜单下面没有常规选项，是因为powerdesigner还没有破解，可以先破解，方法可以去百度一下，这里就不在累赘
![](/img/2017/5/0204.png)
如果还是不行，换用32位的odbc，我的是试了所有上述方法之后不行，后来试了这个方法才可以的。是自己刚在安装了64位的odbc数据源了。
![](/img/2017/5/0205.png)

至此，反向工程第一步，也就是已经存在数据库表，反向生成PDM模型的过程已经完成。

接下来就是生成模拟数据了。
#### 三、测试数据的生成
生成数据: 数据库>Generate test data
![](/img/2017/5/0206.png)
测试数据的生成也很简单，这里只是列出了针对在定义字段的测试profiles时出现的一些问题的解决方案
在定义日期时间的范围的时候，发现年份的定义只有三位数，这个是不合理的，也算是powerdesigner的一个bug吧，但是也不是没有解决方案，看一下这下面这两个图，基本就是可以了。
![](/img/2017/5/0301.png)
解决方案
![](/img/2017/5/0302.png)
