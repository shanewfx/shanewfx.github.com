---
layout: post
title: "如何维护Github上博客"
date: 2012-02-16 14:59
comments: true
categories: octopress 
---

有了自己的独立博客，日常维护得全靠自己了。

有时，我们需要在不同电脑上对自己博客进行维护或者发表博文。

如果要在一台陌生的电脑(如没有安装Git或者Ruby运行环境等)上进行博客维护，这种情况下, 我们除了需要根据[上一篇文章](http://shanewfx.github.com/blog/2012/02/16/bulid-blog-by-octopress/)介绍的那样来安装必要的Git和Ruby运行环境，还需要从Github上克隆一份自己博客，以便进行维护。

<!--more-->

下面以自己的博客为例，看看如何怎么做。

####从Github上Clone博客
{% codeblock %}
cd f:
cd blog
git clone git@github.com:shanewfx/shanewfx.github.com.git
{% endcodeblock %}

####要从克隆下来的博客代码中checkout出"source分支"
{% codeblock %}
cd shanewfx.github.com
git checkout source
{% endcodeblock %}
这一步是必须的，否则会出现问题，看[这里](http://fancyoung.com/blog/octopress-study/)。

####要重新设置本地博客与Github Page的关联

    rake setup_github_pages

根据提示输入: git@github.com:shanewfx/shanewfx.github.com.git

*上述的设置完成后，除了删除了本地的博客文件夹，否则以后在本地就可以不用再进行设置，对博客的维护直接进行下面的操作即可。不过，在对博客维护前，最好要从Github上获得最新的博客内容，使用下面这个命令即可:*
`git pull origin source`

####对博客进行维护，如修改配置、写博文等
{% codeblock %}
rake new_post["test clone from github"]
rake generate
rake preview
{% endcodeblock %}

####更新博客至Github
{% gist 1863884 %}
