---
title: mysql启动和中文字符乱码
date: 2017-05-05 10:20:11
tags: mysql
---
mysql是使用非常广泛的关系型数据库，在使用过程中经常会遇到一些问题。
本篇记录在mysql启动的过程中遇到的问题，以及mysql数据库中文乱码问题的处理。
<!--more-->
#### 一、启动报错
```js
[root@izwz96tljwj636zfkf3wvqz share]# mysql
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)
[root@izwz96tljwj636zfkf3wvqz share]# mysqld
2017-05-04T12:11:42.751767Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2017-05-04T12:11:42.753370Z 0 [Note] mysqld (mysqld 5.7.18) starting as process 2797 ...
2017-05-04T12:11:42.755044Z 0 [ERROR] Fatal error: Please read "Security" section of the manual to find out how to run mysqld as root!

2017-05-04T12:11:42.755075Z 0 [ERROR] Aborting

2017-05-04T12:11:42.755088Z 0 [Note] Binlog end
2017-05-04T12:11:42.755220Z 0 [Note] mysqld: Shutdown complete

[root@izwz96tljwj636zfkf3wvqz share]# 

```



1. 为了不让警告mysqld --default-file=/etc/my.cnf --user=root出现，
启动mysql时，设置--explicit_defaults_for_timestamp=true
即在/etc/my.cnf在[mysqld]下面添加
```js
explicit_defaults_for_timestamp=true
```
2. 解决Security的问题
在/etc/my.cnf的[mysqld]下面添加
```js
user=root
```
使得启动mysql服务时，强行使用root用户启动

----
#### 二、又一次启动报错了
```js
[root@izwz96tljwj636zfkf3wvqz ~]# service mysqld start
Starting mysqld (via systemctl):  Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.
                                                           [FAILED]

```

处理过程如下
1. 执行>journalctl -ex  #查看错误代码
2. 查看mysql日志文件>vim /var/log/mysql.log
3. 进入日志文件，查看后面的错误代码
4. 发现有错误
```js
2017-05-04T17:56:59.784551Z 0 [ERROR] unknown variable 'default-character-set=utf8'
2017-05-04T17:56:59.784564Z 0 [ERROR] Aborting
```

解决办法  
>**去掉/etc/my.cnf配置文件的default-character-set属性**

#### 三、处理中文字符乱码
```js
mysql> show variables like 'character%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```
此时发现character_set_database和 character_set_server都是latin1的编码

```js
mysql> show create database bangkok;
+----------+-----------------------------------------------------------------------------------+
| Database | Create Database                                                                   |
+----------+-----------------------------------------------------------------------------------+
| bangkok  | CREATE DATABASE `bangkok` /*!40100 DEFAULT CHARACTER SET utf8 COLLATE utf8_bin */ |
+----------+-----------------------------------------------------------------------------------+
1 row in set (0.00 sec)
mysql> set  character_set_database = utf8;
mysql> set character_set_server = utf8;
```
此时，再执行show variables like 'char%';显示的都是utf8；但是重启mysql之后，还是原来未修改的状态。也就是说，刚刚修改的只是当前的session；并没有真正地修改。
此时需要配置my.cnf配置文件
```js
[mysqld]
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_general_ci
```
重启mysql即可。再使用程序插入数据，终于正常了。
