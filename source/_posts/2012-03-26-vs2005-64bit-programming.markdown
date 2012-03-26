---
layout: post
title: "VS2005 64-bit Programming Note"
date: 2012-03-26 17:47
comments: true
categories: 
- X64
- VS2005
- C++
---

最近接到的任务是将32位的directshow filter移植到64位平台下，因此，借此机会学习了一点关于64位编程方面的内容。

由于平时的开发环境是Windows + VS2005，所以，下面的内容也主要是讨论VS2005下64位编程的一些主要事项。不过，对于其他平台下的64位编程也有参考价值。

之前已经讲过如何搭建VS2005下64位编程环境，看[这里](http://shanewfx.github.com/blog/2012/03/18/64bit-programming/)。

<!--more-->

##关于Windows下64位编程，有如下几篇文章可做参考：

- [开始进行64位Windows系统编程之前需要了解的所有信息](http://www.microsoft.com/china/MSDN/library/Windev/64bit/issuesx64.mspx?mfr=true)

- [Lessons on development of 64-bit C/C++ applications](http://www.viva64.com/en/l/full/), [中文学习笔记](http://www.cnblogs.com/walfud/articles/2291839.html)

- [Programming Guide for 64-bit Windows](http://msdn.microsoft.com/en-us/library/bb427430.aspx)

- [Getting Ready for 64-bit Windows](http://msdn.microsoft.com/en-us/library/aa384198.aspx)

- [Migration Tips](http://msdn.microsoft.com/en-us/library/aa384214.aspx)

- [用VC进行64位编程](http://www.usidcbbs.com/read.php?tid=5247)

- [Introduction_to_x64_Assembly](http://wenku.baidu.com/view/61804438376baf1ffc4fad48.html)

##VS2005下64位编程注意事项

###1. 数据类型模型改变

Windows 32位平台下使用的是ILP32模型，而Windows 64位平台下使用LLP64模型。

在LLP64模型中，只有指针为64位，其余的类型则保证和32位平台一致:

{% codeblock %}
char   -> 1byte
short  -> 2bytes
int    -> 4bytes
long   -> 4bytes
{% endcodeblock %}

Windows平台为开发者提供了很多的数据类型别名，合理使用这些数据类型为我们编写32位和64位共用代码是有帮助的。

长度固定的类型：
INT32、UINT32、LONG32、ULONG32、DWORD32、INT64、UINT64、LONG64、ULONG64、DWORD64

随平台长度变化的类型（32位平台->32位，64位平台->64位）：
INT_PTR、UINT_PTR、LONG_PTR、ULONG_PTR、DWORD_PTR、SIZE_T、SSIZE_T、HALF_PTR、UHALF_PTR

指针：
POINTER_32、POINTER_64


由于数据类型模型的改变，因此，如果原代码中有涉及指针与整数类型之间相互转换（如在设置回调函数指针时，通常会将原模块的this指针作为long型保存起来），则这部分代码必须要改变（将long改为ULONG_PTR），即任何在代码中有假设指针与整数类型位数相同的代码在64位下都会有问题。

涉及指针运算的地方，要检测偏移量是否可能会溢出的问题。（偏移量是通过无符号数和有符号数运算得来的，特别容易出现问题）

另外，64位系统的API函数参数的数据类型可能发生了改变，如LPARAM，WPARAM，LRESULT在32位平台下是32位整型，而在64位平台下则为64位整型，这也是在移植过程中需要注意的地方，防止出现数据截断。

如果API或者自己代码中使用了size_t和ptrdiff_t，要注意它们的长度在32位平台为32位，在64位平台则是64位，这是特别容易出现数据截断的地方。

函数重载（多态）也要留意是否是仅依靠参数的数据类型来区分的。

代码中魔术数也是要check的地方，如果是魔术数是用来假设数据类型的size时，最好要使用sizeof来确定数据类型的size; 另外，魔术数不管是在32位平台还是在64位平台都是被当作一个32位的整型数来处理，特别要留意将-1作为错误码的地方，在32位平台下为0xFFFFFFFF, 而在64为平台下则应该为0xFFFFFFFFFFFFFFFF。如果代码中使用了0xFFFFFFFF这个整型数，那么在32位平台下为-1, 但在64位下却是一个很大的值(0x00000000FFFFFFFF)。因此，代码中有移位操作和使用MASK的地方，也是容易出现数据溢出的地方。


在代码中，打印整型数和指针的地方也需要注意，打印UINT_PTR整数将%u改为%Iu, 对于指针使用%p。

###2. 汇编代码

在VS2005下64位编程不再支持内联汇编，汇编代码需要提取到一个单独的.ASM文件中。

编写64位平台汇编代码需要注意以下几个与32位平台汇编的不同的地方：

- 扩展并增加了寄存器

[32位] EAX、EBX、ECX、EDX、ESI、EDI、EBP、ESP -> [64位] RAX、RBX、RCX、RDX、RSI、RDI、RBP、RSP

可以在64位程序中调用32位的寄存器，如RAX（64位）、EAX（低32）、AX（低16位）、AL（低8位）、AH（8到15位），
相应的有R8、R8D、R8W和R8B，不过不要在程序中使用如AH之类的寄存器

增加了以下寄存器：R8 ~ R15  XMM8 ~ XMM15

- 函数调用约定和参数传递方式

x64平台函数calling convention与x86平台函数calling convention是不同的。

在x86平台下，函数调用约定有：`__cdecl、__stdcall、__fastcall、__thiscall`等，而x64下的调用约定只作如下限制：

- 前4个整数参数（从左至右）通过4个寄存器传递：RCX、RDX、R8、R9，前4个以外的整数参数将传递到堆栈, 指针被视为整数参数;

对于浮点参数，前4个参数将传入XMM0到XMM3的寄存器，后续的浮点参数也是通过堆栈传递。

即使参数可以是通过寄存器传递，但在堆栈上仍需为其预留空间，每个函数至少要在堆栈上预留32个字节（为前4个参数预留空间）, 该空间允许将传递函数函数的寄存器轻松地复制到堆栈中。

当然，如果要传递4个以上的参数，则必须为其预额外的堆栈空间。

- 调用者负责椎栈空间的分配与回收，被调用函数不需要自己负责平衡堆栈（仅用于传递参数的这部分堆栈空间）

注意，被调用函数中有局部变量和保存其他寄存器时，其空间是由被调用函数来分配，并在结束时由自己去回收这部分堆栈空间

{% codeblock %}
|参数6| <-------------------- rsp + 48  <------------ 栈底 
|参数5| <-------------------- rsp + 40
|参数4| <-------------------- rsp + 32 (R9)
|参数3| <-------------------- rsp + 24 (R8)
|参数2| <-------------------- rsp + 16 (RDX)
|参数1| <-------------------- rsp + 8  (RCX)
|函数返回地址| <------------- rsp
|局部变量...|  <------------- rsp会向低地址移动，上述的取参数的偏移地址也需要改动
{% endcodeblock %}

如int foo(int a, int b, int c, int d, int e, int f) { int i, j; return 0; }

{% codeblock %}
|参数f| <-------------------- rbp + 80  <------------ 栈底 
|参数e| <-------------------- rbp + 72
|参数d| <-------------------- rbp + 64 (R9)
|参数c| <-------------------- rbp + 56 (R8)
|参数b| <-------------------- rbp + 48 (RDX)
|参数a| <-------------------- rbp + 40 (RCX)
|函数返回地址| <------------- rbp + 32 ==> 没有分配局部变量空间和保存寄存器前的rsp
|保存RBP|      <------------- rbp + 24
|保存RBX|      <------------- rbp + 16
|保存RSI|      <------------- rbp + 8
|保存RDI|      <------------- rbp - 0  ==> 分配局部变量空前的rsp，并将rsp保存到rbp中         
|局部变量j|    <------------- rbp - 8   <------------ rsp' + 8
|局部变量i|    <------------- rbp - 24  <------------ rsp'     (局部变量先定义的在低地址)
{% endcodeblock %}

{% codeblock %}
MOV [RSP+ 8], RCX
MOV [RSP+16], RDX
MOV [RSP+24], R8
MOV [RSP+32], R9
PUSH RBP
PUSH RBX
PUSH RSI
PUSH RDI
MOV RBP, RSP
SUB RSP, 16


ADD RSP, 16
POP RDI
POP RSI
POP RBX
POP RBP
RET
{% endcodeblock %}
