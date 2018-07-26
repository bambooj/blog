---
title: 使用ssh-keygen实现免密登录服务器
date: 2018-07-26 22:19:31
tags:
---

## 使用ssh-keygen实现免密登录服务器

参考：https://www.cnblogs.com/dream-to-pku/p/7049760.html
### 一、目标：免密登陆服务器
有两台服务器，分别为web12和web13，需要实现web12免密码登录web13
```$xslt
192.168.1.12    web12
192.168.1.13    web13
```

### 二、生成公钥私钥对
web12在用户（假设为root）目录下执行命令: 
> ssh-keygen -t rsa,

或者直接执行ssh-keygen命令（默认使用的也是rsa加密类型）。
此时会在当前目录下生成.ssh文件夹，里面有公钥（id_rsa.pub）和秘钥(id_rsa)
### 三、在web13上也用同样的方式生成公钥和秘钥
### 四、在web13上，查看/etc/ssh/sshd_config文件，找到：
>AuthorizedKeysFile      .ssh/authorized_keys

如果没有需要加上。

### 五、提交公钥到服务器
web12上执行：
```$xslt
   cat id_rsa.pub >> authorized_keys					# 把公钥复制到文件authorized_keys中
   chmod 600 authorized_keys						    # 修改文件权限，否则可能会无法登录
   scp authorized_keys root@45.77.43.6:/root/.ssh/		# 将web12上生成的公钥信息拷贝到主服务器上，主服务器配置了sshd_config，认证文件指向了authorized_keys文件。
```

### 六、执行免密登陆
在web12上执行：
> ssh root@45.77.43.6 

验证是否已经实现了免密登录，正常来说是可以了的。