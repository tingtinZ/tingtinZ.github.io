---
layout: post
title: 关于SecureCRT问题的解决
category: SecureCRT
descriptions: SecureCRT问题的解决
keywords: SecureCRT 超时 编码 ssh
---

这里记录我在使用SecureCRT过程中遇到的几个问题及解决。

<!-- more --> 

## 关于SecureCRT超时问题的解决

## 出现的超时提示

```
Connection timed out
```

### 解决方法

+ 尝试方法1：

对Session进行设置，步骤如下：

菜单栏上选择`Options`（选项） ----> `Session Options` （会话选项）---->  在弹出框中选中`Teminal`（终端） ---->  勾选`Send protocol NO OP` 复选框

该设置意为向SSH服务器发送一个消息：会话仍然存在，不应该断开连接。

## 关于SecureCRT编码的解决

### 问题的出现

```
[zhuangtingting@ ~]$ date
2017骞07鏈26鏃鏄熸湡涓11:08:08 CST
```

### 解决

`Options` ---->`Session Options`---->  `Apperance` ----> `Fonts` ----> `Character`

### 效果

```
[zhuangtingting@ ~]$ date
2017年 07月 26日 星期三 11:13:36 CST
```
## 关于SecureCRT用密钥登录的问题

在过程中犯的错误：

+ 服务端配置错误，公私钥问题不清
+ 用户身份错乱，错在root下的.ssh进行配置使得非root身份无法通过公钥登录

解决：

+ 在本地生成一对公私钥

+ 在SrcureCRT上的公钥属性上上传生成的私钥

+ 把公钥放在Linux家目录下的.ssh文件夹下，并使文件名改成authorized_keys

+ 重新启动ssh

  ```
  $ service sshd restart
  ```

