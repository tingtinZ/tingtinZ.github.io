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

4. 将通用代码粘贴到_includes下的disqus.html里

5. 除了通用代码，还可以发现下面还带了如何显示评论数的代码标签：

   ```
   <script id="dsq-count-scr" src="//tingtinz.disqus.com/count.js" async></script>
   ```

6. 把上面的代码标签也粘贴到post.html里

上述步骤完成后，就可以欣赏自己的成果了。

## 学习心得

在实现过程中不难发现Disqus支持的平台有很多，而我们使用添加代码的方式去在博客上添加Disqus评论系统是非常具有主动性的，我们可以按照意愿随心添加，非常方便有效。同时也能感受