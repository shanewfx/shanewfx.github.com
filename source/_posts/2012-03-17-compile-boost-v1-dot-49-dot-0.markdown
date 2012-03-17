---
layout: post
title: "Compile Boost v1.49.0"
date: 2012-03-17 21:15
comments: true
categories:
- Boost
- C++ 
---

对C++程序员来说，Boost是一个非常有名的库，里面提供了很多强大的功能。

不过，我之前一直没有使用和研究过Boost，最近看一些开源的代码中有使用Boost，感觉很不错，于是，今天抽一点时间先将Boost的使用环境先搭起来，后面慢慢学习。

<!--more-->

##编译Boost

- 先到[Boost的官方网站](http://www.boost.org/)上下载最新的版本Boost v1.49.0, 我选择的是[boost_1_49_0.7z](http://sourceforge.net/projects/boost/files/boost/1.49.0/);

- 将下载的boost_1_49_0.7z解压缩到D:\Program Files\boost_1_49_0中;

- 为了自动编译Boost，编写了下面的Batch，将Batch代码拷贝到文本文件中，重新命名为为compile_boost_1_49_0.bat;

{% gist 2058696 %}

由于我使用的VS2005，所以使用了`--toolset=msvc-8.0`。

如果使用的是其他版本的VS，则上述的代码要作一点小修改，具体就是：

VS2008 -> `--toolset=msvc-9.0`     VS2010 -> `--toolset=msvc-10.0` 

当然，lib的存放的路径也可以改动一下。

- 将上面的compile_boost_1_49_0.bat文件拷贝到D:\Program Files\boost_1_49_0目录中;

- 双击运行compile_boost_1_49_0.bat，这样就开始了漫长的自动编译过程，整个过程大概要2小时（编译了32位和64位的全部lib）;

- 编译结束后，生成的Libs都在D:\Program Files\boost_1_49_0\stage\lib\win32\vs8_0和D:\Program Files\boost_1_49_0\stage\lib\x64\vs8_0两个文件夹下；

到这里Boost编译的过程就结束了。

##设置VS2005

- 在Tools->Options->Projects and Solutions->VC++ Directories中：
      - Win32平台加入Include files和Library files的路径
          - Include files: D:\Program Files\boost_1_49_0
          - Library files: D:\Program Files\boost_1_49_0\stage\lib\win32\vs8_0
 
      - X64平台加入Include files和Library files的路径
          - Include files: D:\Program Files\boost_1_49_0
          - Library files: D:\Program Files\boost_1_49_0\stage\lib\x64\vs8_0

- 通过上述的设置，在VS2005中就可以正常使用Boost了。

**相信此时你一定很激动，赶紧写个测试程序，来体验一下Boost的强大功能吧!**
