---
layout: post
title: "程序员的代码编辑器--Sublime Text 2"
date: 2013-01-06 15:43
comments: true
categories: 
- Sublime-Text2
- FreeSoftware
---

####===目录===####

- 在Windows/Ubuntu上安装Sublime Text 2
- 在Windows/Ubuntu上搭建Sublime Text 2的C/C++编译环境
- 使用Sublime Text 2编写、编译、运行C++代码
- 推荐几个程序员喜欢的Sublime Text 2插件
- 使用Sublime Text 2浏览Source Code
- 使用Sublime Text 2和Github Gist管理代码片段
- 开启Sublime Text 2中的VIM功能
- Sublime Text 2与坚果云和HK4WIN的配合使用

### 0.序言 ###

元旦小长假前几天偶然中接触到[Sublime Text 2][1]， 初步使用下来感觉很不错，是又一款比较适合程序员使用的文本编辑器。

在Sublime Text 2之前，一直比较喜欢使用VIM和Notepad++，其中VIM主要用来查看一些源代码文件或编辑一些文本，

而Notepad++更多是用来替代UltraEdit查看Log文件，目前使用下来感觉还不错，搜索功能同样强大。

当然现在VIM还基本属于初步上手阶段，主要是VIM需要记忆的命令太多，而自己在Windows上使用VIM的频率也不怎么高。

<!--more-->

### 1.在Windows/Ubuntu上安装Sublime Text 2 ###

到[Sublime Text 2官网][2]上下载Sublime Text 2，目前的版本是2.0.1。

Windows上我下载的是portable版本，解压后即可运行，这样我结合同步工具就可以再多台机器上共享Sublime Text 2的配置和插件了。

Ubuntu我是通过在虚拟机VirtualBox中安装的，版本是10.04，虽然版本旧了一点，但相对于新版的UI，我还是喜欢这版的。

Ubuntu上下载Sublime Text 2的Linux 32Bit版本即可，解压后即可运行。


### 2.在Windows/Ubuntu上搭建Sublime Text 2的C/C++编译环境 ###

这里C/C++编译器使用的是gcc/g++。

在Windows上使用gcc/g++，可以安装[MinGW][3]，安装时要勾选上g++，默认没选择g++，安装好后需要在系统环境变量**Path**中加上`C:\MinGW\bin`，这里是假设MinGW被安装在C盘中。

打开Windows的命令控制台，输入`g++ -v`来查看g++是否安装成功。

当然在Windows中也可以使用VC++中的编译器，如何在命令行下使用VC++编译器请自行google之。

如果在命令行下可以使用VC++编译器，这样我们可以在Sublime Text 2中新建一个C++编译配置，以实现在Sublime Text 2中使用VC++编译器。

在Ubuntu下安装gcc/g++，在终端命令行中执行`sudo apt-get install build-essential`即可。

Windows下，要在Sublime Text 2中实现编译、运行C/C++代码，需要修改或新建一个C++编译配置。

具体是：

Sublime Text 2中Tools –> Build System –> New Build System…

输入如下内容，并将文件保存为C++Bulider.sublime-bulid。

在Windows中，该文件被保存在Sublime Text 2目录下的Data\Packages\User中。

在Ubuntu下，该文件被保证在当前用户目录下的.Config/sublime-text-2/Packages/User中。

{% codeblock %}
{
     "cmd": ["g++", "${file}", "-o", "${file_path}/${file_base_name}"], // For GCC On Windows and Linux
     //"cmd": ["CL", "/Fo${file_base_name}", "/O2", "${file}"],     // For CL on Windows Only
     "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
     "working_dir": "${file_path}",
     "selector": "source.c, source.c++",

     "variants":
     [
          {
               "name": "Run",
               //"cmd": ["bash", "-c", "g++ '${file}' -o '${file_path}/${file_base_name}' && '${file_path}/${file_base_name}'"]  // Linux Only
               "cmd": ["CMD", "/U", "/C", "g++ ${file} -o ${file_base_name} && ${file_base_name}"]  // For GCC On Windows Only
               //"cmd": ["CMD", "/U", "/C", "CL /Fo${file_base_name} /O2 ${file} && ${file_base_name}"]   // For CL On Windows Only
          }
     ]
}
{% endcodeblock %}

我的机器上直接使用sublime Text 2默认的C++编译配置也是正常的，应该是我之前安装了Git的原因。

ubuntu下也是可以直接使用sublime Text 2默认的C++编译配置的。

搭建好C/C++编译环境后，Sublime Text 2中编译运行C/C++代码了。

### 3.使用Sublime Text 2编写、编译、运行C++代码 ###

如在Sublime Text 2中新一个Demo.cpp文件，在其中输入代码：

{% codeblock %}
#include <stdio.h>

int main()
{
     printf("hello world!\n");
     return 0;
}
{% endcodeblock %}

勾选Tools –> Build System –>C++或者C++Bulider，使用`Ctrl + B`编译代码，`Ctrl + Shift + B`执行程序。


### 4.推荐几个程序员喜欢的Sublime Text 2插件 ###

和VIM、Notepad++等一样，Sublime Text 2也支持通过插件来扩展其功能。

Sublime Text 2中安装插件前可先安装**Package Control**，然后通过Package Control来查找、安装插件。

安装Package Control的方法是：

