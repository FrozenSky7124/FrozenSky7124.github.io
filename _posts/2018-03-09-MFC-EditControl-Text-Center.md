---
layout: post
title: "MFC EditControl控件文字垂直居中显示"
categories: C++
tags: MFC EditControl 垂直居中
author: FrozenSky
---

<html>
<pre style="font-family:Inziu Iosevka SC;font-size:17;color:gainsboro;background:#1e1e1e;"><span style="color:#57a64a;">//&nbsp;EditControl文本垂直居中</span>
<span style="font-weight:bold;color:gold;">RECT</span>&nbsp;<span style="font-weight:bold;color:darkkhaki;">editRect</span><span style="font-weight:bold;color:#b4b4b4;">;</span>
<span style="font-weight:bold;color:#ff8000;">GetDlgItem</span><span style="font-weight:bold;color:#b4b4b4;">(</span><span style="font-weight:bold;color:#bd63c5;">IDC_EDIT_IP</span><span style="font-weight:bold;color:#b4b4b4;">)-&gt;</span><span style="font-weight:bold;color:#ff8000;">GetClientRect</span><span style="font-weight:bold;color:#b4b4b4;">(&amp;</span><span style="font-weight:bold;color:darkkhaki;">editRect</span><span style="font-weight:bold;color:#b4b4b4;">);</span>
<span style="font-weight:bold;color:#ff8000;">OffsetRect</span><span style="font-weight:bold;color:#b4b4b4;">(&amp;</span><span style="font-weight:bold;color:darkkhaki;">editRect</span><span style="font-weight:bold;color:#b4b4b4;">,</span>&nbsp;<span style="font-weight:bold;color:#b5cea8;">0</span><span style="font-weight:bold;color:#b4b4b4;">,</span>&nbsp;<span style="font-weight:bold;color:#b5cea8;">4</span><span style="font-weight:bold;color:#b4b4b4;">);</span>
<span style="font-weight:bold;color:#ff8000;">GetDlgItem</span><span style="font-weight:bold;color:#b4b4b4;">(</span><span style="font-weight:bold;color:#bd63c5;">IDC_EDIT_IP</span><span style="font-weight:bold;color:#b4b4b4;">)-&gt;</span><span style="font-weight:bold;color:#ff8000;">SendMessage</span><span style="font-weight:bold;color:#b4b4b4;">(</span><span style="font-weight:bold;color:#bd63c5;">EM_SETRECT</span><span style="font-weight:bold;color:#b4b4b4;">,</span>&nbsp;<span style="font-weight:bold;color:#b5cea8;">0</span><span style="font-weight:bold;color:#b4b4b4;">,</span>&nbsp;<span style="font-weight:bold;color:#b4b4b4;">(</span><span style="font-weight:bold;color:gold;">LPARAM</span><span style="font-weight:bold;color:#b4b4b4;">)&amp;</span><span style="font-weight:bold;color:darkkhaki;">editRect</span><span style="font-weight:bold;color:#b4b4b4;">);</span></pre>
</html>

需要注意：EditControl控件必须启用多行模式

#### MSDN

##### EM_SETRECT message

Sets the formatting rectangle of a multiline edit control. The formatting rectangle is the limiting rectangle into which the control draws the text. The limiting rectangle is independent of the size of the edit control window.

This message is processed only by multiline edit controls. You can send this message to either an edit control or a rich edit control.

##### Parameters
###### wParam 
Rich Edit 2.0 and later: Indicates whether lParam specifies absolute or relative coordinates. A value of zero indicates absolute coordinates. A value of 1 indicates offsets relative to the current formatting rectangle. (The offsets can be positive or negative.)

Edit controls and Rich Edit 1.0: This parameter is not used and must be zero.

###### lParam 
A pointer to a RECT structure that specifies the new dimensions of the rectangle. If this parameter is NULL, the formatting rectangle is set to its default values.

##### Return value
This message does not return a value.
