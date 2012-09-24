---
layout: post
title: "在Ubuntu中下载编译ffmpeg的过程"
date: 2012-09-24 14:06
comments: true
categories: 
- Ubuntu
- ffmpeg
---

登陆[ffmpeg官网的下载页面](http://ffmpeg.org/download.html)可以得到使用git克隆ffmpeg源代码的地址：
```
git clone git://source.ffmpeg.org/ffmpeg.git ffmpeg
```

在ubuntu的shell下，使用上述git命令来下载ffmpeg，下载所需的时间会有点长。

等ffmpeg下载完成，ubuntu上就已经存在了一份完整的ffmpeg源代码了。

下面就可以进行编译ffmpeg了。

<!--more-->

编译前我们可以使用下面命令对ffmpeg的源代码进行备份：
```
tar czf ffmpeg.tar.gz ffmpeg/
```
这样就在当前目录下产生一个ffmpeg.tar.gz文件。

在以后需要的时候可以通过下面的命令提取出ffmpeg源代码：
```
tar xzf ffmpeg.tar.gz
```

在编译ffmpeg之前，请先安装好基本的C/C++的编译开发环境，请参考[上篇文章](http://shanewfx.github.com/blog/2012/09/22/study-ubuntu-on-virtualbox/)。

另外，ffmpeg编译时需要使用yasm，如果系统中没有安装，可以通过下面的命令来安装：
```
sudo apt-get install yasm
```

上述的编译工具安装好后，就可开始编译ffmpeg了。

ffmpeg是采用autoconfig和automake等工具自动生成makefile，然后再通过make进行编译，具体的编译的过程如下：

- 使用`./configure`产生makefile文件

- 使用`make`进行编译

- 使用`make install`将ffmpeg安装到系统中

安装ffmpeg到系统中，需要root权限，在shell下执行下面的命令切换到root账户下：

```
sudo -i
```
根据提示输入root密码，然后还需再次进入到ffmpeg源代码目录中，再执行`make install`即可。

默认是安装在/user/local下，其中：

- 头文件放在/user/local/include目录下

- 编译好的libs放在/user/local/lib目录下，其中，在该目录下还有一个pkgconfig目录，里面存放着每个lib的配置文件

- 编译好的可执行文件(ffmpeg、ffprobe、ffserver)放在/user/local/bin目录下

- 文档在/user/local/share/man/man1目录下，同时在/user/local有一个指向此目录的链接


后面计划研究一下ffmpeg的架构，如何抽取出ffmpeg中某个Codec，如何增加自己的Codec到ffmpeg中，以及如何基于ffmpeg开发一个简单的播放器。

(全文完)

