---
title: servlet的监听器、过滤器和拦截器
date: 2017-04-18 20:36:36
tags: servlet
---

### servlet的监听器、过滤器和拦截器

[filter和listener](http://my.oschina.net/chape/blog/158344)


#### 一、监听器
servlet2.4中定义了三个接口：
ServletContextListener,HttpSessionListener,ServletRequestListener。分别实现对应的接口就可以实现对应的监听处理

<!--more-->

#### 二、监听器类型

按监听的对象划分：servlet2.4规范定义的事件有三种：
1. 用于监听应用程序环境对象（ServletContext）的事件监听器
2. 用于监听用户会话对象（HttpSession）的事件监听器
3. 用于监听请求消息对象（ServletRequest）的事件监听器
 
按监听的事件类项划分
1. 用于监听域对象自身的创建和销毁的事件监听器
2. 用于监听域对象中的属性的增加和删除的事件监听器
3. 用于监听绑定到HttpSession域中的某个对象的状态的事件监听器
 
在一个web应用程序的整个运行周期内，web容器会创建和销毁三个重要的对象，ServletContext，HttpSession,ServletRequest。
#### 三、分类及介绍
1. ServletContextListener：用于监听WEB 应用启动和销毁的事件，监听器类需要实现javax.servlet.ServletContextListener 接口。
2. ServletContextAttributeListener：用于监听WEB应用属性改变的事件，包括：增加属性、删除属性、修改属性，监听器类需要实现javax.servlet.ServletContextAttributeListener接口。
3. HttpSessionListener： 用于监听Session对象的创建和销毁，监听器类需要实现javax.servlet.http.HttpSessionListener接口或者 javax.servlet.http.HttpSessionActivationListener接口，或者两个都实现。
4. HttpSessionActivationListener： 用于监听Session对象的钝化/活化事件，监听器类需要实现javax.servlet.http.HttpSessionListener接口或者 javax.servlet.http.HttpSessionActivationListener接口，或者两个都实现。
5. HttpSessionAttributeListener：用于监听Session对象属性的改变事件，监听器类需要实现javax.servlet.http.HttpSessionAttributeListener接口。

#### 四、拦截器与过滤器的区别
1. 拦截器是基于java的反射机制的，而过滤器是基于函数回调。
2. 拦截器不依赖与servlet容器，过滤器依赖与servlet容器。
3. 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
4. 拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。
5. 在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次

执行顺序：过滤前 – 拦截前 – Action处理 – 拦截后 – 过滤后。个人认为过滤是一个横向的过程，首先把客户端提交的内容进行过滤(例如未登录用户不能访问内部页面的处理)；过滤通过后，拦截器将检查用户提交数据的验证，做一些前期的数据处理，接着把处理后的数据发给对应的Action；Action处理完成返回后，拦截器还可以做其他过程(还没想到要做啥)，再向上返回到过滤器的后续操作。
