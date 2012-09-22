---
layout: post
title: "在VirtualBox上小玩Ubuntu"
date: 2012-09-22 18:08
comments: true
categories: 
- Linux
- Ubuntu
- Virtualbox
---

第一次接触Linux应该是读研时，当时自己在实验室里捣鼓着嵌入式开发，慢慢也就了解了一些Linux的东西，不过当时完全很业余，很多东西都不太懂。

后来，跟其他实验室的同学借了Linux的安装光盘，在自己的机器上第一次装上Linux，不过后来好像也没什么深入的使用。

工作以后，一直是在Windows上做开发，基本不涉及Linux，不过自己对Linux还是蛮感兴趣的，在家里的电脑上也一直安装着Ubuntu，偶尔也会上去玩一玩。

随着和开源软件的接触，发现很多的开源Code都是来自于Linux，自己也一直想学习一下Linux中C/C++开发的方法。

由于自己大部分的工作和家人对电脑的使用都是集中在Windows上，不希望来回切换系统，故多数是在Windows上使用VirtualBox虚拟机安装Ubuntu来使用。

虚拟机上的Ubuntu，我只会安装Linux C/C++开发所必要的一些软件，如代码编辑器Vim, C/C++编译调试工具，代码版本控制工具Git等。

<!--more-->

我使用的Ubuntu版本是10.04，之前安装过11.10这个版本，不过在虚拟机上运行速度不是很好。

如何使用VirtualBox安装Ubuntu，请Google参考相关教程，要注意的是下载下来的Ubuntu的ISO镜像文件，最好验证一下MD5码，以确保镜像文件是完整正确的，否则安装可能会失败。

另外，中文拼音输入法ibus-pinyin还算比较好用，在Ubuntu软件中心搜索ibus-pinyin并安装，然后“系统->首选项->IBUS首选项”切换到输入法选项卡，添加“汉语->拼PINYIN”即可。
 
下面主要是讲一讲Linux下C/C++开发的一些话题：

- Linux C/C++开发环境的搭建
- Linux常用shell命令
- vim的基本操作
- gcc/g++/gdb/make工具的熟悉和使用
- makefile的编写
- autoconf和automake自动生成makefile的方法

##1.搭建Ubuntu下C/C++开发环境

安装C/C++编译工具，包括gcc, g++, gdb, make等，只需要执行下面这条命令即可：
```
sudo apt-get install build-essential
```
使用"gcc -v"来检查一下安装是否成功。

安装自动生成makefile的相关工具,只需要执行下面这条命令：
```
sudo apt-get install automake1.9
```
这样，依赖的工具也会被安装，包括autoscan、aclocal、autoconf、automake等。
有了这些工具，在大的开发项目中，就可以不用自己去编写makefile了。

安装源代码编辑器Vim，会比Ubuntu原本自带的vi更好用些。
关于如何将Vim打造成一个IDE，以后再研究吧，网上也有相关的文章可参考，主要是使用Vim插件并修改Vim配置文件。
安装Vim执行下面这条命令：
```
sudo apt-get install vim
```
这样以后在shell下可直接使用"vim 文件名"，在当前目录下创建一个新文件并打开文件进行编辑或直接打开已存在的文件进行编辑。

安装源代码版本控制工具Git，只需要执行下面这条命令即可：
```
sudo apt-get install git-core
```

上述工具都安装成功后，在Ubuntu上就已经具备开发C/C++程序的基本环境了。

当然，在Linux上做开发也是有IDE可供选择的，如Eclipse、NetBeans等，据同事介绍，似乎NetBeans还算好用，和Windows下VC比较相似了，而做Android开发一般都是用Eclipse。

目前, 我还不打算使用这些IDE，而是想从底层学习这种在shell下用Vim编写源代码和makefile来完成Code编译和调试的开发模式。

这样，不但有助于掌握Vim和常用的shell命令，而且对相关编译工具的使用、makefile的编写以及理解编译的过程都是有帮助的，当然这样感觉上也会更酷。

##2.Linux常用shell命令

Linux中的命令有很多，这里只列出在开发过程中一些比较常用的命令。
每一个命令可能都有一些选项参数可以使用，用`man 命令`或者`命令 --help`可来查看相关说明。
```
++++++++++++++++++++++++++++++++++++++++++++++++++++++
shell命令     |      功能说明
++++++++++++++++++++++++++++++++++++++++++++++++++++++
mkdir                创建目录
rmdir                删除目录
ls                   查看当前目录下的文件和目录(不包括隐藏文件)
ls -a                查看当前目录下的文件和目录(包括隐藏文件)
cd                   切换到指定目录（/表示系统根目录 ./表示当前目录 ../表示当前目录的上一级目录）
pwd                  查看当前目录的绝对路径
cat                  查看文件内容
touch                修改文件日期或创建新文件
rm                   删除文件
mv                   移动文件或修改文件名
cp                   复制文件或目录
chmod                修改文件操作权限
find                 查找文件或目录
grep                 查找文件里符合正则表达式的字符串
sed                  处理和编辑文本
diff                 比较文件间的差异
tar                  文件打包和解包，通过cvf参数打包，通过xvf解包
zip                  压缩工具，.zip
unzip                解压缩zip文件
gzip                 压缩解压缩工具，.gz，通过-d参数进行解压缩
man                  查看命令说明
file                 查看文件类型
which                查看命令文件安装的位置
whereis              查找软件的安装路径
++++++++++++++++++++++++++++++++++++++++++++++++++++++
```

##3.Vim基本操作

被尊称为程序员神器的Vim有着比较高的学习曲线，不过掌握一些基本的Vim命令，足以让你应付一般的文本编辑。

这里有一份[Vim命令的速查卡](http://coolshell.cn/articles/5479.html)：

<a href="http://www.flickr.com/photos/shanewfx/8011943417/" title="Flickr 上 shanewfx 的 vim"><img src="http://farm9.staticflickr.com/8296/8011943417_b813b2b38a.jpg" width="500" height="386" alt="vim"></a>

常用的Vim操作请参考陈皓的[简明Vim练级攻略](http://coolshell.cn/articles/5426.html)，按照攻略来练习吧。


---

（未完待续）










