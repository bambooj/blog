---
title: linux环境：内存溢出
date: 2017-05-18 12:20:17
tags: linux tomcat
categories: linux
---
有时候在linux环境下tomcat内存不够，需要手动增加内存的配置

>vim ./catalina.sh

大概在375行
![](/img/2017/5/tomcat01.jpg)
只需要新增添加的部分
```javascript
 -Xms1024m -Xmx1024m -XX:+UseConcMarkSweepGC -XX:+UseParNewGC
```


