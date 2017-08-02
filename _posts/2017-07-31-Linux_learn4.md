---
layout: post
title: Linux学习小记4
category: Linux
descriptions: Linux学习
keywords: Linux
---

上个星期六匆匆浏览了一遍鸟哥私房菜的第十、十一、十二、十三章（vim、BASH、正规表示法、Shell）后，也没有留下学习笔记和总结，今天重新看看了当时有些不懂的地方。有些地方多看几遍就恍然大悟（鸟哥的讲解还是比较通俗易懂的），但是还是有个别地方令我疑惑不解。

<!-- more --> 

### notice：

+ 如何得知目前的所有变量与环境变量的配置值？

  环境变量用 env 或 export 而所有变量用 set 即可显示

+ 我想要知道，在 /etc 底下，只要含有 XYZ 三个字节的任何一个字节的那一行就列出来，要怎样进行？

  grep [XYZ] /etc/*

  （当时看到这道题想到的是逐个判断包不包含 XYZ 三个字节，看了答案发现原来是如此简单！说明还是不够熟悉命令，实践不够。）

+ 使用 sh -x 来进行程序的 debug真的很好用，当想检查语法错误时使用sh -n，没有错误时不显示信息。

+ 必须要注意判断符号[]中括号的两端需要有空白字节来分隔，好几次练习中因为没有这样做使得执行程序的时候出现了错误。

+ 在grep作搜索中， ^ 符号在字节集合符号(括号[])之内与之外是不同的！ 在 [] 内代表反向选择，在[]外代表定义在行首。

## 学习中出现的问题

### Q1.

#### 问题发现：

```
[zhuangtingting@ ~]$ pr f2.txt


2017-07-31 10:37                      f2.txt                      Page 1


dadsafdf
(省略空白处)
[zhuangtingting@ ~]$ pr f2.txt > f2.txt
[zhuangtingting@ ~]$ cat f2.txt
[zhuangtingting@ ~]$ 
```

以上代码中，我想实现的的是将pr处理过的内容标准输入到f2.txt中，但是这样做使得文件为空。但是把标准输出后面的f2.txt改成f3.txt，是可以实现成功的。

```
[zhuangtingting@ ~]$ cat f2.txt
dsffd
[zhuangtingting@ ~]$ pr f2.txt >> f2.txt
[zhuangtingting@ ~]$ cat f2.txt
dsffd


2017-07-31 10:39                      f2.txt                      Page 1


dsffd

(省略空白部分)
```

使用”>>“测试，结果能成功，pr处理的内容接到了f2.txt文件原有内容的后面。

#### 问题：

为什么pr处理完却不能标准输出到原文件呢？

解答：问题应该是又">"引起的，而不是pr命令。

### Q2.

```
#!/bin/bash
read -p "Pleas input your birthday (MMDD, ex> 0709): " bir
now=`date +%m%d`
if [ "$bir" == "$now" ]; then
echo "Happy Birthday to you!!!"
elif [ "$bir" -gt "$now" ]; then
year=`date +%Y`
total_d=$(($((`date --date="$year$bir" +%s`-`date +%s`))/60/60/24))
echo "Your birthday will be $total_d later"
else
year=$((`date +%Y`+1))
total_d=$(($((`date --date="$year$bir" +%s`-`date +%s`))/60/60/24))
echo "Your birthday will be $total_d later"
fi
```

以上程序用来计算还有几天可以过生日，但是不太能看得懂:

```
total_d=$(($((`date --date="$year$bir" +%s`-`date +%s`))/60/60/24))
```

#### 搜索解答：

```
%s : 从 1970 年 1 月 1 日 00:00:00 UTC 到目前为止的秒数
```

那么该程序的思路就是求出两个日期之间相差的秒数，然后/60/60/24（/秒/分/时），即算出相差多少天。

### Q3.

练习问题：『A=B』且『B=C』，若我下达『unset $A』，则取消的变量是 A 还是 B？

答案：被取消的是 B 喔，因为 unset $A 相当于 unset B 所以取消的是 B ，A 会继续存在！

为何？

解答：

```
[zhuangtingting@ ~]$ A=B
[zhuangtingting@ ~]$ B=C
[zhuangtingting@ ~]$ echo $A $B $C
B C
[zhuangtingting@ ~]$ unset $A
[zhuangtingting@ ~]$ echo $A
B
[zhuangtingting@ ~]$ echo $B

[zhuangtingting@ ~]$ unset A
[zhuangtingting@ ~]$ echo $A

[zhuangtingting@ ~]$
```

正常情况下取消变量应该是`unset 变量名`，这里unset `$A`，`$A`也就是取用A，那么取消的自然是B咯。

