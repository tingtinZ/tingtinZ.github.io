---
layout: post
title: Linux学习小记6
category: Linux
descriptions: Linux学习
keywords: Linux
---

学习《实战LINUX_SHELL编程与服务器管理》的第二天。

<!-- more --> 

### 命令

1. `-`经常用在管道指令中，代表标准输入，用来不足某些指令需要的参数。

2. `find . -name  '*.txt' -exec rm -f {} \;` 该命令的意思是：

   由现在的目录(.)开始往下寻找扩展名是.txt的文件，找到之后，执行(-exec)删除文件的指令。-f代表强制删除，{}代表找到的文件，;是执行指令(-exec)的终止符号，由于它是Bash Shell的特殊字符，所以要用\转义掉特殊的含义。

3. `tar` 命令的x选项表示解包。

4. `sort -nk 2 dataf` 以dataf中的第2个字段的数值大小做排序的依据。

5. `uniq` 删除重复行，-d挑出重复行，-c计算每一行的重复次数。

6. `cut -d: -f1 /etc/passwd` 抽出/etc/passwd的第一个字段。

7. `(命令1;命令2;命令3;)`和 `{  命令1;命令2;命令3; }`区别：

   + 前者开启一个子Shell环境
   + 后者在现行的Shell中执行，而非在子Shell中执行。需要特别注意，在{右边和}左边要有空格。

8. `Esc+@` 补全主机名

9. `Esc+~` 补全账号

10. `Esc+$`补全变量 

---

### 记录命令的执行过程

有时候，需要把执行命令所产生的信息记录起来，那么我们可以用到日志文件。

`script [日志文件]` 自定义日志文件，若不进行这一步，默认把数据放在typescript这个文件里。直到执行exit，离开script。

---

### 快捷键

1. `ctrl+k`和`ctrl+u`向右/左删除一行
2. `ctrl+w`删除单词
3. `Esc+b` 和`Esc+f` 向左/右移动一个单词
4. `ctrl+r` 搜索历史命令

---

### 变量

1. Bash Shell把任何存储在变量中的值，都视为以字符组成的“字符串”。

2. `shopt -s -o nounset` 意思是变量一定要先经过设定才能使用。

3. `unset -v` 取消变量，`unset -f`取消函数。

4. 取消变量VS清空变量：取消变量把内容删除，还让变量不存在。清空变量只是把变量的内容删除，但是变量仍然存在。想要清空变量，重新设一个空值就可以。

5. 单引号VS双引号

   + 单引号：写什么显示什么。
   + 双引号：可进行替换变量、命令执行结果、算数运算结果。

6. `readonly`定义只读变量，也可以用`declare -r`

   + ``readonly -f` 设定函数只读
   + `readonly -a` 设定数组变量可读

7. bash仅支持一维的数组。

8. Here Document：

   ```
   命令 <<标记
   ......
   ......
   ......
   标记
   ```

   能用来设定变量，做多行批注，打包程序原始码。

---

### 高级变量

1. `${待测变量:-默认值}`利用变量扩展测试存在性与空。若变量存在且有值，传回变量的值，否则传回默认值。

2. `${待测变量:=默认值}` 若不存在或空，变量设为默认值！减号时候只是传回！

3. `${待测变量:?提示信息}` 若不存在或空，就提示错误信息，停止执行。

4. `${待测变量:+真值}` 存在且非空传回真值（用来测试某件事是真的）

5. `${变量:位置起点:长度}`截取字符串。

6. `${变量#样式}`由前面对比，删除相符里面最短的

7. `${变量##样式}`由前面对比，删除相符里面最长的

8. `${变量%样式}`由后面对比，删除相符里面最短的

   与“由前面”比较，来看一个例子：

   ```
   $ va="/abc/abcd/abcde/abcdef"
   $ echo ${va#/*} 
   abc/abcd/abcde/abcdef   //删掉了前面的/
   $ echo ${va%/*} 
   /abc/abcd/abcde
   ```

9. `${变量%%样式}`由后面对比，删除相符里面最长的。

10. `${变量/样式/替换字符串}`只替换一个（最长的）

11. `${变量//样式/替换字符串}`替换全部（最长的）

 

## 学习中遇到的问题

1.今天想要把之前学过的总结推到github上面的时候，出现了错误。

### 问题

```
$ git pull origin master
ssh: Could not resolve hostname github.com: Temporary failure in name resolution
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

### 探索

尝试能不能ping通：

```
$ ping github.com
ping: unknown host github.com
```

可能是DNS出现了问题。

### 解决

配置静态ip：

```
$sudo vi /etc/network/interfaces
```

在文件中写入：

```
auto ens33 
iface ens33 inet static 
address 10.0.0.164
netmask 255.255.255.0 
gateway 10.0.0.1
```

配置DNS：

```
$sudo vi /etc/resolv.conf
```

写入：

```
nameserver 223.5.5.5   //阿里的dns
```

再次尝试ping外网，发现可以成功了。git的操作也变得没有问题～

2.用alias建立一个别名share，指向/mnt/linuxshare：

```
$ alias share=/mnt/linuxshare
$ cd share
-bash: cd: share: No such file or directory
```

### 问题

为什么结果显示找不到目录？

书本解释：bash只会对第一个token（组成命令的单词）进行替换别名，而alog并不是第一个token，因此未被展开。（第一个token是cd）

（在之前犯过类似错误，现在知道为什么了，所以记录下来）

