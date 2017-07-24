---
layout: post
title: 使用Github Pages建立独立博客
category: Github pages
descriptions: 使用jekyll模板，在Github Pages上搭建独立博客
keywords: Github Pages, jekyll
---

# 使用Github Pages建立独立博客——学习小结

本文是基于在Github上面已经创建了自己的Github Pages而继续进行搭建博客的过程描述，这里将会在linux操作系统上使用jekyll模板发布静态网页，建立起独立博客。

## 安装jekyll

通过配置ruby，借助rubygems安装jekyll，步骤如下：

1. 安装ruby。

   ```
   $ sudo apt-get install ruby
   ```

2. 安装ruby -dev，这是ruby开发包，安装完以后就可以使用gem安装jekyll。

   ```
   $ sudo apt install ruby-dev
   ```

3. 因为官方源无法访问，所以需要切换ruby的source源，把淘宝的镜像加到gem的镜像列表里。

   ```
   $ gem sources --remove https://rubygems.org/
   $ gem sources -a https://ruby.taobao.org/
   ```

4. 用`gem source -l`查看源列表

   ```
   zhuangtingting@zhuangtingting-VirtualBox:~/learngit$ gem source -l
   *** CURRENT SOURCES ***

   https://ruby.taobao.org/
   ```

   如果显示的是上面的结果，那么可以进行下一步。

5. 安装jekyll之前，先安装依赖包bundler。

   ```
   $ sudo gem install bundler
   ```

6. 安装jekyll。

   ```
   $ sudo gem install jekyll
   ```

完成以上步骤后，jekyll安装完成。

## 最简单jekyll模板生成静态网页

```
$ jekyll new myblog
$cd myblog
/myblog $ jekyll serve
```

用上述命令可以最简单地生成一个静态网页，并可以在本地预览效果，	只需要修改配置和信息就能使得站点被设置成自己预想的样子。

## 建立博客

使用jekyll模板就只需要管理文件夹下的文件就可以进行线上发布。具体为，首先建立目录结构，然后增加文件，使用jekyll生成静态网页发行，那么Github就成为了托管的地方。

那么我们先看一下jekyll的目录结构，来理清什么文件该放在什么地方。

### 目录结构

* `_config.yml`

  保存配置数据。很多全局的配置或指令写在这里，也可以通过命令行进行设置配置。


* `_drafts` 

  存放未发布的文章。


* `_includes`

  这里面的就是可以重复利用的文件。这个文件可以被其他的文件包含，重复利用。可以用这个标签 {% include file.ext %}来把文件 _includes/file.ext包含进来。

* `_layouts`

  存放的是模板文件。

* `_posts`

  存放实际的文章内容。文件名必须使用YEAR-MONTH-DATE-title.MARKUP的格式，狂战名代表了文章使用什么格式写的。

* `_site`

  jekyll完成转换之后生成的页面默认存放在这里。

* `index.html` 

  博客的主页。

* `other` 

  存放css、images或其他。

### 头信息

只要文件中有YAML头信息，他们就会从原格式转化成HTML页面，从而成为静态网页的一部分。

头信息必须在文件的开始部分，并且按照YSML的格式卸载两行虚线之间。

## Jekyll在Github Pages上的部署

1. 创建本地博客站点

```
$ jekyll new tingtinZ
```

2. 进入新建的目录，启动jekyll服务

```
$ jekyll serve
```

3. 初始化本地仓库

```
$ git init
```

4. 为远程仓库在本地添加一个origin引用：

```
$ git remote add origin https://github.com/tingtinZ/tingtinZ.github.io
```

5. 将远程仓库中的文件拖到本地：

```
$ git pull origin 
```

6. 在jekyll站点编辑和配置好后，将本地的文章上传到远程仓库的master分支：

```
$ git add .
$ git commit -m "post blogs"
$ git push origin master
```

## 学习中遇到的问题

1. 在添加jekyll站点到暂存区时，并没有添加_site目录

   解答：_site目录会被存放在.gitignore目录下

2. 手动添加并上传了_site目录后，用“https://tingtinz.github.io”访问时出现“404”页面，而不是index

   解答：使用用户/组织站点的Github Pages时候，应该把jekyll站点提交到master分支上，而不是gh-pages，使用项目站点的Github Pages时才是提交到gh-pages分支上，页面才会显示正常。

   ​

> [jekyll 指南](http://jekyll.com.cn/) 