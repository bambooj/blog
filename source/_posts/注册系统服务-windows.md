---
title: 注册系统服务(windows)
date: 2017-04-18 15:24:45
tags: service
categories: 配置
---
#### 一、Tomcat变成自动启动的服务
---
1. 在环境变量中设置JAVA_HOME
(假设JDK目录为d:\jdk1.4.2_04)
    A、进入windows桌面，右键选择“我的电脑”-->“属性”
    B、选择“高级”页签，点开“环境变量”
    C、在“系统变量”下点击新建弹出“新建系统变量”，变量名输入“JAVA_HOME”，变量值输入“d:\jdk1.4.2_04”后点击确定
2. 将Tomcat设置成服务
(假设Tomcat目录为d:\Tomcat_oa)
    A、点击开始-->运行，输入cmd进入dos控制台
    B、执行以下dos命令(输入命令后按回车键执行)
        d:
        cd Tomcat_oa\bin
        service install 服务名(可选，默认为tomcat5)
    此时Tomcat服务已经成功安装。
3. 进入系统服务将服务启动，并将服务设置成自动启动
4. 服务移除
    基本操作同2，最后执行service remove 服务名
特殊问题处理
    如果服务启动失败，检查windows的system目录下(如：c:\windows\system)是否有msvcr71.dll
    如果没有的话将jdk\bin目录下的msvcr71.dll拷贝至windows的system.

#### 二、mysql注册成系统服务
---
1. 进入mysql的安装目录下的bin目录，执行：mysqld --install 服务名(可选)
2. 如果出现“install/remove of the service denied”错误，则重新用管理员身份进入cmd
删除服务：
sc delete 服务名