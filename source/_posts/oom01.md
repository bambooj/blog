---
title: tomcat内存溢出PerGen space
date: 2017-05-18 23:44:11
tags: JVM
comments: true
---
#### 一、应用在tomcat运行一段时间后，出现内存溢出报错。如永久区内存溢出PermGen space;

<!-- more -->

```javascript
2017-5-18 14:56:01 org.apache.coyote.http11.Http11Protocol$Http11ConnectionHandler process
严重: Error reading request, ignored
java.lang.OutOfMemoryError: PermGen space
	at java.lang.Throwable.getStackTraceElement(Native Method)
	at java.lang.Throwable.getOurStackTrace(Throwable.java:591)
	at java.lang.Throwable.printStackTrace(Throwable.java:510)
	at java.util.logging.SimpleFormatter.format(SimpleFormatter.java:72)
	at org.apache.juli.FileHandler.publish(FileHandler.java:198)
	at java.util.logging.Logger.log(Logger.java:478)
	at java.util.logging.Logger.doLog(Logger.java:500)
	at java.util.logging.Logger.logp(Logger.java:700)
	at org.apache.juli.logging.DirectJDKLog.log(DirectJDKLog.java:167)
	at org.apache.juli.logging.DirectJDKLog.error(DirectJDKLog.java:135)
	at org.apache.coyote.http11.Http11Processor.process(Http11Processor.java:875)
	at org.apache.coyote.http11.Http11Protocol$Http11ConnectionHandler.process(Http11Protocol.java:620)
	at org.apache.tomcat.util.net.JIoEndpoint$Worker.run(JIoEndpoint.java:489)
	at java.lang.Thread.run(Thread.java:662)
```

#### 二、原因分析：
1. 查看应用运行分配的内存情况
```javascript
[testapp@zhmgr-002 microactivitymgr]$ jps -lv|grep activity
13046 org.apache.catalina.startup.Bootstrap -Xms512m -Xmx1024m -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -Djava.util.logging.config.file=/data/tomcat6-microactivitymgr/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.endorsed.dirs=/data/tomcat6-microactivitymgr/endorsed -Dcatalina.base=/data/tomcat6-microactivitymgr -Dcatalina.home=/data/tomcat6-microactivitymgr -Djava.io.tmpdir=/data/tomcat6-microactivitymgr/temp
```
发现只对堆内存设置了内存-Xms512m -Xmx1024m，永久区并没有设置（XX参数）。

2. 查看该应用的内存使用情况
```javascript
$>jstat -gcutil 13046
  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT   
  0.00   0.00   0.00   5.83 100.00     54    0.264  2174  515.817  516.081
```
#### 三、 解决办法：加大永久区的内存
配置方法：
在之前的加大堆方法的后面加上：
> -XX:MaxNewSize=256m -XX:MaxPermSize=256m

重启即可