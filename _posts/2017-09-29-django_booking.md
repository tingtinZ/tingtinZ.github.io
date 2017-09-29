---
layout: post
title: Django订餐项目（带部署）
category: Django Python 
descriptions: 实现订餐及相关数据操作，用apache和mod_wsgi进行部署。
keywords: Django 订餐 apache和mod_wsgi部署
---

该订餐项目实现普通用户的订餐和查看菜单功能，以及超级用户对订单、菜单、餐馆的数据操作，后面还加入了数据的统计，用apache和mod_wsgi进行部署（顺便记录建立虚拟主机域名访问出现的问题集锦）。

<!-- more --> 

超级久没写过博客了，在这里交代一下这几天做的东西：`订餐`---》`聊天室`---》`订餐`

先说订餐。

### 系统功能

1. 普通用户

   + 订餐。像一般订餐平台那样，以餐馆为切入，浏览餐馆及部分菜单，选择餐馆后可以浏览该餐馆下的所有菜品，选择后提交表单，跳转到订单信息界面。
   + 查看订单。用户可以查看自己历史订单的时间、菜品、餐馆等信息。
   + 取消订单。普通用户具有下订后的一个小时内取消订单的权利，过时则无效。

2. 超级用户

   除了普通用户具有的功能权限外，还具备以下功能：

   + 订单操作。超级用户可以额外为其他用户增加订单，而且随时可以取消订单。
   + 菜单操作。菜单数据由超级用户管理，具有对菜单进行增、删、改的权利。
   + 餐馆操作。菜单信息与餐馆信息相关联，对餐馆进行操作的同时也对菜单产生了影响。

### 需要的工具

1. python语言编写，django框架搭建。
2. bootstrap美化页面。
3. 最佳辅助jquery，帮助我们操作html节点，处理事件，异步Ajax更是在不刷新页面的情况下帮了不少忙。封装好的datatable和highcharts能够很简单的处理表格和制作图表。

### 用户认证

+ 登录、登出、注册

  使用Django对用户的登录、登出、注册的操作变得极其简单，在用户认证这方面Django为我们提供了很多方法。

  ```python
  def login_view(request):
      if request.method == 'POST':
          username = request.POST.get('username')
          password = request.POST.get('password')
          user = authenticate(username=username, password=password)
          if user is not None:
              if user.is_active:
                  login(request, user)
                  request.session.set_expiry(60*30)
                  return HttpResponseRedirect(reverse('yummy:index_view'))
              else:
                  error_message = "The password is valid, but the account has been disabled!"
                  return render(request, 'booking/error_page.html', {'success_message': error_message})
          else:
              error_message = "The username and password were incorrect."
              return render(request, 'booking/error_page.html', {'error_message': error_message})
      return render(request, 'booking/login.html')

  def logout_view(request):
      logout(request)
      return HttpResponseRedirect(reverse('yummy:login_view'))
      
  def register_view(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            new_user = form.save()
            return HttpResponseRedirect(reverse('yummy:login_view'))
    else:
        form = UserCreationForm()
    return render(request, 'booking/register.html', {'form': form})
  ```



  其中，`request.session.set_expiry(60*30)` 意思是设置session的有效时长。
  ```
  request.user.is_authenticated()
  ```

  这个语句用来判断是否已经登录。

+ 权限

  有些功能只有超级用户才能够拥有，那么我们需要判断登录的用户类型：

  ```
  request.user.is_superuser()
  ```

  若是超级用户，在导航栏给其添加一个管理按钮。

### Django部署

这里利用Apache 和mod_wsgi 部署Django

（这里使用的教程是jeff分享的“django三种部署方法”，看django官方文档真的不知道该干什么，所以打算先知道要干什么，做出来了以后再细化看官当文档明白其含义和用法。）

1. 首先进行安装：

```shell
sudo apt-get install apache2
# Python 2
sudo apt-get install libapache2-mod-wsgi
# Python 3
sudo apt-get install libapache2-mod-wsgi-py3
```

