---
layout: post
title: "Octopress主题改造"
date: 2012-08-13 21:38
comments: true
categories: Octopress
---

Octopress是一个非常不错的博客系统，具有很好的扩展性，默认也提供了一个很好的博客主题。

随着越来越多的人开始使用Octopress搭建自己的个人博客，网络上出现了很多外观基本相同的博客。
虽然Octopress默认的主题设计的很简洁、美观，但为了打造一个属于自己的博客，还是希望能够做的更美观一些。
当然，借此机会自己也能够学习一点Web前端设计方面的知识。

<!--more-->

Octopress支持SASS语法，改造Octopress主题基本是通过修改“sass\custom“下以scss为后缀名的文件来完成，大多数的改造是在_styles.scss这个文件中来实现。

1. 为博客添加背景图片

```
html {
  background: #555555 url("/images/wood.jpg");
  //background: #555555;
}

body > div { 
  background-image: none; 
//background: #F5F5D5
} //侧边栏

body > div > div { //文章内容
  background-image: none; 
  //background: #F5F5D5; 
  //background: url("/images/textbg.jpg");
}
```

2. 改造博客的Header区域

```
html {
  body > header {
    background: none;
	padding: 1.6em 0 1em 0;
	h1 {
      @include text-shadow(0px 1px 0px #999, 0px 2px 0px #888, 0px 3px 0px #777, 0px 4px 0px #666, 0px 5px 0px #555, 0px 6px 0px #444, 0px 7px 0px #333, 0px 8px 7px #001135);
    }
    h2 {
      margin-top: .5em;
      display: none;
    }
  }
}
```

3. 改造导航栏

```
body > nav {
  //background: url("/images/nav.jpg");
  //background: #555555;
  ul.main-navigation {
    padding-left: 3px;
  }
  ul.subscription {
    //display: none;
  }
  a {
    &:hover {
      color: hsl(209.01, 86.94%, 50.04%);
      text-shadow:
        hsl(209.01, 86.94%, 42.04%) 0px 0px 5px,
        hsl(209.01, 86.94%, 42.04%) 0px 0px 7px,
        hsl(209.01, 86.94%, 52.04%) 0px 0px 9px,
        hsl(209.01, 86.94%, 52.04%) 0px 0px 11px,
        hsl(0, 0%, 0%) 0 0 2px;

    }
    i {
      color: hsl(209.01, 86.94%, 50.04%);
      text-shadow:
        hsl(209.01, 86.94%, 42.04%) 0px 0px 5px,
        hsl(209.01, 86.94%, 42.04%) 0px 0px 7px,
        hsl(209.01, 86.94%, 52.04%) 0px 0px 9px,
        hsl(209.01, 86.94%, 52.04%) 0px 0px 11px,
        hsl(0, 0%, 0%) 0 0 2px;
    }
  }
}
```

4. 倒圆角

```
@media only screen and (min-width: 1040px) {
  body > nav {
    @include border-top-radius(.4em);
  }

  body > footer {
    @include border-bottom-radius(.4em);
  }
}
```

5. 给博客加上LOGO图片

```
@media only screen and (min-width: 550px) {

  body > header h1{
      background: url("/images/logo3.png") no-repeat 0 1px;
      padding-left: 65px;
  }

  body > header h2 { padding-left: 65px; }
}
```

6. 改造侧边栏

在”source\_includes\asides“下创建侧边栏相关模块的html文件，修改博客根目录下的_config.yml文件，主要是default_asides、blog_index_asides、post_asides这几项。

```
default_asides: [custom/asides/about.html, custom/asides/weibo.html, asides/recent_posts.html, custom/asides/category_cloud.html, custom/asides/recent_comment.html, custom/asides/blog_link.html, custom/asides/douban.html, custom/asides/license.html]
blog_index_asides: [custom/asides/about.html, custom/asides/weibo.html, asides/recent_posts.html, custom/asides/category_cloud.html, custom/asides/recent_comment.html, custom/asides/blog_link.html, custom/asides/douban.html, custom/asides/license.html]
post_asides: [custom/asides/about.html, custom/asides/weibo.html, asides/recent_posts.html, custom/asides/category_cloud.html, custom/asides/recent_comment.html, custom/asides/blog_link.html, custom/asides/douban.html, custom/asides/license.html]
```

