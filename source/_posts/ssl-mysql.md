---
title: 使用DataGrip通过ssl方式连接MySQL数据库
date: 2017-05-18 10:54:00
tags: 工具
---

以前一直使用navicat连接mysql，虽然也特别好用，功能强大，但是这个工具经常卡死无法响应。后面在使用Intellij IDEA的过程中，在它官网就发现了DataGrip，发现这个工具比较好用，自动提示也非常好。下面就简单介绍一下这个工具如何通过ssl方式连接MySQL，当然navicat也是可以使用ssl连接的。
<!-- more -->
#### 一、 ssl连接准备
平时我们直接连接使用工具连接mysql的时候，直接输入IP、端口、用户名和密码登录。但是使用ssl登录的时候，需要额外得做个简单配置，准备文件有：
1. client-cert.pem
2. client-no-password-key.pem
3. mysql-ca-cert.pem

#### 二、 连接mysql
1. 正常连接，输入IP、端口、用户名和密码
![](/img/2017/5/mysql01.jpg)
2. 使用ssl连接
![](/img/2017/5/mysql02.jpg)

#### 三、总结，
1. 需要准备ssl连接文件
2. ssl连接文件路径不能有中文字符