---
layout: post
title: erlang学习小记2
category: erlang
descriptions: 记录erlang学习的知识点
keywords: erlang
---

主要内容：

1. 顺序程序的错误处理（典型内部错误，try...catch，catch，error/1，栈跟踪）
2. 二进制型与位语法（二进制型的编写和操作）
3. 顺序编程补遗（apply、算数表达式、元数、属性、块表达式begin...end，布尔表达式（与或非异或），-include，列表操作++和--，宏-define，匹配操作符，数字，进程字典）
4. 编译和运行程序

<!-- more --> 

#### 顺序程序的错误处理

1. 异常错误的典型内部错误

   + 模式匹配错误
   + 用错误类型的参数调用内置函数
   + 用带有错误值的参数调用内置函数

2. 调用内置函数显示生成一个错误

   + exit(Why)
   + throw(Why)
   + error(Why)

3. try...catch

   ```erlang
   try F of
   	Val -> Val
   catch
     	... %% _:_ -> ... 处理所有异常错误（如果漏了标签，默认是throw）
   (
    after
    	...	
    )
   end
   ```

4. catch

   + 异常错误如果发生在catch语句中，就会被转换成一个描述此错误的{'EXIT', ... }元组。

   + try...catch    VS     catch

     前者概括了信息，后者提供了详细的栈跟踪信息。

     ```erlang
     -module(try_test).
     -export([demo1/0,demo2/0]).

     generate_exception(1) -> a;
     generate_exception(2) -> throw(a);
     generate_exception(3) -> exit(a);
     generate_exception(4) -> {'EXIT',a};
     generate_exception(5) -> error(a).

     demo1() ->
       [catcher(I) || I <- [1,2,3,4,5]].

     catcher(N) ->
       try generate_exception(N) of
         Val -> {N, normal, Val}
       catch
         throw:X -> {N, caught, thrown, X};
         exit:X -> {N, caught, exited, X};
         error:X -> {N, caught, error, X}
        end.

     demo2() ->
       [{I, (catch generate_exception(I))} || I <- [1,2,3,4,5]].
     ```

     ```shell
     1> c(try_test).
     {ok,try_test}
     2> try_test:demo1().
     [{1,normal,a},
      {2,caught,thrown,a},
      {3,caught,exited,a},
      {4,normal,{'EXIT',a}},
      {5,caught,error,a}]
     3> try_test:demo2().
     [{1,a},
      {2,a},
      {3,{'EXIT',a}},
      {4,{'EXIT',a}},
      {5,
       {'EXIT',{a,[{try_test,generate_exception,1,
                             [{file,"try_test.erl"},{line,12}]},
                   {try_test,'-demo2/0-lc$^0/1-0-',1,
                             [{file,"try_test.erl"},{line,27}]},
                   {try_test,'-demo2/0-lc$^0/1-0-',1,
                             [{file,"try_test.erl"},{line,27}]},
                   {erl_eval,do_apply,6,[{file,"erl_eval.erl"},{line,661}]},
                   {shell,exprs,7,[{file,"shell.erl"},{line,684}]},
                   {shell,eval_exprs,7,[{file,"shell.erl"},{line,639}]},
                   {shell,eval_loop,3,[{file,"shell.erl"},{line,624}]}]}}}]
     ```

   + 改进错误消息

     内置函数error/1的一种用途是改进错误消息的质量。例如

     ```erlang
     sqrt(X) when X < 0 ->
     	error({squareRootNegativeArgument, X});
     ```

     如此，当参数小于0时，给出的error信息就会是{squareRootNegativeArgument, X}

   + 栈跟踪

     erlang:get_stacktrace()

---

#### 二进制型与位语法

1. 二进制型

   + 字节串：位数是8的倍数，对应一个字节串

   + 位串：数据位数不是8的倍数

   + 二进制型的编写与操作

     ```shell
     6> <<5,10,20>>.
     <<5,10,20>>
     7> <<"hello">>.
     <<"hello">>
     8> <<65,66,67>>.
     <<"ABC">>
     9> list_to_binary([<<1,2,3>>,1]).
     <<1,2,3,1>>
     10> split_binary(<<1,2,3,4,5,6,7,8,9>>, 3).
     {<<1,2,3>>,<<4,5,6,7,8,9>>}
     11> B = term_to_binary({binaries,"are",useful}).
     <<131,104,3,100,0,8,98,105,110,97,114,105,101,115,107,0,3,
       97,114,101,100,0,6,117,115,101,102,117,108>>
     12> binary_to_term(B).
     {binaries,"are",useful}
     13> byte_size(<<1,2,3,4,5>>).
     5
     ```

2. 位语法

   常用于以位为单位打包或者解包二进制数据。

   （有一丢丢难理解，这一part先不看）

---

#### 顺序编程补遗

1. apply

   + apply(Mod, Func, [Arg1, Arg2, ..., ArgN])

     等价于Mod:Func(Arg1, Arg2, ..., ArgN)

     与直接调用的区别在于模块名或者函数名是可以动态计算出来的。

   + 书上说尽量避免使用apply，如果使用apply对函数进行调用，许多分析工具就无法得知发生了什么，一些特定的编译器优化也不能进行。

