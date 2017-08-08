---
layout: post
title: Linux学习小记3
category: Linux
descriptions: 学习Linux
keywords: Linux
---

今天的Linux学习是进一步的深究，在shell的基础认识上，学习Linux的部分常见任务和工具，并接触shell脚本。

<!-- more --> 

## 网络系统

在网络系统的层面上，我们常用Linux来实现什么功能和任务？

### 检查和监测网络

在实际情况下，我们经常会想要检测网络是否能够连通，是否能够相互访问，还想获得网络中的信息。

这时候，我们可以使用`ping`命令来验证连通。

+ `ping` 发送 ICMP ECHO_REQUEST（网络消息控制协议回显请求）网络数据包到网络主机，通过等待对方主机的回复确定连接是否正常，成功了往往代表网卡、电缆、路由器、网关都处于正常工作状态。

  例如，在我们用`ping` 命令检查是否能连接到网站时，可以发现它并不会自动退出，而且还会在特定的时间间隔内发送数据包，直到被中断，这时可以用`ctrl+c`来中断。	 中断后，会打印出统计信息：

  ```
  --- taobao.com ping statistics ---
  300 packets transmitted, 299 received, 0% packet loss, time 299540ms
  rtt min/avg/max/mdev = 27.422/30.696/47.221/3.650 ms
  ```

  我们还可以利用选项来指定`ping` 发送数据包的次数：

  ```
  $ ping -c 10 taobao.com
  ```

我们还能检测到其他的网络信息。

+ `traceroute` 这个命令常被用来追踪网络数据从本地计算机到远端计算机的所走的路径，获取所经过的网络设备的域名、IP地址和性能数据。
+ `netstat` 检查各种各样的网络设置和统计数据，包括网络连接，路由表，接口统计数据，伪装连接，和多路广播成员。
  + `netstat -ie`查看系统中的网络接口
  + `netstat -r`显示路由表

### 网络中传输文件

有网络知识基础的都会了解到FTP文件传输协议实现网络上的文件传输，Linux在网络中的文件传输也跟FTP很大的关系。

+ `ftp` 因特网文件传输程序。
  + `ftp fileserver` 连接FTP服务器（使用IP地址或主机名也能建立FTP链接）
  + `get `文件下载
  + `put` 文件上传
  + `bye` 结束 ftp 程序会话（也可以使用命令 quit 和 exit）
+ `lftp` 这个程序与`ftp`相似，比`ftp` 更便捷更优越。
+ `wget` 不仅从FTP也能从网络上下载文件，还能下载多个文件。

### 与远程主机安全通信

FTP其实质上是不安全的，以明码形式来传输所有的交流信息（包括登录命令和密码），容易受到中间人攻击方式的攻击（冒充），而SSH专为远程登录会话和其他网络服务提供安全性的协议，加密通讯信息，有效防止信息泄露。

+ `ssh` 远端登录程序
+ `scp` 安全复制
+ `sftp` 与`ftp` 程序相似，但是它不用明码形式传递数据，而是使用加密的SSH通道。

---

## 管理存储设备

在LInux学习小记2中提到了一下Linux只有一个单一的文件系统树，而各个存储设备是挂载到树的各个节点上。那么对于挂载和卸载存储设备的操作是如何的呢？

### 挂载和卸载存储设备

+ `mount` 挂载文件系统。后面不接参数时，会显示当前挂载的文件系统的信息。
  + `mount -t` 指定文件系统类型
+ `umount` 卸载存储设备。

在使用存储设备挂载文件系统的时候，我们可以利用一些小技巧来确定动态的设备名：

```
$ sudo tail -f /var/log/messages
```

选项“-f”表示监视File文件增长，显示文件最新追加的内容。`/var/log/messages`记录了系统活动日志。

这条命令使得我们对系统新的一举一动能进行监测。随后插入可移动设备就能被探测到，我们就可以确定设备名了。注意，设备与计算机重新连接的话，设备名称可能会变哦～

## 归档和备份

在处理大量文件的时候，我们有必要对文件集合进行管理，可以用到以下几种压缩文件的方法：

+ `gzip` 压缩一个或多个文件，相应地用`gunzip` 还原成没有压缩的版本
+ `bzip2` 块排序文件压缩器，相应地用`bunzip` 还原成没有压缩的版本

### 归档文件

归档常常与压缩一起使用。

+ `tar` 归档文件，创建tar包
  + `tar cf` 模式c和选项f，指定tar包的名字
+ `zip` 打包和压缩文件

### 同步本件和目录

+ `rsync` 同步远端文件和目录

### tar，gzip，bzip2比较

+ `tar` 

  只打包，不压缩，生成一个tar包。

+ `gzip` 和`bzip2` 

  都是压缩程序，可以和tar结合使用：

  ```
  $ find playground -name 'file-A' | tar czf playground.tgz -T -  //创建gzip压缩的归档文件
  $ find playground -name 'file-A' | tar cjf playground.tbz -T -  //创建bzip2压缩的归档文件
  ```

  上面的命令解析：

  “-”来表示标准输入/输出，“-T”选项使tar命令从一个文件读取路径名，gzip和bzip2各自使用z和j选项。

  + `gzip` 压缩速度最快
  + `bzip2` 使用了不同的压缩算法，舍弃了压缩速度，但压缩率更高

