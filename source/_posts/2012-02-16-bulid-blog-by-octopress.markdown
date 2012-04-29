---
layout: post
title: "搭Blog 学Git"
date: 2012-02-16 14:45
comments: true
categories: 
- octopress 
- Git
---

想要有一个自己的独立博客是很久之前的事。

以前也在其他网站上写过自己的博客，但总觉得用的不顺手，感觉少了归属感，最主要是提供的界面感觉都不怎么好。

最近想学学**Git**，知道了**Github**，开始以为就是个代码仓库，后来才发现还提供了page服务，可以用来在上面搭建自己的**独立博客**。

在Github上搭建博客可以利用[Jekyll](https://github.com/mojombo/jekyll/wiki)或者[Octopress](http://octopress.org/)，Octopress是在Jekyll上建立起来的，即使没有网站设计经验的人也能够快速搭建起自己的博客。

Jekyll和Octopress都是利用**Ruby**实现的，因此在搭建自己博客的过程中难免要接触到一些Ruby的东西，嗯，这说不定也是一个让自己开始了解Ruby的契机。

整个博客的内容和设置都是通过Git进行版本管理的，其中当然需要了解一些基本的Git操作，其实也没有几个常用的命令。

<!--more-->

由于我平时的工作环境都是在Windows上，所以这次搭建博客也在Windows上完成的(我使用的OS是Windows XP)。

下面是我用Octopress搭建博客的过程:

##安装和设置Git

下载[Git for Windows](http://code.google.com/p/msysgit/)，安装完成后就可以在本地使用Git了，但要将内容放到Github上，必须先在Github上注册个账户，然后在本机使用Git创建SSH Key。

在Git Bash上输入命令:
    ssh-keygen -C "username@email.com" -t rsa
Note: username@email.com需要更换成你自己的在Github上注册的Email地址

这样会在用户目录(C:\Documents and Settings\UserName)下产生一个.ssh文件夹，里面为对应的SSH Keys，其中id_rsa.pub是Github需要的SSH公钥文件。

在Github的Account Settings里选择SSH Keys，在其中将id_rsa.pub文件里内容拷贝至
其中的Key里。

这样以后就可以直接使用Git和GitHub了。

##安装Ruby环境

下载[RubyInstaller](http://rubyforge.org/frs/?group_id=167)和[DevKit](https://github.com/oneclick/rubyinstaller/downloads/)。

Octopress需要的Ruby版本为1.9.2，所以选rubyinstaller-1.9.2-p290.exe，DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe。

先安装RubyInstaller，然后解压缩DevKit(路径中不能有中文)。

在"Start Command Prompt with Ruby"命令行中进入DevKit解压缩的目录，然后运行以下命令:

    ruby dk.rb init
    ruby dk.rb install
    gem install rdiscount --platform=ruby

如果安装成功，就可以使用一些Ruby的工具了，也为后面搭建博客提供了基础环境。

##安装Octopress

先通过Git从Github上克隆一份Octopress

    git clone git://github.com/imathis/octopress.git octopress

然后安装一些依赖的工具

    cd octopress
    ruby --version # Should report Ruby 1.9.2
    gem install bundler
    bundle install

安装Octopress默认的Theme

    rake install

##配置Octopress

通过修改_config.yml来配置博客，具体可以在[我的Github](https://github.com/shanewfx/shanewfx.github.com)上查看具体的配置内容。

##开始写博文

通过`rake new_post["title"]`来在**source/_post**下创建一个新的Post，文件名如下面的格式:**2012-02-16-title.markdown**。

然后可以用VIM打开该文件，并在其中输入/编辑文章内容。

写文章时，可以使用[Markdown](http://daringfireball.net/projects/markdown/)和[Octopress Plugins](http://octopress.org/docs/blogging/plugins/)来对内容进行格式排版。

##预览效果

在修改设置或者写完文章后，想看看具体效果，可以通过如下命令来完成:

    rake generate
    rake preview

然后在浏览器中打开http://localhost:4000/来看一看效果。

##将博客部署到Github上

在预览的效果符合自己的预期后，就可以通过如下命令将内容部署到Github上了。

第一次部署前需要先要在Github上创建一个**username.github.com**的repository，
然后通过`rake setup_github_pages`将自己的Blog与上述的repository关联起来。
在其过程中根据提示输入**username.github.com**。

然后就可以通过下面的命令来部署自己的博客内容至Github了:

    rake deploy
    git status
    git add .
    git commit -a -m 'comment'
    git push origin source

##开始浏览自己的博客

在浏览器的地址栏中输入**username.github.com**，打开的网站就是自己的博客了，此刻一个独立博客就如此问世了!

以后就可以享受这种以Geek方式来写博客的生活了!