2. 编辑Apache服务器的`yummy_site.conf`文件

   ```
   WSGIPythonHome /home/ztt/Envs/virt_proj
   WSGIPythonPath /home/ztt/Envs/virt_proj/yummy_site

   <VirtualHost *:80>
   	ServerName yummy.com
   	ServerAdmin webmaster@localhost
   	DocumentRoot /home/ztt/Envs/virt_proj/yummy_site

   	WSGIScriptAlias / /home/ztt/Envs/virt_proj/yummy_site/yummy_site/wsgi.py
   	WSGIDaemonProcess yummy.com python-home=/home/ztt/Envs/virt_proj python-path=/home/ztt/Envs/virt_proj/yummy_site
    	WSGIProcessGroup yummy.com

   	<Directory /home/ztt/Envs/virt_proj/yummy_site>
       		<Files wsgi.py>
           		Require all granted
       		</Files>
       	</Directory>

   	Alias /media/ /home/ztt/Envs/virt_proj/yummy_site/yummy/media/
   	Alias /static/ /home/ztt/Envs/virt_proj/yummy_site/yummy/static/
     
           <Directory /home/ztt/Envs/virt_proj/yummy_site/yummy/media>
           	Require all granted
           </Directory>
     
           <Directory /home/ztt/Envs/virt_proj/yummy_site/yummy/static>
           	Require all granted
           </Directory>
   </VirtualHost>
   ```

   【注意】

   ```
   WSGIPythonHome /home/ztt/Envs/virt_proj
   WSGIPythonPath /home/ztt/Envs/virt_proj/yummy_site
   ```

   和

   ```
   WSGIScriptAlias / /home/ztt/Envs/virt_proj/yummy_site/yummy_site/wsgi.py
   WSGIDaemonProcess yummy.com python-home=/home/ztt/Envs/virt_proj python-path=/home/ztt/Envs/virt_proj/yummy_site
   WSGIProcessGroup yummy.com
   ```

   这里两个中有一个就可以。

   看了jeff的博客后，上面说是不同apache2带来的？？

   但是看了官方文档和分享的教程，这个貌似和守护进程模式更有关系。

   还是旧版本没有这样的模式？

   需要进一步了解～～

3. 启动站点`a2ensite yummy_site`

4. 重新加载apache服务`service apache2 reload`

【问题】部署后打开网页提示权限问题。

【原因】家目录没有被访问的权限，而项目在家目录下，因此不能被访问到。

【解决】修改apache的配置文件，设置成当前用户：

```
 $ vim /etc/apache2/apache2.conf
 User ztt
 Group ztt
```

（此外如果还是默认的www用户，而且项目不在家目录下，还应注意数据库的权限！）

顺便记录一下以往建立虚拟主机用域名访问的时候出现的其他问题：

+ 没有在hosts文件上添加域名解析。
+ 把浏览器的代理给改了，导致访问不了。

### 小结

其他部分的实现无外乎与之前的django项目大同小异，但这个项目加入了更多的bootstrap和jquery。其中最深刻的地方有如下几点：（回忆顺序有点混乱）

1. 【发生问题】今天用了django的模板变量给js里面的变量赋值，想用ajax访问后端数据，让后端重新渲染页面来让js变量发生变化达到重新赋值后端新数据的效果。

   ```javascript
    <script>
    $(function() {
    ...
            $.get(path, {
                'date_selected': date_selected
            }, function(){
                chart_updata();
            });
    ...                                                     |
    });                                                     |  (理想很丰满)
                                                            V

    function chart_updata(){
    ...
       var record_count = {{record|safe}};
    ...
    }
    </script>
   ```

    【真实效果】但是真实效果是django的模板变量并没有发生变化，还是原来的值。

    【原因】因为我用ajax请求后台后`return render(request, 'admin/data.html', {'record': json.dumps(record), 'date_list' : date_list})`这里return的是一个生成的页面，而不是xml

    、json抑或是html片段，没有办法再从里面抽取模板变量。

2. 学了jquery之后，对表格内容进行筛选，决定表格内容是否显示或隐藏，这样很容易就实现了根据餐馆显示对应的菜单的效果了！

   ```javascript
   <script type="text/javascript">
   $(function(){
       $("#select_1").click(function(){
           var $option = $("option:selected", this);
           var rtr = $option.text()
           if(rtr=="All"){
               $("table tbody tr").show();
           }
           else{
               $("table tbody tr").hide()
                   .filter(":contains('"+$option.text()+"')").show();
           }
       })
   })
   </script>
   ```

3. 看了jeff做的才发现一个网站开发，更好的用户体验是很有必要的，比如认证失败跳转到友好的提示页面，而不是自带的那种看了就头疼而不知所云的提示。

4. 【编写规范】

   一个项目的、css、js、视图函数等在编写上都要有所规范，最好是各司其职，不要混杂在一起。

   模板html，视图函数尽量简洁。

   变量名不要过长。

   注意页面加载的顺序而带来的影响。

   （回忆到这里先，回头再看看做过的django想起来的时候再更上）
