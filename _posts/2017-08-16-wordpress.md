---
layout: post
title: 建立wordpress博客
category: wordpress
descriptions: wordpress博客
keywords: wordpress
---

<!-- more --> 

### 1.安装mysql

```
$ sudo apt-get install mysql-server
```

安装完后新建wordrress数据库：

```
$ sudo mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.7.19-0ubuntu0.16.04.1 (Ubuntu)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> create database wordpress;
Query OK, 1 row affected (0.00 sec)
```

### 2.安装apache2

（之前已安装）

### 3.安装PHP

```
$ sudo apt-get install libapache2-mod-php7.0
```

重启apache2服务器

```
$ service apache2 restart
```

写一个php探针查看php是否安装成功

```
$ sudo vim /var/www/html/blogpark.php
<?php
phpinfo();
?>
~ 
```

在浏览器中输入ip/blogpark.php，若出现相应的正确页面，则成功。

### 4.配置php和mysql

```
$ sudo apt-get install php7.0-mysql
$ sudo apt-get install php7.0-gd
$ sudo apt-get install php7.0-curl
```

重启apache2服务器

```
$ service apache2 restart
```

### 5.安装wordpress

下载wordpress

```
$ wget cn.wordpress.org/wordpress-4.7.3-zh_CN.zip
```

解压

```
$ sudo unzip wordpress-4.7.3-zh_CN.zip
```

把wordpress程序移动到网站主目录

```
$ sudo cp -r wordpress/* /var/www/html/   //不应该放在这里，后面更正
```

设置权限

```
$ sudo chown www-data:www-data -R /var/www
```

然后浏览器访问ip/wp-admin/setup-config.php，就可以看到wordpress页面了。

### 6.wordpress设置

在出现的wordpress页面会提示设置，跟着步骤走

+ 输入数据库名、用户名（默认root）、密码、数据库主机、表前缀，提交

  （如果出现“抱歉，我不能写入wp-config.php文件，说明/var/www/html的权限不足，就是刚刚设置的权限啦～）

+ 点击进行安装

+ 输入信息：站点标题、用户名、密码、电子邮件，安装

## 错误点

**不应该把网页等放在/var/www/html里面，一般不去动他，那么我们需要建立虚拟主机，用域名来访问。**

都做到这一步了···wordpress博客也建好了，那就给它搬个家吧，把相关文件拷贝到/var/www/tingblog。

1. 建立虚拟主机，通过域名访问：

   复制站点文件

```
# cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/tingblog.conf
```

​	修改文件

```
 /etc/apache2/sites-available# vim tingblog.conf
 <VirtualHost *:80>       
        ServerName tingblog.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/tingblog

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/tingblog_error.log
        CustomLog ${APACHE_LOG_DIR}/tingblog_access.log combined
```

​	启动站点：

```
# a2ensite tingblog
```

​	重新加载：

```
# service apache2 reload
```

​	在本地hosts文件中添加域名解析（本地测试）

```
127.0.0.1 tingblog.example.com
```

2. 那么把wordpress博客搬家到/var/www/tingblog，不仅需要把文件拷贝过去，还需要修改数据库的数据：

```
mysql> UPDATE wp_options SET option_value = replace( option_value, 'http://10.0.0.164', 'http://tingblog.example.com' ) WHERE option_name = 'home' OR option_name = 's

mysql> UPDATE wp_posts SET post_content = replace( post_content, 'http://10.0.0.164', 'http://tingblog.example.com' ) ;

mysql> UPDATE wp_posts SET guid = replace( guid, 'http://10.0.0.164', 'http://tingblog.example.com' ) ;
```