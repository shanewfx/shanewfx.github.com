---
layout: post
title: "x64 filter移植笔记"
date: 2012-04-11 17:53
comments: true
categories: 
- X64
- VS2005
- C++
---
64位filter开发工具为VS2005，原来使用VC6开发的filter要转换到VS2005下。

32位的filter移植到x64平台上，对于没有汇编的filter，工作比较简单，主要就是编译选项的设置和修改指针与整形数相互强制转换的地方，以及部分数据类型不匹配等。

<!--more-->

###x64编译选项

对于从VC6转换到VS2005的Project，其中x64编译选项主要注意以下几个地方：

- C/C++->Preprocessor->Preprocessor Definitions : 检查或移除、添加编译器预定义常量；

- C/C++->Code Generation->Runtime Library : 对于Release版本这里可能选择Multi-threaded(/MT)或者Multi-threaded DLL(/MD)，如果选择Multi-threaded(/MT)，如果编译报错则在Linker->Input->Addtional Dependencies中要增加MSVCRT.LIB；

- Linker->Input->Addtional Dependencies : 至少包括 strmbase.lib strmiids.lib winmm.lib，其中 strmbase.lib根据编译环境的不同可能为ustrmbase.lib,  strmbasd.lib或 ustrmbasd.lib；

- Linker->Input->Ignore All Default Libraries : 要选择No，否则编译可能会报错；

- Linker->Advanced->Entry Point : 如果填的是DllEntryPointer@12，则编译器会报错，win32下则不会报错；在x64下通常不要填；

- Linker->Advanced->Base Address : 在x64下不要填，win32下如果Linker->Advanced->Entry Point填了DllEntryPointer@12，则这里通常设为0x1c400000；

###指针与回调函数

指针部分重点是要检查使用回调函数的地方，在Win32下，指针和int/long转换是可以的，因为都是32位长度，而在x64下，指针的长度为64位，而int/long仍是32位，这样原有的转换在x64下就会出现问题。在这些地方，要将int/long替换为ULONG_PTR，ULONG_PTR在Win32下为32位，而在x64下则为64位。

###数据类型warnings

不对编译器报出的关于数据类型不匹配的Warnings，要检查并确认不会发生溢出或者截断，没有改变原有的值的情况下可以不用修改。当然，最好Coding的过程中就要避免数据类型可能存在的不匹配问题，否则出现问题时，查找原有就会麻烦了。

###汇编代码的修改

对于有汇编的filter，移植64位版本，除了上述的问题，还涉及汇编代码的修改。

在VS2005 x64中不再支持内联汇编，编译器不再认识__asm关键字。

