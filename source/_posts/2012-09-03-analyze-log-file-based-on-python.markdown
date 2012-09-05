---
layout: post
title: "使用Python图形化分析大Log文件"
date: 2012-09-03 14:14
comments: true
categories: 
- Python
- Matplotlib
- Sed
---

最近因为项目需要经常分析数据量很大的Log文件，以往靠手工目测的分析方法已不太可能快速分析和定位问题。

由于和我一起做这个项目的同事，会使用Python，编写的Python脚本能够将Log中的相关数据以图形的方式显示出来，这样就便于我们快速检测出是否输入、输出有异常情况发生。
这样，也就加快了解决问题的效率。

为此，在这里简要记录一下具体的方法，以便以后在需要的时候能够做参考。

<!--more-->

##工具准备##

需要的工具如下：

- [Python](http://www.python.org/)（版本是2.7.3，自带一个简单的IDE）

- 数值运算库[numpy](http://sourceforge.net/projects/numpy/files/NumPy/)，matplotlib依赖这个库

- 类似matlab的图形库[matplotlib](http://matplotlib.sourceforge.net/)

- 文本编辑工具[sed](http://gnuwin32.sourceforge.net/packages/sed.htm)

有了这些工具，还需要了解一些正则表达式，可参看[这里](http://shanewfx.github.com/blog/2012/09/01/learning-regex/)。

当然，基本的Python脚本编程功底的需要的。网上关于Python的教程有很多，我参考了如下几篇：

- [简明Python教程](http://woodpecker.org.cn/abyteofpython_cn/chinese/)

- [Python学习笔记](http://docs.linuxtone.org/ebooks/Python/python%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.pdf)

- [Dive into Python](http://woodpecker.org.cn/diveintopython/)

从这几天学习的效果来看，Python应该算是比较容易上手的语言。

可能是Python里面的语法和概念和C++有相似之处，所以很多语法基本只有了解一下怎么写就可以了。

其中，列表、序列、字典这几种Python中的数据结构要用熟，另外，在Python中不需要再关心数据的类型，直接用就好了。

终于体会到一点“学好C++之后再学其他语言要相对容易”的感觉。

看了上述的教程，现在写一些简单的脚本也是很容易的了。

##分析Log文件的过程##

- 编写批处理文件，使用Sed命令从Log文件中提取出需要的数据，将所有匹配数据所在的行都打印出来，写并到其他的文件中

参考下面的代码，其中，Sed命令中引号的内容为要匹配文本的正则表达式，正则表达式在两个斜杠之间，后面的p是打印的命令，Sed命令执行的结果被重定向到后面的文件中。

{% codeblock %}
del /f/s/q Video.txt
del /f/s/q Audio.txt

del /f/s/q VideoRecv.txt
del /f/s/q AudioRecv.txt

del /f/s/q videopts.txt
del /f/s/q audiopts.txt

sed -n "/Video.*New Frame/p" tsmux.txt>>Video.txt
sed -n "/Audio.*New Frame/p" tsmux.txt>>Audio.txt

sed -n "/nType : ESTYPE_VIDEO/p" tsmux.txt>>VideoRecv.txt
sed -n "/nType : ESTYPE_AUDIO/p" tsmux.txt>>AudioRecv.txt

sed -n "/origin pts: .*, video/p" tsmux.txt>>videopts.txt
sed -n "/origin pts: .*, audio/p" tsmux.txt>>audiopts.txt
{% endcodeblock %}

这个批处理的脚本要和要分析的Log文件放在同一文件夹下，这样双击执行脚本，文件夹中会多了5个文件，这几个文件就是我们下面Python脚本需要用到的。

- 编写分析Log数据的Python脚本，该脚本运行后会以图形的方法显示出相关数据的分析结果

下面这个例子要完成的功能就是前端输入PTS的总体趋势是否正确，有无明显跳变。

{% codeblock %}
import re
import matplotlib.pyplot as plt

print "analyse video input pts!..."

regex  = re.compile('video pts : (\d+)')
regex1 = re.compile('audio pts : (\d+)')

video_index = []
video_pts   = []

'''
file = open("videopts.txt", "rb")
log = file.readline()
while log:
    found = regex.search(log)
    if found:
        video_index.append(len(video_index))
        video_pts.append(found.group(1))
    log = file.readline()
file.close()
'''

file  = open("videopts.txt", "rb")
log = file.read()
file.close()

m = regex.findall(log)
if m:
    print "video match success"
    for pts in m:
        video_index.append(len(video_index))
        video_pts.append(pts)
else:
    print "not match"


audio_index = []
audio_pts   = []

file1 = open("audiopts.txt", "rb")
log1 = file1.read()
file1.close()

m1 = regex1.findall(log1)
if m1:
    print "audio match success"
    for pts1 in m1:
        audio_index.append(len(audio_index))
        audio_pts.append(pts1)
else:
    print "audio not match"

print "begin to plot"

fig = plt.figure()
video_pts_plot = fig.add_subplot(211)
video_pts_plot.plot(video_index, video_pts, 'r')
audio_pts_plot = fig.add_subplot(212)
audio_pts_plot.plot(video_index, video_pts, 'b')
plt.show()
{% endcodeblock %}

运行这个Python脚本后，可看到输入的Video/Audio PTS以图形的方式被显示出来。

<a href="http://www.flickr.com/photos/shanewfx/7919967470/" title="Flickr 上 shanewfx 的 python"><img src="http://farm9.staticflickr.com/8169/7919967470_a8847583ce.jpg" width="500" height="498" alt="python"></a>

在这个例子中，我们可以看到要使用到Python的正则表达式库re，图形绘制库matplotlib，还需要了解python中文件的操作，列表的使用等。

具体的用法请google其他资料。

（完）


