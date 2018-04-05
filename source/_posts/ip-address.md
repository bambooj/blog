---
title: ip地址分类
date: 2017-04-24 22:33:17
tags: 网络
---

IP地址分类可以分为三类：A类、B类和C类。
<!--more-->
#### 1. A类IP地址 

一个A类IP地址由1字节的网络地址和3字节主机地址组成，网络地址的最高位必须是“0”， 地址范围从1.0.0.0 到126.0.0.0。可用的A类网络有126个，每个网络能容纳1亿多个主机。 需要注意的是网络号不能为127，这是因为该网络号被保留用作回路及诊断功能。 

#### 2. B类IP地址 

一个B类IP地址由2个字节的网络地址和2个字节的主机地址组成，网络地址的最高位必须是“10”，地址范围从128.0.0.0到191.255.255.255。可用的B类网络有16382个，每个网络能容纳6万多个主机。 

#### 3. C类IP地址 

一个C类IP地址由3字节的网络地址和1字节的主机地址组成，网络地址的最高位必须是“110”。范围从192.0.0.0到223.255.255.255。C类网络可达209万余个，每个网络能容纳254个主机。

什么叫网络地址的最高位
B类地址是用32 位地址中的最高位模式来识别的，如：
10XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX（32位），最高位的“10”代表它是B类的。B 类地址的前16 位代表网络号，剩余的16位可由管理网络地址的用户来修改。

#### 4. 局域网ip段

私有IP地址范围： 
  A类：10.0.0.0-10.255.255.255
  B类：172.16.0.0-172.31.255.255 
  C类：192.168.0.0-192.168.255.255