---
layout: post
title: 使用securecrt连接虚拟机内的 Ubuntu 系统
category: securecrt
descriptions: 与虚拟机内的Ubuntu系统用ip和端口相连接，实现在宿主机上的终端仿真。
keywords: securecrt, ssh
---

# 使用securecrt 连接虚拟机内的 Ubuntu 系统
## 关于securecrt
SecureCRT是一款支持SSH（SSH1和SSH2）的终端仿真程序，简单地说是Windows下登录UNIX或Linux服务器主机的软件。
## 配置
1. 设置虚拟机的网络模式，NAT或桥接。
2. 在Ubuntu上用`ifconfig`命令查看IP地址信息。
```
[zhuangtingting@ ~]$ ifconfig
enp0s3    Link encap:Ethernet  HWaddr 08:00:27:e9:50:03  
          inet addr:10.0.0.176  Bcast:10.0.0.255  Mask:255.255.255.0
          inet6 addr: fe80::378a:3320:64c7:7952/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:405 errors:0 dropped:0 overruns:0 frame:0
          TX packets:397 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:143029 (143.0 KB)  TX bytes:73878 (73.8 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:157 errors:0 dropped:0 overruns:0 frame:0
          TX packets:157 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:15404 (15.4 KB)  TX bytes:15404 (15.4 KB)
```
这里我的IP地址是10.0.0.176

## 安装SSH
1. 在Ubuntu上使用`ssh username@localhost`命令检查是否已经安装SSH服务。
2. 若无，则安装SSH
```
$ sudo apt-get install openssh-server
```
3. 查看是否有进程在22端口上监听，即是否已启动：
```
[zhuangtingting@ ~]$ netstat -nat | grep 22
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN 
```
## SecureCRT连接Ubuntu
1. 打开SecureCRT软件，进行配置。ProtoCol模式选为SSH2，Port为22，HostName为192.168.172.168（就是我们Ubuntu的IP地址），UserName为我们Ubuntu的登录账户。
2. 点击“Connect”后，PassWord为我们Ubuntu的登录密码。
3. 现在就可以尝试输入linux命令检验成果。
## 遇到的问题
如此配置之后，发现在宿主机上浏览器访问不了页面了。
> [SecureCRT连接Ubuntu参考教程](http://www.linuxidc.com/Linux/2015-05/117048.htm)