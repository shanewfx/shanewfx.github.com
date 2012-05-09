---
layout: post
title: "基于GitStack搭建Git中心服务器"
date: 2012-05-03 20:10
comments: true
categories: 
- Git
- GitStack
---

通过前一段时间对Git的学习，基本掌握了Git常用命令的使用方法，并进行了总结，有兴趣的可以看看[Git常用命令的使用情景][1]和[Git学习笔记][2]。

在[Git常用命令的使用情景][1]中提到过多人协作的项目往往是需要一个中心服务器来同步多人之间的工作成果，另外，最终的工作成果通常也是中心服务器上的代码为准（为了项目管理的需要），因此，备份时往往需要对中心服务器上的仓库进行刻盘。

中心服务器上创建的项目仓库一般为裸仓库（没有工作目录），且需要为项目开发人员设置访问和操作中心服务器上仓库的权限（访问、读、写）。

本文将介绍如何使用开源软件[GitStack][3]在Windows上搭建Git中心服务器。

<!--more-->

为了学习如何在Windows上搭建Git服务器，用Google搜索了一下，发现多数的方案是采用CopSSH + msysgit + PuTTY的方式来实现，这种方案使用[SSH](http://zh.wikipedia.org/wiki/SSH)协议（采用公钥和私钥进行身份验证，用[PuTTY][8]可以产生公钥和私钥，关于公钥和私钥可参考[这篇][9]入门介绍）与Git服务器通信，在安全性上应该来说是比较高的，但缺点就是搭建过程比较麻烦，且要清楚一些概念才知道自己在做什么，因此对新手来说有一定的难度。

另外，让我暂时没采用这种的方案的原因是CopSSH已经不再免费了（找到一个免费的版本[Copssh 3.0.3][10]，需要将[icwbase-2.0.3-patch-100.exe][11]这个补丁拷贝到Copssh的安装目录下运行来修复回退键和左右方向键不能正常的问题），对于喜欢开源软件的我来说，还是希望能够找到其他的开源软件来代替。

关于这种方案的几篇文章：

- [Step by Step Setup Git Server on Windows with CopSSH + msysGit and Integrate Git with Visual Studio][4]

- [Setting up a Msysgit Server with copSSH on Windows][5]

- [如何在WINDOWS XP下使用copSSH配置GIT服务器 + TortiseGIT客户端][6]

另外，还有一种采用Gitolite来搭建的方案，可参考[Gitolite构建Git服务器][7]，讲的很详细。

无意之中，通过google发现了[GitStack][3]，查看了官方文档，感觉不需要做什么特殊的设置就可以在Windows上搭建Git服务器，并且对用户权限的设置也很简单，故决定下载下来试用一下，测试下来果然很方便，且在Client端也成功进行了clone和push等操作（虽然中间遇到一个问题，后面会提到）。

为了以后有个参考，特在此记录下用GitStack搭建Git服务器的主要过程。

工具列表：

- 服务器端：[GitStack 1.4.1](http://gitstack.com/download/)，GitStack中已经集成了Git，可以不用再独立安装msysgit
- 客户端：[msysgit 1.7.10](http://code.google.com/p/msysgit/downloads/list)

###下载并安装GitStack

到其[官方网站](http://gitstack.com/)上下载最新版的[GitStack 1.4.1](http://gitstack.com/download/)。

安装文件有100M，要注意的是，目前GitStack只支持下面几个系统（不支持Windows XP）：

- Windows Server 2008
- Windows Server 2008 R2
- Windows Vista
- Windows 7

另外，GitStack是一个新的开源软件（可以看看[release的历史](http://gitstack.com/category/releases/)），目前有些功能可能还不是很完善，文档也不是很全面，好在GitStack并不复杂。

安装和普通的Windows软件一样，双击安装包自动进行安装，要注意的是最好其安装路径中不要包括空格，所以不建议安装到C:\Program Files下，默认是安装到C:\GitStack下。

安装好GitStack后，下面主要就是配置GitStack和仓库管理。

提醒一下，只需要在服务器上安装GitStack即可，其他的客户机上是不要安装的。安装好GitStack后，可以在任意机器上通过浏览器登录到Git服务器上（当然实际上只有仓库的管理员才有权限登录）。

###GitStack的配置

在服务器上，可以通过开始菜单找到GitStack打开，也可以直接打开浏览器，在地址栏里输入http://localhost/gitstack/打开登录界面。

另外，也可以通过server机的IP地址来登录，如server的IP地址为：192.168.0.105，则可以直接在浏览器的地址栏中输入http://192.168.0.105/gitstack/打开登录界面(注意在客户机上只能使用这种方式来打开登录界面，通过ipconfig可以查看本机的IP地址)。

初始状态下，默认的登录账户为admin，登录密码也为admin。管理员登录后可在Settings->General中修改admin的登录密码。

勾选Enable web based repository browsing选项开启在浏览器中直接查看Git仓库的内容。

另外还有两个Repositories和Users & Groups两个界面，其中在Repositories中可以在服务器上创建项目的裸仓库，直接输入仓库名（如输入ProjectRepos），然后点击Create按钮即可（会在服务器C:\GitStack\repositories下创建一个ProjectRepos.git裸仓库），创建好的仓库也会在Repositories中显示出来，并显示出该仓库的clone的地址git clone http://localhost/ProjectRepos.git，之后就可以在Action下通过浏览器查看仓库、添加用户/Group并设置用户/Group权限等。

在Users & Groups中，Users下是用来创建用户或修改用户密码等，每个用户对应一个Username和其Password，已有的用户会在上面的列表中显示出来；Groups下用于创建组，可以在每个Group下添加或移除用户，已有的Group也会在列表中显示出来。

<a href="http://www.flickr.com/photos/shanewfx/7163525050/" title="Flickr 上 shanewfx 的 gitstack_1.5"><img src="http://farm9.staticflickr.com/8164/7163525050_4e0a696de9.jpg" width="500" height="370" alt="gitstack_1.5"></a>


###牛刀小试

上述已经在服务器上创建了一个ProjectRepos.git裸仓库，现在我们在服务器上来克隆该仓库。
{% codeblock %}
cd d:
mkdir project
cd project
git clone http://192.168.0.105/ProjectRepos LocalRepos 或 git clone http://localhost/ProjectRepos LocalRepos 或 git clone http://localhost:80/ProjectRepos LocalRepos
{% endcodeblock %}
默认的是80端口，可以修改为其他端口。
这里，会提示输入用户名和密码，注意输入的用户名和密码不会被显示出来。

cd LocalRepos
进入了工作目录，我们可以添加文件到工作区，并提交到本地仓库中。


然后，将本地修改推送到服务器的仓库里：git push origin master，这里会提示输入用户名和密码，注意输入的用户名和密码不会被显示出来。
通过git remote -v，我们可以查看origin对应的服务器上的仓库地址。

这时打开GitStack，可以看到服务器上仓库有了提交的内容。

###在客户机上克隆服务器的仓库到本地

先在客户机上安装msysgit 1.7.10。

{% codeblock %}
cd d:
mkdir project
cd project
git clone http://192.168.0.105/ProjectRepos LocalRepos
{% endcodeblock %}
这里，会提示输入用户名和密码，注意输入的用户名和密码不会被显示出来。

cd LocalRepos
进入了工作目录，我们可以添加文件到工作区，并提交到本地仓库中。

然后，将本地修改推送到服务器的仓库里：git push origin master，这里会提示输入用户名和密码，注意输入的用户名和密码不会被显示出来。
通过git remote -v，我们可以查看origin对应的服务器上的仓库地址。

这时打开GitStack，可以看到服务器上仓库有了提交的内容。

在客户机上也可以打开GitStack，直接在浏览器的地址栏中输入http://192.168.0.105/gitstack/打开登录界面，当然这需要知道管理员密码。

（这里要注意的是，要保证在客户机上能够成功打开GitStack或者从服务器上克隆仓库，必须将服务器的防火墙关闭，否则在客户机上的这些操作就会失败。这个问题一直困扰了我好几个小时。）


可见，服务器和客户机在操作上已经没有什么区别了，这正是Git作为分布式版本控制系统的体现。

[1]:http://shanewfx.github.com/blog/2012/04/28/git-command-note/
[2]:http://shanewfx.github.com/blog/2012/04/21/learn-git-command/
[3]:http://gitstack.com/
[4]:http://www.codeproject.com/Articles/296398/Step-by-Step-Setup-Git-Server-on-Windows-with-CopS
[5]:http://www.timdavis.com.au/git/setting-up-a-msysgit-server-with-copssh-on-windows/
[6]:http://www.cnblogs.com/Yinner/archive/2011/05/01/2034147.html
[7]:http://www.ossxp.com/doc/git/gitolite.html
[8]:http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[9]:http://www.360doc.com/content/12/0502/18/1016783_208170505.shtml
[10]:http://sourceforge.net/projects/sereds/files/Copssh/3.0.3/
[11]:http://sea.tomsk.ru/pub/soft/git/Copssh_3.0.3_Installer/

（全文完）
