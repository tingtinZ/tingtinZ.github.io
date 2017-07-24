---
layout: post
title: Markdown学习
category: Markdown
descriptions: 轻量级标记语言Markdown，专注内容，减少排版烦恼。
keywords: Markdown, 标记, 轻松排版
---

# Markdown学习总结

## 一、什么是Markdown

​	Markdown 是一种轻量级标记语言，通过使用一些简单的符号（* / ` > [] () #）来标记文本格式，可以导出成PDF、HTML、WORD等多种格式。

## 二、Markdown的优点

1. 语法简单。使用简单的符号标记格式，简单易学。
2. 兼容性强。Markdown 是兼容性非常强的纯文本内容，可以使用任何编辑器打开，格式都不会乱。
3. 导出方便。Markdown 可以导出 PDF、Word、HTML、Epub、LaTeX 等文件格式。
4. 专注内容。不用纠结排版问题。
5. 团队协作。 Github、Gitlab、Gitbook 都支持 Markdown，便于团队协作。

## 三、Markdown不适用的情况

1. 需要复杂排版的时候，比如纸版图书编辑就不太适合用 Markdown。我的做法是从 Markdown 导出 Word 文档，然后交给编辑。
2. 需要复杂表格的时候，因为 Markdown 对表格的支持太简单了。
3. 需要调整图片大小或直接贴本地图片的时候。

## 四、Markdown的常用语法

1. 标题

   行首通过“#”数量表示几级标题，一共只有1~6级标题，1级标题字体最大。

2. 粗体

   将需要设置为斜体的文字两端使用2个“*”包含起来。

3. 斜体

   将需要设置为斜体的文字两端使用1个“*”包含起来。

4. 有序列表

   使用数字后面跟上句号，并空格。

5. 无序列表

   在文字开头添加(*, +, -)实现无序列表，但是要注意和文字之间需要添加空格。

6. 引用

   开头添加“>”表示。

7. 图片

   语法：`![]()`。

8. 链接：

   语法：`[]()。`

9. 代码

   第一种：简单文字出现一个代码框。使用`<blockquote>`。
   第二种：大片文字需要实现代码框。使用Tab和四个空格。

## 五、支持Markdown 的软件及网站

1. 不同系统下常用的Markdown编辑器

   Windows：MarkdownPad、Smark、Atom、VSCode、Miu、Typora、RStudio

   Linux：VSCode、Atom、ReText、UberWriter、RStudio

   OSX：VSCode、Atom、Byword、Mou、Typora、MacDown、RStudio

2. 在线网站

   简书、知乎、CSDN、Github、Gitlab、Gitboo

## 六、总结

1. 学习中遇到的问题：

   （1）刚开始下载的编辑器是MarkdownPad，但是安装失败。

   （2）Typora上使用嵌入实现居中效果时，并没有渲染出效果。

2. 问题的解决：

   （1）没有成功解决安装失败的问题，安装了另一款编辑器Typora。

   （2）通过查阅资料得知该效果在导出文件时才能显现。

3. 学习心得：

   ​	通过查阅资料了解到Markdown的概念及语法，并进行实践，在windows7系统下安装支持Markdown的Typora编辑器后进行编辑。在Typora 的使用上，Typora 的菜单栏对于标题、段落、列表、表格等的格式排版给予了很大的便利。Typora 的编辑与预览融为一体，回车后立即渲染效果，但是却不能再次编辑。另外，对于一些嵌入也不能进行预览，需要导出后才能观察到效果。在Markdown的实践学习体会上，深深的体会到Markdown相对于word等编辑对于并不用到word太多功能的使用者来说，使用Markdown能够轻松进行要求不太高的排版，而且在导出上导出的格式多样，消除了因为要编辑不同格式的文件而下载诸多编辑器和软件的麻烦。