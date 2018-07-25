---
title: 在CentOS安装google-chrome
date: 2018-07-25 22:19:39
tags: google-chrome
---

### 一、写这篇文章的原因
最近在写爬虫，需要解析动态页面数据，不能够直接请求接口，接口有经过复杂的加密。解析动态页面需要用到浏览器的支持，有三个浏览器可以使用：
chrome
firefox
PhantomJS
由于firefox解析的时候，部分页面没有会乱，如在获取验证码的位置时获取不到，而验证码的页面是嵌套在iframe下面的。而PhantomJS已经不再支持，官方建议使用chrome和firefox。基于以上情况，我选择使用google的chrome无头浏览器。

### 二、google-chrome无头浏览器
使用参考：https://developers.google.com/web/updates/2017/04/headless-chrome

### 三、google-chrome的安装
由于爬虫应用需要放在CentOS服务器上，因此需要在CentOS安装google-chrome。安装过程参考
配置yum源
1. 在/etc/apt/sources.list加上
deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main
2. 执行
wget https://dl.google.com/linux/linux_signing_key.pub
3. 执行
sudo apt-key add linux_signing_key.pub
4. 执行
    * sudo apt update
    * sudo apt install google-chrome-stable

参考：https://www.linuxbabe.com/ubuntu/install-google-chrome-ubuntu-16-04-lts

#### 四、报错：
```shell
Transaction Summary
===================================================================================================================
Install  1 Package (+37 Dependent packages)

Total size: 71 M
Installed size: 236 M
Is this ok [y/d/N]: y
Downloading packages:
warning: /var/cache/yum/x86_64/7/google-chrome/packages/google-chrome-stable-67.0.3396.99-1.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 7fac5991: NOKEY
Retrieving key from https://dl-ssl.google.com/linux/linux_signing_key.pub


GPG key retrieval failed: [Errno 14] curl#7 - "Failed to connect to 2404:6800:4008:c02::be: Network is unreachable"
[root@reptile v2ray]#
```
通过报错信息，我们知道是无法下载 https://dl-ssl.google.com/linux/linux_signing_key.pub文件，我们的CentOS是没有做翻墙的，所以无法安装。
解决办法
1. 需要先在可以翻墙的机器（我本地开发机就安装了ssr翻墙）下载这个文件，然后再scp拷贝到我们的服务器上。
    > wget https://dl-ssl.google.com/linux/linux_signing_key.pub

2. 拷贝到CentOS后，执行如下命令。
    > rpm --import linux_signing_key.pub
3. 校验是否已经正确import
    > rpm -qi gpg-pubkey-7fac5991-*  

参考：https://blog.csdn.net/knityster/article/details/6312498

到此准备工作已经完成

#### 五、正式安装，执行命令
yum install -y google-chrome-stable

安装完成后，校验是否安装成功：
> google-chrome --no-sandbox --headless --disable-gpu --screenshot https://www.baidu.com

执行完后，在当前目录生成一个png文件。就是baidu首页的截图


### 六、其他：
安装firefox无头浏览器参考：
https://developer.mozilla.org/en-US/Firefox/Headless_mode
