---
layout: post
title: Python学习5
category: Python
descriptions: Python
keywords: Python
---

学习自《像计算机科学家一样思考Python+(第2版)》。

<!-- more --> 

#### 字符串运算

+ `+`用于字符串拼接

  ```
  >>> str1 = 'abc'
  >>> str2 = 'a'
  >>> str1 + str2
  'abca'
  ```

+ `*`执行重复运算

  ```
  >>> str1 = 'abc'
  >>> str1*2
  'abcabc'
  ```

#### print函数

print函数默认会自动换行，把结尾变成空格。可以阻止这个行为：

```
>>> print('hello', end=' ')
```

#### turtle模块

这个模块可以让我们使用海龟图形（turtle graphics）绘制图像。

```
# -*- coding:utf-8 -*-
import turtle
bob = turtle.Turtle()
print(bob)
turtle.mainloop()
```

执行结果：会新建一个窗口，窗口中间有一个小箭头，代表海龟。

在这里，新建了一个Turtle对象，并赋值给bob。mainloop告诉窗口等待用户操作。

我们可以调用方法来移动该对象：

+ `bob.fd()`前进，接受的实参是像素距离
+ `bob.bk()`后退，接受的实参是像素距离
+ `bob.lt()`向左转，接受的实参是角度
+ `bob.rt()`向右转，接受的实参是角度

#### 字符串方法

用点标记法。

+ `upper()`大写

+ `find()`查找子字符串

  ```
  >>> str1 = 'hello world'
  >>> str1.find('hello')
  0
  >>> str1.find('world')
  6
  >>> str1.find('lo',2)   #从索引2开始查找
  3
  >>> str1.find('lo', 1, 2)   #不包括2
  -1
  ```

#### zip函数

zip是一个内建函数，可以接收多个序列组，返回一个元组列表。

```
>>> s='abc'
>>> t = [0, 1, 2]
>>> z = zip(s, t)
>>> for pair in z:
...    print(pair)
...    
('a', 0)
('b', 1)
('c', 2)
```

#### 随机数

random模块提供了生成伪随机数的函数。

```
>>> import random
#返回0.0到1.0的随机浮点数（不包括1.0）
>>> random.random()
0.5198304811062424
#返回5到10之间的整数（两个数都包括）
>>> random.randint(5, 10)
6
#从一个序列中随机选择一个元素
>>> t = [1, 2, 3]
>>> random.choice(t)
3
```



### 学习中遇到的问题

- [ ] 

1.  zip既然返回的是元组列表，为什么不能用索引？用list()后并赋值给l后，得到的是一个空列表？？

   ```python
   >>> z[0][0]
   Traceback (most recent call last):
     File "<input>", line 1, in <module>
   TypeError: 'zip' object is not subscriptable
   >>> list(z)
   [('a', 1), ('b', 2), ('c', 3)]
   >>> l = list(z)
   >>> l
   []
   ```

   来观察一下在python2.7中的表现：

   ```python
   >>> s = 'abc'
   >>> t = [1, 2, 3]
   >>> z = zip(s, t)
   >>> z[0]
   ('a', 1)
   >>> z[0][0]
   'a'
   ```

   【】zip在python2和python3的不同：

   + zip在python2返回的是列表
   + zip在python3中返回的是一个对象，而且支持遍历

   至于为什么会变成空列表？

   ```
   >>> list(z)
   [('a', 1), ('b', 2), ('c', 3)]
   >>> list(z)
   []
   ```

   灰常神奇，这个对象是个迭代器，这个对象里面的数据并不是永久保存的，取出来后就不会再存在了。我们用list()强制转化的时候相当于用next()去一次次返回数据了。
