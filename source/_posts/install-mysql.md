---
title: mysql安装
date: 2017-04-27 09:47:02
tags: mysql
---

在linux上面安装和在windows上的安装过程还是有一点区别的，这边文章记录了我个人在linux平台上面安装mysql过程中遇到的一些问题和解决方案。
<!--more-->

#### 1. 下载安装包，mysql57-community-release-el6-9.noarch.rpm
如果下载的安装包放在了window上，需要将安装包上传至linux服务器，可以使用xftp工具从windows put 文件到linux。
当然，最简单的还是在linux命令行上面直接> wget mysql57-community-release-el6-9.noarch.rpm；
#### 2. 开始安装 [(参考官网)](https://dev.mysql.com/doc/refman/8.0/en/linux-installation-yum-repo.html#yum-repo-installing-mysql)

安装仓库 
> sudo yum localinstall platform-and-version-specific-package-name.rpm 

查看
>yum list | grep mysql

安装mysql
>yum -y install mysql-community-server

![clipboard1](/img/2017/4/clipboard1.png)

这个时候可能出现截图中的错误，是因为包的兼容性问题，可以先删除mysql依赖
删除依赖
>yum remove mysql-libs

安装完成

如果想安装旧版本，如5.5版本，则可以通过yum list mariadb或者yum list mysql 查看有哪些版本可供安装
可以直接执行命令
yum install mysql
或者
yum -y install mariadb mariadb-server
此时安装的是mysql的一个分支mariadb。mariadb是mysql的一个分支，mysql先后被sun和oracle收购，以防oracle会对mysql做一些认证。


#### 3. 启动mysql服务
>service mysql start

#### 4. mysql安装启动完成后，自动生成的root密码
> vim /var/log/mysqld.log

![clipboard1](/img/2017/4/clipboard2.png)
或者执行
>sudo grep 'temporary password' /var/log/mysqld.log

#### 5. 重置密码
5.1 先使用初始密码登录
```javascript
 网上可能是建议你这样修改的
>mysql -uroot -p
 密码
>mysql
>use mysql;
>update user set password=passworD("123456") where user='root';
执行update时，报错：ERROR 1046 (3D000): No database selected
```
5.2 使用官网做法
> ALTER USER 'root'@'localhost' IDENTIFIED BY 'Gz@2017(Kj)';
​>flush privileges;

5.3 直接在shell中执行mysql_secure_installation
```javascript
[root@vultr usr]# mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE! PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them. This is intended only for testing, and to make the installation
go a bit smoother. You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] Y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'. This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n
 ... skipping.

By default, MariaDB comes with a database named 'test' that anyone can
access. This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] n
 ... skipping.

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] Y
 ... Success!

Cleaning up...

All done! If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
[root@vultr usr]#
```

#### 6. 使用可视化工具连接

![clipboard3](/img/2017/4/clipboard3.png)
发现此时还是不能够登录
在window上面telnet ip port一下
![clipboard4](/img/2017/4/clipboard4.png)

发现无法telnet到端口
因此可以判断是端口没有开放出来。
#### 7. 开放端口号
查看端口状态
>service status iptables;

如果是centos7版本，则需要使用systemctl，已经不再支持service命令了。此时命令行会有提示。
如果还没有安装防火墙，可以先安装
> yum install iptables-services

安装完成后，在查看
>systemctl status iptables.service

![clipboard5](/img/2017/4/clipboard5.png)
说明已经关闭防火墙了
#### 8. 客户端连接
此时如果使用navicat等客户端连接还报错，那么授权一下就可以了。
```javascript
mysql> grant all privileges on *.* to root@"%" identified by "yourpassword";
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```
其实执行**grant all privileges on *.* to root@"%" identified by "yourpassword";** 就相当与执行
**insert into mysql.user(Host,User,Password) values("%","root",password("yourpassword"));** 也就是往mysql.user插入一条数据。
可以通过 **select Host ,User , Password from user;** 查看效果。

#### 9. 总结
总之，安装的过程最好还是按着官网的来。安装过程也因各个linux版本不同而不同。

#### 10. 补充部分
上述步骤都完成之后，我在windows用navicat远程连接root，创建一个用户user1，然后给他授权一个刚创建的数据库db1.但是提交的时候失败了。
接着，又使用user1试着去连接，还是报错，连接不上；登录服务器使用mysql -uuser1 -p user1password又可以正常连接。
于是查了资料，发现在授权的时候需要加一个with grant option；也就是
>grant all privileges on db1.* to user1@'%' identified by 'user1password' *with grant option*;

with grant option 的 作用可以参考官网文档；这里简单理解为使用了这个参数之后，就可以就行远程连接了。

