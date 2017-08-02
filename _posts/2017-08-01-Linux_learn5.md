---
layout: post
title: Linux学习小记5
category: Linux
descriptions: Linux学习
keywords: Linux
---

今天到星期五，都将会学习《实战LINUX_SHELL编程与服务器管理》这本书，加强Linux知识的学习。

<!-- more --> 

## 知识点小记：

1. 使用openssh的登录法有三种：

   + `ssh -l 账号 主机名或IP`
   + `ssh 账号@主机名或IP`
   + `ssh 主机名或IP`  （以目前系统中的账号为默认账号）

2. 权限rwx中x对目录来说不是执行的意思，而是可以进入该目录的权限。

3. [a-z_-]代表英文小写字母、下划线、以及-字符（由于-字符代表字符集合的范围，如果要把-也纳为集合的一份子，需要把-放置在集合的开头或结尾。

4. 例如在`./my-work.sh &` 中，&表示把my-work.sh丢到后台去执行。

5. 在/etc/passwd的第二栏原为密码栏，考虑系统安全，编码后的密码已被放入/etc/shadow文件中。

6. 查看目前位于第几层shell `echo $HLVL`

7. 通常Linux/BSD系统，默认会开启7个终端界面。

8. + 登录：bash先执行/etc/profile，再调用～/.bash_profile。
   + 注销：bash调用~/.bash_logout。
   + 执行新shell：执行终端程序或手动执行/bin/bash，或在编辑程序中调用Shell，会先调用/etc/bash.bashrc，再调用~/.bashrc。
   + 执行Script：
     + 使用#!/bin/bash：不调用.bash_profile、.bashrc，但会检查BASH_ENV的内容，如果为空，则执行它指定的启动文件。
     + 使用#!/bin/sh：不调用任何启动文件，没有其他检查环境变量的操作。

9. `echo`默认会自动换行。若不想自动换行，加上`-n`选项。

   之前看鸟哥那本圣经的时候，好像一直没有理解`-e`选项的作用，今天看了这本书，纠正自己的误解，这个选项可让字符串的特殊字符有作用。

10. `:`（冒号）什么都不做，传回真值

  可以利用`:`建立一个空文件：`: > ff.txt`

11. `fc -l`后面接数字可以查找指定编号范围的历史命令。还可以接命令的某几个字符来查找指定关键词之间的历史命令。

12. `read`读取一行数据放入数组中`read -a arr < <(echo 123 45 97 101 88)`

13. `eval`读取变量内容并执行。

---

## 有意思的命令及脚本

1. 清空apache2 的日志文件

   ```
   #! /bin/bash
   cp /dev/null /car/log/apache2/access.log
   ```

2. 重定向输入和输出合用

   ```
   sort < unsort.txt > sorted.txt
   ```

   sort会由unsort.txt读取数据，经过排序之后，将结果转向存储至sorted.txt。

   刚开始一看到这行命令就会以为是没有排序过的就会重定向输出到新文件，但是仔细看了以后，才明白该命令之执行的顺序。

3. 用cat命令和重定向输出来做简易的编辑：

   ```
   $ cat > out.sh
   #! /bin/bash

   echo "Hello World!!!"
   ```

   编辑的内容就存储在out.sh中。

## 学习中遇到的问题

1. 书范例4-1-1

   ```
   $ cat show_name.sh
   #!/bin/bash
   function show_name(){
   echo "今个儿是$1，你$2大大，来自$3”
   }
   name="$1"
   ip="10.0.0.164"
   today=`data +%F`

   if [ $# != 1 ];then
   echo "Usage: $0 [使用者名称]"
   exit
   fi

   show_name "$today" "$name" "$ip"
   sleep 5
   echo
   echo "Bye"

   $ sh show_name.sh zhuangtingting
   show_name.sh: 2: show_name.sh: Syntax error: "(" unexpected
   ```

   问题1：根据提示还是无法解决错误。

   解答：“ 来自$3” ”中有引号错误。

   问题2：

   ```
   if [ $# != 1 ];then
   echo "Usage: $0 [使用者名称]"
   exit
   fi
   ```

   这一段的所要实现的是？

   解答：如果参数不是1个，说明执行该脚本时并没有按预想的“$0 [使用者名称]”这样的规则，则提示用户这样来执行脚本。

2. 变量str=“Hello world"

   printf"%q\n""$str"

   结果显示：”Hello\ world“

   为何？不是很明白%q功能（将特殊字符用\转义？）

   （这个知识点不是学习的重点，跳过）