2. 算数表达式

   + 书P90（加，减，乘，除，求商，求余，按位反与或异或，移位）

3. 元数

   函数拥有的参数个数

4. 属性

   + 调用modname:module_info().可以返回一个属性列表。


   + 预定义的模块属性

     + -module(modname).

     + -import(Mod,[Name1/Arity1, Name2/Arity2,...]).

     + -export([Name1/Arity1, Name2/Arity2,...]).

     + -compile(Options).

       添加Options到编译器选项列表中，如-compile(export_all).会导出模块里所有的函数。

     + -vsn(Version).

       指定模块的版本号。

   + 用户定义的模块属性

5. 块表达式

   例如，在一个形式为[E||...]的列表推导中，要求E是单个表达式，可是我们也许想要在E中做不止一件事情，这时我们可以用

   ```erlang
   begin
     Expr1,
     ...,
     ExprN
   end
   ```

6. 布尔表达式

   + not B1
   + B1 and B2
   + B1 or B2
   + B1 xor B2

7. 函数引用

   + 引用当前模块的某个函数，例如：

     ```erlang
     -module(x1).
     -export([square/1]).
     square(X) -> X*X.
     double(L) -> lists:map(fun square/1, L).
     ```

   + 引用外部模块的某个函数，例如：

     ```erlang
     double(L) -> lists:map(fun x1:square/1, L).
     ```

8. 包含文件

   + -include(Filename).

     包含文件的扩展名是.hrl。

   + 包含库的头文件：

     -include_lib(Name).

9. 列表操作：++和--

   + 用于列表添加/移除的中缀操作符。

   + ++

     ```shell
     17> [1,2,3] ++ [1,2].
     [1,2,3,1,2]
     ```

   + --

     ```shell
     18> [a,1,x,1] -- [1].
     [a,x,1]
     19> [a,1,x,1] -- [1,1].
     [a,x]
     20> [a,1,x,1] -- [1,1,1].
     [a,x]
     ```

     注意随着1的变化，结果的变化。

10. 宏

    + -define(Constant, Replacement).

      -define(Func(Var1, Var2, ..., Var), Replacement)).

    + ?MacroName 展开这个宏

    + 还有一些预定义宏：

      + ?FILE展开成当前的文件名
      + ?MODULE展开成当前的模块名
      + ?LINE展开成当前的行号

    + 宏控制流，用来控制宏的展开：

      + -undef(Macro).

        取消这个宏的定义

      + -ifdef(Macro).

        仅当Macro有过定义时才执行后面的代码

      + -ifndef(Macro).

        仅当Macro未定义时才执行后面的代码

      + -else.

        可用于ifdef和ifndef之后，如果条件为否，else后面的语句就会被执行

      + -endif.

        标记ifdef或ifndef语句的结尾

11. 模式的匹配操作符

    ```erlang
    func1([{tag, {one, A}, B} | T]) ->
      f(..., {tag, {one, A}, B}, ...),
      f(..., {one, A}, ...)
                            
                            |
                            |  (改进)
                            |
                            V 
                            
    func1([{tag, {one, A}=Z1, B}=Z2 | T]) ->
      f(..., Z2, ...),
      f(..., Z1, ...)                        
    ```

12. 数字

    + K进制整数  K#Digits
    + $C这种写法代表了ASCII字符C的整数代码。
    + 浮点数的例子：1.0  3.14159  -2.3e+6  23.56E-27

13. 进程字典

    ```shell
    1> get().
    []
    2> put(x,20).
    undefined
    3> get(x).
    20
    4> get(y).
    undefined
    5> put(y,40).
    undefined
    6> get().
    [{y,40},{x,20}]
    7> erase(x).
    20
    8> get().
    [{y,40}]
    9> erase().
    [{y,40}]
    10> get().
    []
    ```

14. 短路布尔表达式

    + Expr1 orelse Expr2

      1真，不执行2

      1假，执行2

    + Expr1 andalso Expr2

      1真，执行2

      1假，不执行2

---

#### 编译和运行程序

1. 运行程序的方式

   + 在shell里编译和运行：

     ```shell
     $ erl
     Eshell V6.4  (abort with ^G)
     1> c(hello).
     {ok,hello}
     2> hello:start().
     Hello World
     ok
     ```

   + 在命令提示符界面里编译和运行

     ```shell
     $ erlc hello.erl
     $ erl -noshell -s hello start -s init stop
     Hello World
     ```

   + 作为Escript运行

     ```
     hello
     ---
     #!/usr/bin/env escript

     main(Args) ->
     	io:fromat("Hello world~n").
     ```

     （运行脚本）

2. makefile编译自动化

   不过好像我暂时不需要了解这个

---

#### 并发编程

1. 基本并发函数

   + Pid = spawn(Mod, Func, Args)

     创建一个新进程，返回一个Pid

   + Pid ! Message

     给Pid进程发送消息Message

   + receive...end

     接收消息，然后可以进行匹配

2. rpc远程过程调用

   ```erlang
   rpc(Pid, Request) ->
   	Pid ! {self(), Request},
   	receive
   		Response ->
   			Response
   	end.
   ```

   ​