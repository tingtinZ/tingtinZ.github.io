---
layout: post
title: 添加Disqus评论系统
category: Disqus
descriptions: Disqus评论系统
keywords: Disqus 评论 博客
---

在已经搭建了Github Pages博客之后，还应当为博客丰富其功能，这里将会在博客里添加增加 disqus 评论系统。

<!-- more --> 

## 添加方法

采用手动添加代码的方式实现，实现过程如下（代码个别地方因人而异）：

1. 在Disqus上注册帐号

2. 创建site（站点）

3. 在接下来的页面的安装Disqus步骤中，找到最下面的“Universal Code”（通用代码）

   ```
   <div id="disqus_thread"></div>
   <script>

   /**
   *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
   *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
   /*
   var disqus_config = function () {
   this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
   this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
   };
   */
   (function() { // DON'T EDIT BELOW THIS LINE
   var d = document, s = d.createElement('script');
   s.src = 'https://tingtinz.disqus.com/embed.js';
   s.setAttribute('data-timestamp', +new Date());
   (d.head || d.body).appendChild(s);
   })();
   </script>
   <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
   ```
   观察以上代码的注释部分，大概意思就是将配置变量插入动态值，那么看看有在`disqus_config`哪些需要我们修改的配置变量：

   ```
   var disqus_config = function () {
   this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
   this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
   };
   ```

   可以看出有两处：`this.page.url`和`this.page.identifier` ，两者用其一就可以，不使用的设置为空值，前者是所在页面的规范链接，后者是页面的id，这样的设置很好地将disqus评论系统和我所在的页面关联起来。关于这两个的优先查找级别：`identifier`>`url` 。
   由于我的博客是生成的静态网页，没有办法生成页面能唯一标识的变量id，这里将会用`identifier`去设置`disqus_config`
   怎么样让我的评论系统与每一篇文章的所在关联起来?
   我们可以看到每篇文章的url是不同的，下面对我所做的配置进行解析：
   
   ```
   this.page.url = "{{site.blog.url}}{{page.url}}";
   ```
   以上的`page.url`并不包括域名，在_config.yml文件的“permalink: /:year/:month/:day/:title.html”就可以发现，其实`page.url`指的是这部分的文章url，那么我们还缺少我们的域名。`site.blog.url`指的是在_config.yml文件下查找blog部分里的url，也就是我们博客所在的url，那么就可以得到文章url前面的域名了。
4. 将`disqus_config`修改完成后将通用代码粘贴到_includes下的disqus.html里

5. 除了通用代码，还可以发现下面还带了如何显示评论数的代码标签：

   ```
   <script id="dsq-count-scr" src="//tingtinz.disqus.com/count.js" async></script>
   ```

6. 把上面的代码标签也粘贴到post.html里

7. 我们能在Disqus上对评论系统进行个性化的设置，如头像等

上述步骤完成后，就可以欣赏自己的成果了。

## 学习心得

在实现过程中不难发现Disqus支持的平台有很多，而我们使用添加代码的方式去在博客上添加Disqus评论系统是非常具有主动性的，我们可以按照意愿随心添加，非常方便有效。同时也能感受到Github Pages搭建的博客非常个性化，我们能自由地添加模块并与模块所使用的站点关联起来，相比在成体博客只负责发文章来看，自己搭建的博客更自由，对于页面的代码和所属文件夹下的文章、引用、样式、配置等等我们能自由更改和管理。