所以，对于内联汇编的代码，要将函数提取到单独的.asm文件中，并修改函数参数传递、局部变量的分配及堆栈、寄存器管理。这部分可参看[上篇](http://shanewfx.github.com/blog/2012/03/26/vs2005-64bit-programming/)内容。

下面是ARGB转AYUV的一部分x64汇编代码示例：

{% codeblock %}

; extern "C" void ARGB2AYUV(BYTE* pDest, BYTE* pSrc, LONG lWidth, LONG lHeight, LONG lStride)
; X64 assembly version

.data
ALIGN 16
mask0000ffff   DQ    000000000ffffffffH, 000000000ffffffffH
YbYgYrYa       DW    3736, 19235, 9798, 0, 3736, 19235, 9798, 0
UbUgUrUa       DW    16384, -10879, -5505, 0, 16384, -10879, -5505, 0
VbVgVrVa       DW    -2654, -13730, 16384, 0, -2654, -13730, 16384, 0
const128       DW    128, 0, 128, 0, 128, 0, 128, 0
F000           DW    0, 65280, 0, 65280, 0, 65280, 0, 65280


.code
PUBLIC ARGB2AYUV
ARGB2AYUV PROC
    mov qword ptr [rsp+ 8], rcx ;pDest
    mov qword ptr [rsp+16], rdx ;pSrc
    mov dword ptr [rsp+24], r8d ;lWidth
    mov dword ptr [rsp+32], r9d ;lHeight
   
    push rbp
    push rdi
    push rsi
    mov rbp, rsp
    sub rsp, 16
   
    ;int iCycle  = lWidth >> 2;
    mov eax, dword ptr [rbp+48]  ;lWidth
    sar eax, 2                   ;lWidth>>2
    mov dword ptr [rbp-16], eax  ;iCycle
   
    ;int iRemain = lStride - (lWidth << 2);
    mov eax, dword ptr [rbp+48]  ;lWidth
    shl eax, 2                   ;lWidth<<2
    mov ecx, dword ptr [rbp+64]  ;lStride
    sub     ecx, eax
    mov     eax, ecx
    mov qword ptr [rbp-8], 0     ;add for iRemain = 0
    mov dword ptr [rbp-8], eax   ;iRemain

    ;prepare 
    mov rdx, qword ptr [rbp+32]  ;pDest
    mov rcx, qword ptr [rbp+40]  ;pSrc
    mov edi, dword ptr [rbp+56]  ;lHeight
    mov rsi, qword ptr [rbp- 8]  ;iRemain
    
    pxor xmm0, xmm0
    
NextLine:
    mov     eax, dword ptr [rbp-16]  ;iCycle
NextCycle:
    movdqa xmm1, [rcx]       ;A3 R3 G3 B3 A2 R2 G2 B2 A1 R1 G1 B1 A0 R0 G0 B0
    movdqa xmm2, xmm1        ;A3 R3 G3 B3 A2 R2 G2 B2 A1 R1 G1 B1 A0 R0 G0 B0

    punpcklbw xmm1, xmm0     ;00 A1 00 R1 00 G1 00 B1 00 A0 00 R0 00 G0 00 B0
    punpckhbw xmm2, xmm0     ;00 A3 00 R3 00 G3 00 B3 00 A2 00 R2 00 G2 00 B2

    ;y = (9798*r + 19235*g + 3736*b) / 32768
    movdqa     xmm3, xmm1    ;00 A1 00 R1 00 G1 00 B1 00 A0 00 R0 00 G0 00 B0
    movdqa     xmm4, xmm2    ;00 A3 00 R3 00 G3 00 B3 00 A2 00 R2 00 G2 00 B2

    pmaddwd     xmm3, xmmword ptr YbYgYrYa    
    pmaddwd xmm4, xmmword ptr YbYgYrYa    

    pshufd     xmm5, xmm3, 0b1h
    pshufd     xmm6, xmm4, 0b1h

    paddd     xmm3, xmm5      ;Y1 Y1 Y0 Y0
    paddd     xmm4, xmm6      ;Y3 Y3 Y2 Y2

    psrad xmm3, 15
    psrad xmm4, 15
    pand  xmm3, xmmword ptr mask0000ffff ;0 Y1 0 Y0
    pand  xmm4, xmmword ptr mask0000ffff ;0 Y3 0 Y2

    packssdw xmm3, xmm4        ;0 Y3 0 Y2 0 Y1 0 Y0........

    ;u = (-5505*r - 10879*g + 16384*b) / 32768
    ;......
    ;v = (16384*r - 13730*g - 2654*b) / 32768
    ;......

    ;begin to next
    add rdx, 16
    add rcx, 16
    dec eax
    jnz NextCycle
    add rdx, rsi

    dec edi
    jnz NextLine
    
    ;exit
    add rsp, 16
    pop rsi
    pop rdi
    pop rbp    
    emms
    ret
      
ARGB2AYUV ENDP
End 


{% endcodeblock %}

对于寄存器，在x64平台中，原来的32位寄存器被扩展到64位，并新增了8个64位的通用寄存器R8~R15，另外还增加了8个128位的XMM寄存器XMM8~XMM15。

同时，原来的32位通用寄存器仍然可以使用，但32位寄存器和新的64位寄存器混合使用时，要注意相互赋值的地方，应该要匹配。

另外，在内存访问的地方，必须要通过PTR指明数据的类型，否则编译器会报错。