在custom/asides/about.html中添加About Me信息

```
<section>
  <h1>About Me</h1>
  <p><img src="/images/blogImgs/about.jpg"></p>
  <p>C++程序员，在Windows上搞音视频开发4年有余</p>
  <p>爱电子产品，爱Google, 爱折腾</p> 
  <p>喜欢开源的东西, 喜欢读书和思考, 喜欢做一些geek的事情/东西</p>
  <p><img src="/images/myemail.png"  alt="shanewfx@gmail.com" /></p>
</section>
```

在custom/asides/weibo.html中添加新浪微博模块

```
<section>
  <h1>Sina围脖</h1>
  <iframe width="100%" height="550" class="share_self"  frameborder="0" scrolling="no" src="http://widget.weibo.com/weiboshow/index.php?language=&width=0&height=550&fansRow=2&ptype=1&speed=300&skin=1&isTitle=1&noborder=1&isWeibo=1&isFans=0&uid=1684299551&verifier=e30813de&dpc=1"></iframe>
</section>
```

其中，iframe中的代码是来自新浪微博中”账号->我的工具->微博秀“，可以做一些简单的设置，并自动产生出嵌入代码。

在custom/asides/douban.html中添加豆瓣读书列表

```
{% if site.douban_user %}
<section>
<h1>My Douban</h1>
<div>
<script type="text/javascript" src="http://www.douban.com/service/badge/shanewfx/?show=wishlist&amp;n=9&amp;columns=3&amp;hidelogo=yes&amp;cat=movie|book" ></script>
</div>
```
</section>
{% endif %}
```
其中，div中的代码来自[豆瓣](http://www.douban.com/service/badgemakerjs)。
同时，要在_config.yml中添加douban_user: XXX (XXX为你的豆瓣用户名)。

在custom/asides/blog_link.html中添加友情链接
```
<section>
  <h1>大牛博客</h1>
  <ul>
    <li>
      <a href="http://coolshell.cn/">酷壳CoolShell</a>
    </li>
    <li>
      <a href="http://mindhacks.cn/">刘未鹏MIND HACKS</a>
    </li>
    <li>
      <a href="http://blog.codingnow.com/">云风</a>
    </li>
    <li>
      <a href="http://www.cnblogs.com/Solstice/">陈硕</a>
    </li>
  </ul>
</section>
```

在custom/asides/recent_comment.html中添加Disqus评论
```
<section>
  <h1>最新评论</h1>
  <script type="text/javascript" src="http://shanewfx.disqus.com/recent_comments_widget.js?num_items=5&hide_avatars=0&avatar_size=32&excerpt_length=200"></script>
</section>
```
其中，script中的代码由Disqus产生。
同时，需要修改_config.yml中Disqus的相关参数:
disqus_short_name: XXX (XXX为你的Disqus用户名)
disqus_show_comment_count: true

7. 增加一键分享

我目前使用的是[bshare](http://www.bshare.cn/)。

在_config.yml中增加bshare: true。
在“source\_includes\post”下的sharing.html中增加如下代码：

```
{% if site.bshare %}
    <a class="bshareDiv" href="http://www.bshare.cn/share">Sharing</a><script type="text/javascript" charset="utf-8" src="http://static.bshare.cn/b/buttonLite.js#uuid=25fcdf85-62f9-400e-b053-627f102edf5a&amp;style=999&amp;img=http%3A%2F%2Fstatic.bshare.cn%2Fimages%2Fbuttons%2Fbox-shareTo-zh.gif&amp;w=147&amp;h=21"></script>
{% endif %}
```
其中，if中的代码由bshare产生，可以自己选择所需的外观。
这样，在每一篇文章的最下方会出现一个分享的小工具。


8. 添加标签云

这部分需要第三方的plugin支持，目前我还没有解决中文标签在上传到github上连接出错的问题，在本地是OK的。
具体可参看[这篇文章](http://tinyxd.me/blog/2012/06/25/octopress-add-tag-cloud/)。


（完）