在Sublime Text 2中按Ctrl + `，调出Sublime Text 2的命令行，在其中输入如下内容后，回车即可。

{% codeblock %}
import urllib2,os; pf='Package Control.sublime-package'; ipp=sublime.installed_packages_path(); os.makedirs(ipp) if not os.path.exists(ipp) else None; urllib2.install_opener(urllib2.build_opener(urllib2.ProxyHandler())); open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read()); print 'Please restart Sublime Text to finish installation'

{% endcodeblock %}
安装成功后，重启Sublime text 2，这时，在Preferences下看到Package Control了。

在Windows中，Package Control被安装在Sublime Text 2目录下的Data\Installed Packages中；

在Ubuntu中，Package Control被安装在当前用户目录下的.Config/sublime-text-2/Installed Packages中。

而Sublime Text 2中其他的插件，在Windows中都被安装在Sublime Text 2目录下的Data\Packages中，

在Ubuntu中被安装在当前用户目录下的.Config/sublime-text-2/Packages中。

执行`Ctrl + Shift + P`调出命令窗口,输入install，根据提示选择Package Control: Install Package;

稍等一下，就会弹出Sublime Text 2的插件列表，在其中选择需要的插件即可完成安装。

下面是几个比较适合程序员使用的Sublime Text 2插件：

* Alignment: 用于代码对齐

* CTags: 用于方便浏览源代码

* Git：源代码版本控制

* Gist：Github中代码片段管理、分享工具



要使Sublime Text 2中的CTags插件可用，需要在系统中安装CTags工具。

到[这里][4]下载CTags工具，Windows选择ctags58.zip，解压后将其中的ctags.exe拷贝到C:\MinGW\Bin下。

Ubuntu选择ctags-5.8.tar.gz，解压后, 在Bash中进入ctags-5.8目录，

通过执行`./configure`, `make`, `make install`来安装ctags。


安装好Gist插件后，需要修改Gist.sublime-settings这个配置文件，在username后输入Github的登陆用户名，在password后输入Github登陆密码，保存即可。



安装好插件后，通过`Ctrl + Shift + P`调出命令窗口，然后输入插件名，根据提示可选择相应的插件功能。



### 5.使用Sublime Text 2浏览Source Code ###

这里主要是利用Sublime Text 2中打开文件夹和快速搜索等功能，配合CTags插件来使用。

对于一个已存在的工程，可以通过Sublime Text 2的Open Folder这个功能来打开工程的全部文件，其中目录结构也同样保留，这个功能对于查看开源代码是非常有帮助的。

而Sublime Text 2的快速搜索功能对于定位代码中的函数、变量等是非常有帮助的，结合CTags插件使用则会更加方便。

使用`Ctrl + P`可调出Sublime Text 2的快速搜索界面，其功能主要包括：

* 可以快速跳转到当前项目中的任意文件，可进行关键词匹配

* 用 @ 可以快速列出/跳转到某个函数

* 用 # 可以在当前文件中进行搜索

* 用 : 加上数字可以跳转到相应的行

* 可通过关键字转到某个文件同时加上 @ 来列出/跳转到目标文件中的某个函数，或是同时加上 # 来在目标文件中进行搜索，或是同时加上 : 和数字来跳转到目标文件中相应的行



### 6.使用Sublime Text 2和Github Gist管理代码片段 ###

通过`Ctrl + Shift + P`调出命令窗口，在其中输入Gist，选择Gist: Open Gist会列出Github上Gist中已存在的代码片段，选择一个可用Sublime Text 2打开查看或修改。

修改后通过Gist: Update File上传到Github的Gist中。

要增加一个新的代码片段，可在Sublime Text 2中新建一个文件并在其中放入代码片段，或打开一个已存在的文件。

然后使用Gist: Create Public Gist，然后输入描述文件和文件名即可。


这里，顺便推荐一款在**Chrome浏览器**中使用Github Gist的插件 -- [EasyGist](https://github.com/simplelife7/EasyGist)。

使用下来感觉还不错，很适合在Chrome中来管理代码片段。

在Google网上应用点里搜索EasyGist，选择扩展程序，然后安装即可。

初次使用需要登录一下。


### 7.开启Sublime Text 2中的VIM功能 ###

通过`Ctrl + Shift + P`调出命令窗口，在其中输入Preferences，选择Preferences Settings - User

将打开的文件内容修改为如下：
{% codeblock %}
{
     "ignored_packages":
     [
     ],
     "vintage_start_in_command_mode": true
}
{% endcodeblock %}

这样，在Sublime Text 2中也可以使用VIM的相关命令了。

如插入文本前需要使用`i`进入插入模式，用`Esc`回到正常模式中。


### 8.Sublime Text 2与坚果云和HK4WIN的配合使用 ###

在Windows中，云同步工具一直在使用[坚果云][5]，在国内算是做的不错的一个，支持多目录同步。

由于我使用的是Sublime Text 2的portable版，这样将Sublime Text 2放入到坚果云的同步目录中。

这样，我在Sublime Text 2中安装的插件和修改的相关配置都会被同步到云端，

这样家中的机器也会自动进行同步，然后可继续使用Sublime Text 2。

为了使用方便，我一直使用[HK4WIN][6]来管理键盘快捷键。

在HK4WIN的配置文件中加入启动Sublime Text 2的快捷键，这样，以后启动Sublime Text 2就很方便了。


[1]: http://www.sublimetext.com/
[2]: http://www.sublimetext.com/2
[3]: http://www.mingw.org/
[4]: http://ctags.sourceforge.net/
[5]: https://jianguoyun.com/
[6]: http://www.songruihua.com/hk4win.html

（完）
