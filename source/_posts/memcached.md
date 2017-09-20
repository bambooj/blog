---
title: memcached的安装和使用（单点）
date: 2017-07-22 23:18:12
tags:
---

Linux安装memcached
#### 一、Memcached的安装：使用命令安装
1. 使用命令安装
安装时，会自动安装memcached的依赖libevent。libevent是memcached的通信模块。
```
yum install memcached
```

2. 启动memcached服务
安装完成后，可以使用**find / -name memcached**命令查询memcached存放的目录。
```js
[root@izwz96tljwj636zfkf3wvqz ~]# find / -name memcached
/etc/selinux/targeted/active/modules/100/memcached
/etc/sysconfig/memcached
/usr/bin/memcached
```
memcached命令放在/usr/bin/目录下面，cd到/usr/bin目录，执行启动memcached服务命令
```js
memcached -d -m 512 -u root  -p 11211 -c 10000  -M -f 1.1 -P /tmp/memcached.pid
```
启动成功后，就可以在window上面
telnet ip 11211测试是否可以使用了。


启动memcached
```js
[root@localhost ~]# memcached -d -m 10 -u root -l 192.168.1.75 -p 11212 -c 256 -P /tmp/memcached.pid
[root@localhost ~]# ps -ef|grep memcached

memcached -d -m 10 -u root -l 39.108.10.27 -p 11212 -c 256 -P /tmp/memcached.pid
```
#### 二、memcached的使用
1. 配置连接信息
在属性文件sys.properties中配置memcached的连接信息;如果是集群方式，这配置多个服务连接信息
```js
#memcached
# the pool size(the number of client)
memcached.connectionPoolSize=5
# in this mode, when a node out, it will throws MemcachedException when call this node
memcached.failureMode=true
#server1
memcached.server1.host=39.108.10.27
memcached.server1.port=11211
memcached.server1.weight=1
#server2
#memcached.server2.host=192.168.88.141
#memcached.server2.port=11211
#memcached.server2.weight=1
#server3
#memcached.server3.host=192.168.88.142
#memcached.server3.port=11211
#memcached.server3.weight=1
```
2. 在config-cache.xml的spring配置文件中配置memcached客户端构建器
```xml
<!--memcached配置开始-->
    <bean id="memcachedClientBuilder" class="net.rubyeye.xmemcached.XMemcachedClientBuilder">
        <!-- XMemcachedClientBuilder have two arguments.First is server list,and
            second is weights array. -->
        <property name="connectionPoolSize" value="${memcached.connectionPoolSize}"/>
        <property name="failureMode" value="${memcached.failureMode}"/>
        <constructor-arg>
            <list>
                <bean class="java.net.InetSocketAddress">
                    <constructor-arg>
                        <value>${memcached.server1.host}</value>
                    </constructor-arg>
                    <constructor-arg>
                        <value>${memcached.server1.port}</value>
                    </constructor-arg>
                </bean>
                <!--
                <bean class="java.net.InetSocketAddress">
                    <constructor-arg>
                        <value>${memcached.server2.host}</value>
                    </constructor-arg>
                    <constructor-arg>
                        <value>${memcached.server2.port}</value>
                    </constructor-arg>
                </bean>
                <bean class="java.net.InetSocketAddress">
                    <constructor-arg>
                        <value>${memcached.server3.host}</value>
                    </constructor-arg>
                    <constructor-arg>
                        <value>${memcached.server3.port}</value>
                    </constructor-arg>
                </bean>
                -->
            </list>
        </constructor-arg>
        <constructor-arg>
            <list>
                <value>${memcached.server1.weight}</value>
                <!--<value>${memcached.server2.weight}</value>-->
                <!--<value>${memcached.server3.weight}</value>-->
            </list>
        </constructor-arg>
        <!-- BinaryCommandFactory -->
        <property name="commandFactory">
            <bean class="net.rubyeye.xmemcached.command.BinaryCommandFactory" />
        </property>
        <property name="transcoder">
            <bean class="net.rubyeye.xmemcached.transcoders.SerializingTranscoder" />
        </property>
        <property name="bufferAllocator">
            <bean class="net.rubyeye.xmemcached.buffer.SimpleBufferAllocator"></bean>
        </property>
    </bean>
    <!-- Use factory bean to build memcached client -->
    <bean id="memcachedClient" factory-bean="memcachedClientBuilder"
          factory-method="build" destroy-method="shutdown" />
    <!--memcached配置结束-->
```
3. 在java代码中使用
```js
//注入客户端
@Resource
private MemcachedClient memcachedClient;
 
//在方法中使用(对缓存执行增删改查)
 memcachedClient.delete(DOMAIN_TREE_ROOT_KEY);
 ```