---
title: linux环境：tomcat统一启动脚本
date: 2017-05-18 12:17:15
tags: linux
categories: linux
---

如果linux服务器安装了很多tomcat应用，有时候需要单独重启某一个tomcat的应用，这时候，如果每次操作都需要进入到tomcat的bin目录下面去执行shutdown.sh和startup.sh，这将是非常麻烦的事情。所以就写了一个脚本，以便在shell中任何位置都可以重启指定的tomcat。
<!-- more -->
#### 一、在/usr/scripts/下面脚本文件restartpro.sh内容
```javascript
if [ -z "$1" ]; then
        echo "脚本执行方法： restartpro 程序名"
        exit
fi
source /etc/profile
ps -ef|grep tomcat6-$1|grep -v tomcat6-$1-|awk '{print $2}'|xargs kill -9
cd /data/tomcat6-$1/conf/Catalina
rm -rf localhost
cd /data/tomcat6-$1/work/Catalina
rm -rf localhost logs
cd /data/tomcat6-$1/logs
rm -rf *.log* *.out
/data/tomcat6-$1/bin/startup.sh
tail -f /data/tomcat6-$1/logs/*.out

```
#### 二、在/etc/profile文件最后一行添加
```javascript
alias restartpro='/usr/scripts/restartpro.sh'
#alias restartpro7='/usr/scripts/restartpro7.sh'
```
#### 三、重启tomcat
假设我们有三个tomcat，分别放在/data/下面，名字为
1. tomcat-A
2. tomcat-B
3. tomcat-C
那么我现在需要重启tomcat-B，直接就可以在shell中执行
>$>restartpro B

注：不需要tomcat，可以通过脚本可以看得出。脚本会自动给咱们加上**tomcat-**
