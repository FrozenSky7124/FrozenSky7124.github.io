---
layout: post
title: "MFC 非模态对话框的释放"
categories: C++
tags: C++ MFC 模态对话框
author: FrozenSky
---

由主窗口new出来的非模态对话框，在释放的时候一定要注意！

1.需要重写当前窗口的`OnCancel()`虚函数，直接调用`DestroyWindow()`函数，然后删掉基类的`OnCancel()`调用。

2.重写当前窗口的`OnNCDestroy()`虚函数，在基类执行完毕后使用 `delete this`彻底释放内存空间。

<pre style="font-family:mononoki;font-size:16;color:gainsboro;background:#1e1e1e;"><span style="font-weight:bold;color:#569cd6;">void</span>&nbsp;<span style="font-weight:bold;color:gold;">HT_MenuSPSDlg</span><span style="font-weight:bold;color:#b4b4b4;">::</span><span style="font-weight:bold;color:#ff8000;">OnNcDestroy</span><span style="font-weight:bold;color:#b4b4b4;">()</span>
<span style="font-weight:bold;color:#b4b4b4;">{</span>
	<span style="font-weight:bold;color:gold;">CDialogEx</span><span style="font-weight:bold;color:#b4b4b4;">::</span><span style="font-weight:bold;color:#ff8000;">OnNcDestroy</span><span style="font-weight:bold;color:#b4b4b4;">();</span>
 
	<span style="color:#57a64a;">//&nbsp;TODO:</span>
	<span style="font-weight:bold;color:#bd63c5;">TRACE</span><span style="font-weight:bold;color:#ff8000;">(</span><span style="font-weight:bold;color:#bd63c5;">_T</span><span style="font-weight:bold;color:#b4b4b4;">(</span><span style="font-weight:bold;color:#d69d85;">&quot;OnNcDestroy!\n&quot;</span><span style="font-weight:bold;color:#b4b4b4;">)</span><span style="font-weight:bold;color:#ff8000;">)</span><span style="font-weight:bold;color:#b4b4b4;">;</span>
	<span style="color:#569cd6;">delete</span>&nbsp;<span style="font-weight:bold;color:#569cd6;">this</span><span style="font-weight:bold;color:#b4b4b4;">;</span>
<span style="font-weight:bold;color:#b4b4b4;">}</span>
 
 
<span style="font-weight:bold;color:#569cd6;">void</span>&nbsp;<span style="font-weight:bold;color:gold;">HT_MenuSPSDlg</span><span style="font-weight:bold;color:#b4b4b4;">::</span><span style="font-weight:bold;color:#ff8000;">OnCancel</span><span style="font-weight:bold;color:#b4b4b4;">()</span>
<span style="font-weight:bold;color:#b4b4b4;">{</span>
	<span style="color:#57a64a;">//&nbsp;TODO:</span>
	<span style="font-weight:bold;color:#bd63c5;">TRACE</span><span style="font-weight:bold;color:#ff8000;">(</span><span style="font-weight:bold;color:#bd63c5;">_T</span><span style="font-weight:bold;color:#b4b4b4;">(</span><span style="font-weight:bold;color:#d69d85;">&quot;OnCancel!\n&quot;</span><span style="font-weight:bold;color:#b4b4b4;">)</span><span style="font-weight:bold;color:#ff8000;">)</span><span style="font-weight:bold;color:#b4b4b4;">;</span>
	<span style="font-weight:bold;color:#ff8000;">DestroyWindow</span><span style="font-weight:bold;color:#b4b4b4;">();</span>
<span style="font-weight:bold;color:#b4b4b4;">}</span></pre>

详细的解释：

一个MFC窗口对象包括两方面的内容：一是窗口对象封装的窗口，即存放在m_hWnd成员中的HWND（窗口句柄），二是窗口对象本身是一个C++对象。要删除一个MFC窗口对象，应该先删除窗口对象封装的窗口，然后删除窗口对象本身。
删除窗口最直接方法是调用CWnd::DestroyWindow或::DestroyWindow，前者封装了后者的功能。前者不仅会调用后者，而且会使成员m_hWnd保存的HWND无效(NULL)。如果DestroyWindow删除的是一个父窗口或拥有者窗口，则该函数会先自动删除所有的子窗口或被拥有者，然后再删除父窗口或拥有者。在一般情况下，在程序中不必直接调用DestroyWindow来删除窗口，因为MFC会自动调用DestroyWindow来删除窗口。例如，当用户退出应用程序时，会产生WM_CLOSE消息，该消息会导致MFC自动调用CWnd::DestroyWindow来删除主框架窗口，当用户在对话框内按了OK或Cancel按钮时，MFC会自动调用CWnd::DestroyWindow来删除对话框及其控件。

对于一个在堆中动态创建的窗口对象，其生命期却是任意长的。所以可能会产生这样的疑问，为什么有些程序用new创建了一个窗口对象，却未显式的用delete来删除它呢？问题的答案就是有些MFC窗口对象具有自动清除的功能。
如前面讲述非模态对话框时所提到的，当调用CWnd::DestroyWindow或::DestroyWindow删除一个窗口时，被删除窗口的PostNcDestroy成员函数会被调用。缺省的PostNcDestroy什么也不干，但有些MFC窗口类会覆盖该函数并在新版本的PostNcDestroy中调用delete this来删除对象，从而具有了自动清除的功能。此类窗口对象通常是用new操作符创建在堆中的，但程序员不必操心用delete操作符去删除它们，因为一旦调用DestroyWindow删除窗口，对应的窗口对象也会紧接着被删除。
对于在堆中动态创建了的非自动清除的窗口对象，必须在窗口被删除后，显式地调用delete来删除对象（一般在拥有者或父窗口的析构函数中进行）．对于具有自动清除功能的窗口对象，只需调用CWnd::DestroyWindow即可删除窗口和窗口对象。注意，对于在堆中创建的窗口对象，不要在窗口还未关闭的情况下就用delete操作符来删除窗口对象。
