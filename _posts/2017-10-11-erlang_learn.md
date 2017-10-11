---
layout: post
title: erlang学习小记1
category: erlang
descriptions: 记录erlang学习的知识点
keywords: erlang
---

主要内容：

1.顺序编程（shell、变量、整数、浮点数、原子、元组，列表，字符串）

2.模块与函数（模块、fun、列表求和、列表推导、关卡）

3.记录与映射组（记录、映射组、映射组内置函数）

<!-- more --> 

#### 顺序编程

1. Shell

   开启shell

   ```
   $ erl
   ```

   停止shell

   ```
   q()
   ```

2. 变量

   + 只能绑定一次
   + 大写开头

3. 整数

   任意长度，不用担心溢出

4. 浮点数

   + 用/给两个整数做除法，结果自动转换成浮点数
   + div：做除法后舍去余数
   + rem：做除法后剩下余数

5. 原子

   + 表示常量值
   + 小写开头
   + 可以放在单引号内
   + 一个原子的值就是它本身

6. 元组

   例如P = {10, 45}

   元组里的字段没有名字，，可以将原子作为第一个元素，表示元组是什么：Person = {person, {name, joe}, {height, 1.82}}

7. 列表

   例如[1,2,3,4]

   + 列表头：在[1,2,3,4]里为整数1
   + 列表尾：在[1,2,3,4]里为整数[2,3,4]
   + [H|T]

8. 字符串

   ```shell
   1> [1,2,3].
   [1,2,3]
   2> [83,117,114,112,114,105,115,101].
   "Surprise"
   3> $s.
   115
   4> [$S,$u,$r,$p,$r,$i,$s,$e].
   "Surprise"
   ```

9. 模式匹配

   一个特别的例子：[H|T]="cat"  =====>  H=99, T="at"

---

#### 模块与函数

1. 模块

   + 保存在扩展名为.erl的文件里
   + 先编译才能运行，编译后以.beam为扩展名
   + 模块声明-module(hello).
   + 导出声明-export([start/1]).   1是表明多少个参数
   + 要注意逗号，分号，句号的使用区别

2. fun

   + 用于代表函数的数据类型称为fun

   + map

     ```shell
     8> Even = fun(X) -> (X rem 2) =:= 0 end.
     #Fun<erl_eval.6.90072148>
     9> Even(8).
     true
     10> lists:map(Even, [1,2,3,4,5,6]).
     [false,true,false,true,false,true]
     ```

   + filter

     ```shell
     11> lists:filter(Even, [1,2,3,4,5,6]).
     [2,4,6]
     ```

3. 简单的列表处理

   + 列表求和：lists:sum(L).

4. 列表推导

   例如[2*X || X <- L].

5. 关卡

   ```erlang
   max(X, Y) when X > Y -> X;
   max(X, Y)  -> Y.
   ```

   + 关卡序列：分号隔开，只要有一个通过则为true
   + 关卡由一系列关卡表达式组成，用逗号隔开，全部通过则为true
   + 关卡判断函数在书P51

6. case

7. if

---

#### 记录与映射组

1. 记录

   ```erlang
   -record(todo, {
                  status = reminder,
                  who = joe,
                  text,             %%相当于text=undefined
                 }).
   ```

   记录的相关操作

   ```shell
   1> #todo{}.
   2> X1 = #todo{status=urgent, text="Fix errata in book"}.
   3> X2 = X1#todo{status=done}.
   4> rf(todo).   %%使shell忘了todo的定义
   ```

2. 映射组

   + 键—值存储

   + 没有记录名，例如

     ```erlang
     #{Key1 Op Val1, Key2 Op Val2, ..., KeyN Op ValN}
     ```

     其中Op是=>或:=其中一个

     使用映射组的最佳方式是在首次定义某个键值的时候使用Key=>Val，而在修改某个键值的时候使用Key:=Val

   + 内置函数

     + maps:new() -> #{}
     + erlang:is_map(M) -> boolean()
     + maps:to_list(M) -> [{K1,V1},...,{Kn,Vn}]
     + maps:from_list([{K1,V1},...,{Kn,Vn}]) -> M
     + maps:size(Map) -> NumberOfEntries
     + maps:is_key(Key,Map) -> boolean()
     + maps:get(Key,Map) -> Val
     + maps:find(Key,Map) -> {ok,Value} | error
     + maps:keys(Map) -> [Key1,...,[KeyN]
     + maps:remove(Key, M) -> M1
     + maps:without([Key1,...,[KeyN], M) -> M1
     + maps:difference(M1,M2) ->M3