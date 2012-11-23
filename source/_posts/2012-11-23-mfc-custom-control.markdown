---
layout: post
title: "使用MFC Custom Control实现界面的自绘"
date: 2012-11-23 11:45
comments: true
categories: 
- MFC
- UI
---

最近在做一个视频编辑的项目，其中界面部分有点复杂，负责这部分的同事使用了MFC的Custom Control来实现界面的自绘效果，目前DemoAP的初步效果已经出来，感觉还不错。

因为工作以来基本没有接触过UI方面的编程，加之以前也没有使用过Custom Control，这次做一个简单学习和总结，以便后用。

<!--more-->

#####1.创建MFC工程

在VS2005中创建一个基于Dialog的MFC工程，DemoUI；

#####2.添加CMyPad类

在DemoUI上右击选择Add->Class，在类向导中选择MFC Class，
再点击Add，在Class Name中填入类名，如CMyPad，选择Base class为CWnd，点击Finish，
这时自动生成MyPad.h和MyPad.cpp。

#####3.打开DemoUI.rc，双击Dialog下的IDD_DEMOUI_DIALOG，进入对话框窗口设计界面

#####4.在对话框上加入一个Custom Control控件

#####5.设置Custom Control的属性

设置如下属性，其他属性使用默认即可：

{% codeblock %} 
ID ： IDC_CUSTOM_PAD
Class: MyDrawPad
{% endcodeblock %}

其中，这里的MyDrawPad必须与注册窗口时使用的类名一致，否则编译可通过，但无法运行。

#####6.在CMyPad类中添加一个用于注册窗口的成员函数BOOL RegisterWndClass()

具体实现为：

{% codeblock %}
BOOL CMyPad :: RegisterWndClass()
{
       WNDCLASS windowclass ;
       HINSTANCE hInst = AfxGetInstanceHandle ();

       //Check weather the class is registerd already
       if (!(::GetClassInfo ( hInst, MYWNDCLASS , &windowclass )))
       {
             //If not then we have to register the new class
             windowclass .style = CS_DBLCLKS; // | CS_HREDRAW | CS_VREDRAW;
             windowclass .lpfnWndProc = :: DefWindowProc;
             windowclass .cbClsExtra = windowclass. cbWndExtra = 0;
             windowclass .hInstance = hInst;
             windowclass .hIcon = NULL;
             windowclass .hCursor = AfxGetApp ()->LoadStandardCursor ( IDC_ARROW);
             windowclass .hbrBackground = ::GetSysColorBrush (COLOR_WINDOW );
             windowclass .lpszMenuName = NULL;
             windowclass .lpszClassName = MYWNDCLASS;

             if (!AfxRegisterClass (& windowclass))
             {
                   AfxThrowResourceException ();
                   return FALSE ;
             }
       }

       return TRUE ;
}
{% endcodeblock %}

其中，MYWNDCLASS是在MyPad.h中定义的宏：

{% codeblock %}
#define MYWNDCLASS  L "MyDrawPad"
{% endcodeblock %}

(工程属性中设置为Unicode，API会调用对应的Unicode版本，因此字符串前需加上L)


#####7.在主对话框类CDemoUIDlg中关联自定义控件类CMyPad

首先在CDemoUIDlg类中添加一个成员：```CMyPad m_Pad``` ;

然后进行关联，方法有两种：

- 在```CDemoUIDlg ::OnInitDialog```中关联：```m_Pad .SubclassDlgItem(IDC_CUSTOM_PAD, this)```;

- 在```CDemoUIDlg ::DoDataExchange```中关联：```DDX_Control(pDX, IDC_CUSTOM_PAD, m_Pad)```;

两种方法只能用其中一种，不可同时使用。

#####8.为CMyPad添加消息响应函数

在Class View中选择CMyPad，右击打开其属性界面，选择消息一栏，选择需要响应的消息，并增加对应的消息处理函数。

如选择```WM_PAINT```，选择```<Add>OnPaint```，这样就在CMyPad中自动添加了响应WM_PAINT消息的处理函数OnPaint()。

简单测试一下, OnPaint函数的实现如下：

{% codeblock %}
void CMyPad:: OnPaint ()
{
       CPaintDC dc ( this); // device context for painting
       // TODO: Add your message handler code here
       // Do not call CWnd::OnPaint() for painting messages
       dc .DrawText ( L"test123" , CRect(0,0,100,100), DT_LEFT );
}
{% endcodeblock %}

#####9.效果测试

编译运行，即可看到在Custom Control中绘制出"test123"字符。

（完）