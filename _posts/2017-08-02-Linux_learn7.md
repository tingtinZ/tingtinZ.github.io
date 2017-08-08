---
 layout: post
title: Linux学习小记7
category: Linux
descriptions: Linux学习
keywords: Linux
---

学习《实战LINUX_SHELL编程与服务器管理》的第三天，又是个涨知识的好日子～因为大多都有过了解的基础，以下列出遗忘点或新学习的知识。

<!-- more --> 

### 算术运算

1. Bash的算数运算有以下几种：
   + $((算术式)) 
   + expr 算术式
   + $[算术式]
   + declare -i 变量=算术式
   + let 算术式 
2. 数字以0开头，视为八进制；以0x、0X开头，视为16进制
3. $((表达式))中如果表达式中有变量，在变量前最好不要加\$，如果有变量扩展，则要加上\$。
4. r=$((4+m--))先运算4+m再把结果赋值给r，接着m减1。
5. expr的表达式中若有特殊字符，要转义。
6. expr没有乘幂功能。

---

### 流程控制

1. 命令执行后的结束状态值：
   + 执行成功，传回0
   + 执行失败，非0，传回128+n，n是信号
   + 命令不存在，127
   + 命令存在但是没有执行权，126
2. [[判断式]]里特殊字符不需要转义，[]里特殊字符需要转义。
3. 选项nocasematch忽略大小写。
4. for ((;1;))永不停止的循环。
5. select具有“选择”和“循环” 的双重性质，每键入一次编号，就执行一次命令区域。

---

### 函数

1. function关键词可以省略，不省略时函数名称后面的小括号可以省略。

2. 在函数里使用内置命令local可以设定变量的属性为函数私有。

3. shift n移动位置参数的值。

4. Bash的内置命令set可以指定位置参数。

   （注意！！！一旦用set重置位置参数，原有的值就会全部消失！）

5. getopts命令的u:ah表示该选项需要提供一个参数，若没有提供，就会显示错误信息。

6. 一个看起来挺有用的例子：

   ```
   $ vi opt.sh                
   #! /bin/bash

   while getopts u:ah opt
   do
           case $opt in
           u)
                   echo "提供了选项u和参数：$OPTARG" ;;
           a)
                   echo "提供了选项a" ;;
           h)
                   echo "提供了选项h" ;;
           *)
                   ;;
           esac
   done
   ~                                                          "opt.sh" 15L, 194C written   

   $ sh ./opt.sh -u Jack -a -h
   提供了选项u和参数：Jack
   提供了选项a
   提供了选项h
   ```

   这个脚本能够取得用户提供的选项和参数，根据不同的选项，进行不能的处理和执行。

7. 调用函数库的方法：

   + . 路径/函数库文件名
   + source 路径/函数库文件名

---

重定向

1. fd<>文件，开启文件，指定文件代码为fd，可供读写。

2. fd<&-，关闭输入文件

   fd>&-，关闭输出文件

3. n<&m，复制转向输入的文件代码m

   n>&m，复制转向输出的文件代码m

4. &>文件，或>&文件，如果产生错误，会伴随着一起到输出。

---

### sed、awk

1. .代表任意字符

2. ^在行首，$在尾部

3. ^出现在括号里第一个位置，代表“非”，例如[ ^A-Z] 表示除了大写字母之外的一个字符。

4. \\{...\\}指定符合的个数。

5. sed命令：sed ‘样式命令’ 文件

   sed ‘1,4d’ dataf1 删除第一到第四行。

   样式用//含括，表示寻找，例如：

   sed '/La/d' dataf3 把含有La的行删除

6. awk常用格式

   + awk “样式” 文件
   + awk ‘{操作}’ 文件
   + awk ‘样式{操作}’ 文件

---

##  学习中出现的问题

1. 在脚本中有这么一个语句：

   ```
   （上面部分省略）
   declare -i a i q s=0
   （下面部分省略）
   ```

   但是在执行脚本的时候，有这样的提示：

   ```
   netmask_2_bits.sh: 3: netmask_2_bits.sh: declare: not found
   ```

   搜索解答：

   sh命令默认的shell是dash！/bin/sh是链接到dash的，而dash不支持declare关键字。我们看一下是否如此：

   ```shell
   $ ls -l /bin/sh  
   lrwxrwxrwx 1 root root 4 7月  20 11:03 /bin/sh -> dash
   ```

   果然如此，那么怎么把改bash为默认的shell环境呢?执行以下命令，对已安装的包进行重新配置，在菜单中选择是否将 sh 链接到 dash 选择“no”即可，此时默认的shell环境更改到了bash。

   ```
   $ sudo dpkg-reconfigure das
   ```

2. 对以下脚本不是很明白：

   ```shell
   $ vi openf1.sh
   #! /bin/bash

   datafile='dataf1'
   exec 3<&0
   exec < $datafile

   while read
   do
           echo $REPLY
   done

   exec 0<&3 3<&-  
   ```

3. ```
   sed s/^.*anonymous_enable=.*/anonymous_enable=Yes/ &VSFTPD_conf > $TMP_file
   ```

   主要不明白.\*是什么意思

   .\*它匹配包含任意字符（或完全不含字符）的任意长度的字符串。