---

## Shell脚本

shell脚本包含了一系列命令，执行一个shell脚本即执行文件中所有的命令。

### 编写shell脚本

首先，要清楚编写脚本需要注意的地方。shell脚本是文本文件，需要一个文本编辑器来编写，在这里我使用的是vi编辑器。

```
$ vi hello_world

#!/bin/bash
# This is our first script.
echo 'Hello World!'
~                                                                                   
"hello_world" [New] 5L, 62C written
```

### 使脚本可执行

接下来用`chmod`命令修改文件权限使得可执行。

```
$ ls -l hello_world
-rw-rw-r-- 1 zhuangtingting zhuangtingting 62 7月  27 15:32 hello_world
$ chmod 755 hello_world
$ ls -l hello_world    
-rwxr-xr-x 1 zhuangtingting zhuangtingting 62 7月  27 15:32 hello_world
```

### 脚本文件位置

在我们指定文件的明确路径的时候，我们可以直接执行我们的脚本：

```
$ ./hello_world
Hello World!
```

但是如果不指定明确路径，那很可能会出现报错。为了保证脚本可执行，我们把脚本放置到shell能够找到的地方。

创建一个bin目录，并把脚本放在该目录下，那么脚本就可以轻松执行了。

## vi

在上面我使用了vi编辑器，这里再对vi进行更深入一点的学习。大多数 Linux 发行版不包含真正的 ，而是自带一款高级替代版本， vim。以下是使用vi时所要了解的操作：

+ 启动vi

  直接输入命令`vi` 即可，后面可以接上文件名， 若文件不存在，则自动生成文件。

+ 插入模式

  vi是一个模式编辑器，vi启动后会直接进入命令模式，按下”i”键进入插入模式即可编辑。

+ 命令模式

  编辑完成后，按下Esc键进入命令模式，命令的键入方法为，先输入“:”，再输入命令字符。

  + `:w`保存
  + `:q`退出
  + `:q!` 强制退出

  此外，如果想对正在编辑的内容进行简便光标移动，可以先由插入模式转成命令模式，再按下vi提供的光标移动按键来进行快速的光标移动，非常有助于我们编辑文本。这里列出几个比较好用的按键：

  + `0` 移动到当前行的行首
  + `$` 移动到当前行的末尾。
  + `B` 移动到上一个单词的开头，忽略标点符号
  + `Ctrl-f or Page Down/UP`向下/上翻一页
  + `G` 移动到文件末尾

## 学习中遇到的问题

1. 问题1：`traceroute` 命令只打印所经过的路由器信息吗？其他网络设备？书P314

2. 问题2：ssh中私钥和公钥？

   搜索解答：ssh有两种登录方式，口令（密码）认证方式和密钥认证方式，在后者认证方式中，实现的登录需要进行以下步骤：

   + 生成密钥，包括公钥和私钥
   + 把公钥上传到ssh服务器，添加在指定文件中，完成ssh服务端配置
   + 客户端通过私钥登录ssh服务器

3. 问题3：将脚本放置bin下的时候，还是不能够执行脚本：

   ```
   $ ls -l bin/hello_world
   -rwxr-xr-x 1 zhuangtingting zhuangtingting 60 7月  27 15:51 bin/hello_world
   ```

   探索：在保证每一个步骤都按部就班以后，开始考虑路径的问题，查看一下PATH环境变量的内容：

   ```
   $ echo $PATH
   /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
   ```

   可以看到以上的目录列表中，并没有出现/home/zhuangtingting/bin目录，虽然大多Linux发行版会配置PATH变量，包含一个用户家目录下的bin目录，但是我这里并没有配置，所以依然不能够直接执行。

   解决：将该目录添加到PATH环境变量：

   ```
   # export PATH=$PATH:/home/zhuangtingting/bin
   # echo $PATH
   /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/home/zhuangtingting/bin
   ```

   再次执行脚本：

   ```
   [zhuangtingting@ ~]$ hello_world
   Hello World
   ```

   执行成功！
   #### 但是！！！
   Jeff说我们一般不去设置PATH变量，这种执行脚本的方法是错误的。
   看了鸟哥私房菜，发现鸟哥也只是在脚本里面将环境变量添加进去，但是在脚本的有效范围之余，环境变量并没有改变。
4. 问题4：为什么脚本开头要加上“#!bin/bash”？

   搜索解答：“#!bin/bash”表示用系统中bin目录下的bash程序来执行。

### 待了解

分区

## 来自Jeff的学习建议

多看书!

怎么选购图书？

1. 不选“编著“
2. 认准出版社：人民邮电出版社，机械工业出版社，电子工业出版社
3. 丛书系列（推荐O'Reilly）
4. 封面
5. 豆瓣点评
6. 一般没有腰封

