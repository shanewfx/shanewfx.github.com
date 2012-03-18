---
layout: post
title: "VS2005下的64位编程与移植"
date: 2012-03-18 17:34
comments: true
categories:
- C++
- X64
---

最近有个项目需要将32位下的代码移植到64位下，之前没有做过这方面的工作，特别向同事请教了这方面的知识，在此记录下来。

<!--more-->

##VS2005下64位编程环境

1. VS2005安装时要勾选X64 Compilers and Tools，如果安装时忘记了，则可以重新打开VS2005安装程序来添加；

2. VS2005X64编程环境设置

- 在VS2005的Build菜单下选择Configuration Management；

- 在Configuration Management对话框中的Active solution platform下选择New；

- 在New Solution Platform中的Type or select the new platform下选择x64，OK。
   
对于没有汇编的程序，上述设置就可以了。但如果代码中有汇编代码，则还需要Project下的Custom Build Rules中切换到MASM64 Bulid Rule。

缺省情况下，VS2005没有提供到MASM64 Bulid Rule，需要将原来的masm.rules（在C:\Program Files\Microsoft Visual Studio 8\VC\VCProjectDefaults文件夹下）文件拷贝一份，重新命名为masm64.rules，并在VS2005中修改rule中的ml.exe为ml64.exe即可。

上面设置好之后，就可以在Win32和X64平台间自由切换，但要注意的是，切换的时候也需要将Custom Build Rules切换到相应的平台下。

##VS2005下64位编程的注意事项

- X64平台下，不支持内联汇编，必须将汇编代码提取到独立的.asm文件中去；

- 指针不再是32位，而是64位，汇编代码中涉及32位寄存器间接寻址的，移植时需要修改；

- 32位的寄存器被扩展为64位，又增加了一些新的寄存器；

- 函数参数的传递与32位下汇编不再相同，而是前4个参数分别通过RCX, RDX, R8, R9来传递，其他的参数通过堆栈来传递；


上述的应该是最基本的，要在64位平台中进行编程，还需要了解更多的这方面的知识。

