---
title: 使用vps搭建翻墙服务，实现科学上网
date: 2017-05-29 01:45:32
tags: 翻墙
---


用过当前市面上比较流行的翻墙工具，比如lantern、loco加速器。但都比较贵，一个月好几十块钱，有时还不是很稳定，于是就想自己租用一个外地服务器，实现科学上网，这里使用的是vultr服务器搭建翻墙

<!--more-->
#### 一、在https://www.vultr.com/购买服务器

* 注册账号

注册一个vultr账号，登录vultr
* 购买服务器

使用paypal支付
![img/2017/5/vps.jpg](/img/2017/5/vps.jpg)

* paypal账号

如果还没有paypal账号支付，就去注册一个，并关联信用卡。不然无法支付。

* 查看服务器信息，并使用xshell连接

![/img/2017/5/vps02.jpg](/img/2017/5/vps02.jpg)

![/img/2017/5/vps03.jpg](/img/2017/5/vps03.jpg)

#### 二、 安装ssr
ssr，也就是翻墙使用的服务器端的软件。比较常用的是shadowsocks。
```js
wget --no-check-certificate https://www.qcgzxw.cn/SSR/shadowsocksR.sh; bash shadowsocksR.sh
```
安装成功
```js
祝贺！ ShadowsocksR 已经配置成功!
服务器 IP:  45.32.XXX.XXX 
服务器 端口:  8989 
连接密码:  yourpwd123
协议:  auth_sha1_v4 
obfs:  tls1.2_ticket_auth 
加密方式:  chacha20 
多用户配置方式:https://www.qcgzxw.cn/?p=533
如果您想更改连接协议或者加密方式 请前往：
https://www.qcgzxw.cn/?p=533

Enjoy it!
```
注意，安装完成后，默认的加密方式是chacha20.后面连接不了，就是因为这个加密方式的问题，后面需要改成aes-256-cfb.再重新连接就可以了。

#### 三、安装锐速

安装脚本
```js
wget -N --no-check-certificate https://github.com/91yun/serverspeeder/raw/master/serverspeeder-v.sh && bash serverspeeder-v.sh CentOS 7.2 3.10.0-327.el7.x86_64 x64 3.11.20.5 serverspeeder_72327
```

如果安装失败，https://www.91yun.org/serverspeeder91yun 找到合适的版本。不同的系统安装的时候是不同的脚本的。

安装成功提示
```js
===============System Info=======================
CentOS 
3.10.0-514.16.1.el7.x86_64 
x64 
=================================================


installing ServerSpeeder, please wait for a moment...


[Running Status]
ServerSpeeder is NOT running!
version              3.11.20.5

[License Information]
License              607CDDB633476C89 (valid on current device)
MaxSession           unlimited
MaxTcpAccSession     unlimited
MaxBandwidth(kbps)   unlimited
ExpireDate           2034-12-31
[root@vultr ~]# 

```

#### 四、安装shadowsocks连接客户端
已经启动了shadowsocks，但是访问 google.com出现错误
```js
504 Connect to google.com:80 failed: SOCKS protocol error

The following error occurred while trying to access http://google.com/:

504 Connect to google.com:80 failed: SOCKS protocol error

Generated Sun, 28 May 2017 12:19:48 中国标准时间 by Polipo on Lenovo-PC:8123.
```
解决办法：
就是那个连接方式的问题，加密方式由默认的chacha20改成aes-256-cfb。重启shadowsocks，重新连接就可以了。

---


#### 五、 重启shadowsocks服务
重启之前，先使用**ps -ef|grep shadowsocks**查看已经启用的进程，再**kill -9 pid**杀掉进程，重启即可。

```js
python /usr/local/shadowsocks/shadowsocks/server.py -c /etc/shadowsocks.json -d start
```

#### 六、参考地址
1. 注册vulter，租用服务器，类似于国内的阿里云：
https://my.vultr.com/
2. 注册paypal账号并绑定信用卡：https://www.paypal.com/signin
3. 安装shadowsocks教程：https://www.qcgzxw.cn
4. shadowsocks文档和下载：https://github.com/shadowsocks/shadowsocks-windows
5. shadowsocks客户端：https://github.com/shadowsocks/shadowsocks-qt5 。也可以不用这个
6. 锐速安装地址：https://www.91yun.org/serverspeeder91yun

#### 七、特别提醒
安装完ssr后，记得修改加密方式为aes-256-cfb。并重启服务。这个很重要。


