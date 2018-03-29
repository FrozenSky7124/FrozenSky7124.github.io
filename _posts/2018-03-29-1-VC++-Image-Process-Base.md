---
layout: post
title: "第一章 Visual C++图像处理基础"
categories: C++ 图像处理
tags: CDib
author: FrozenSky
---

摘自《Visual C++ 数字图像处理技术详解》（刘海波等编著）

## 颜色模式和调色板

### 颜色模式
 
#### 1. RGB颜色模式
  
3个基本颜色：红R、绿G、蓝B

3个基本特性参数：亮度、色调、饱和度。色调与饱和度合称为色度。

当把红、绿、蓝三色光混合时，通过改变三者各自的强度比例可得到各种色调和饱和度的颜色。
  
#### 2. HSI颜色模式

HSI颜色模式是从人的视觉系统出发，用色调、饱和度、亮度来描述色彩。通常把色调和饱和度通称为色度，用来表示颜色的类别与深浅程度。由于人类的视觉对亮度的敏感程度远强于对颜色浓淡的敏感程度，为了便于色彩的处理和识别，视觉系统经常采用HSI颜色模式。HSI颜色模式可以大大简化图像分析和处理的工作量。

HSI颜色模式和RGB颜色模式只是同一个物理量的不同表示法，因此它们之间存在着转换关系。

- 色调（Hue）：物体传导或反射的波长。取0°~360°的数值来衡量。

- 饱和度（Saturation）：色彩的强度或纯度。代表灰色与色调的比例，并以0%（灰色）到100%（完全饱和）来衡量。

- 亮度（Intensity）：颜色的相对明暗度。以0%（黑色）到100%（白色）的百分比来衡量。

#### 3. 其他颜色模式

Lab颜色模式、YUV颜色模式、CMYK颜色模式等。

### Windows调色板

Windows采用24位真彩色，红、绿、蓝三原色分别用8Bit来表示。其SDK提供了一个名为RGB的宏将一组RGB颜色值转化为24位颜色值。

`COLORREF RGB(BYTE bRed, BYTE bGreen, BYTE bBlue)`

COLORREF并不是24位，而是32位无符号长整型变量。其高8位作为保留用于与将来的系统兼容。
  
Visual C++中，MFC定义了CPalette类来对调色板进行操作。<PAGE-8>
  
Windows中的位图操作与调色板密切相关。Windows使用两种不同的位图，即设备相关位图DDB和设备无关位图DIB。DIB位图文件中包含该位图的逻辑调色板的颜色表，其像素值是该调色板中的颜色索引值。DDB位图文件中不包含调色板信息，其像素值是该系统调色板中的颜色索引值。DIB与DDB的主要区别在于DIB包含一个名为RGBQUAD的结构，描述了DIB位图的颜色表。

Win32 SDK使用调色板句柄HPALETTE来表示调色板，HPALETTE与CPalette对象的相互转换由下面的函数实现：

`CPalette::FromHandle`

`CPalette::GetSafeHandle`

## 数字图像文件格式

### BMP文件格式

计算机内的数字图像通常用由采样点的值所组成的矩阵来表示。每一个采样点单元叫做一个像素（pixel）。由于数字图像格式不同，图像文件一般具有不同的扩展名。最常见的图像格式是位图格式，其文件名以BMP为扩展名。

#### BMP文件的结构：

- 位图文件头结构 BITMAPFILEHEADER
  
- 位图信息头结构 BITMAPINFOHEADER
  
- 位图颜色表 RGBQUAD
  
- 位图像素数据

#### 1. 位图文件头

<pre style="font-family:Inziu Iosevka SC;font-size:17;color:gainsboro;background:#1e1e1e;"><span style="font-weight:bold;color:#569cd6;">typedef</span>&nbsp;<span style="font-weight:bold;color:#569cd6;">struct</span>&nbsp;<span style="font-weight:bold;color:gold;">tagBITMAPFILEHEADER</span>&nbsp;<span style="font-weight:bold;color:#b4b4b4;">{</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">WORD</span>&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">bfType</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图文件的类型，必须为&quot;BM&quot;，即0x424D，才是Windows位图文件</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">DWORD</span>&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">bfSize</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//整个BMP文件的大小</span>
		<span style="font-weight:bold;color:gold;">WORD</span>&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">bfReserved1</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图文件保留字，必须为0;</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">WORD</span>&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">bfReserved2</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图文件保留字，必须为0;</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">DWORD</span>&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">bfOffBits</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图数据起始位置，以相对于文件头的偏移量表示，以字节为单位</span>
<span style="font-weight:bold;color:#b4b4b4;">}</span>&nbsp;<span style="font-weight:bold;color:gold;">BITMAPFILEHEADER;</span></pre>

#### 2. 位图信息头

<pre style="font-family:Inziu Iosevka SC;font-size:17;color:gainsboro;background:#1e1e1e;"><span style="font-weight:bold;color:#569cd6;">typedef</span>&nbsp;<span style="font-weight:bold;color:#569cd6;">struct</span>&nbsp;<span style="font-weight:bold;color:gold;">tagBITMAPINFOHEADER</span><span style="font-weight:bold;color:#b4b4b4;">{</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">DWORD</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biSize</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//本结构所占字节数</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">LONG</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biWidth</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图的宽度，以像素为单位</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">LONG</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biHeight</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图的高度，以像素为单位</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">WORD</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biPlanes</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//目标设备的级别，必须为1</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">WORD</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biBitCount</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//每个像素所需的位数，1：双色，4：16色，8：256色，24：真彩色</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">DWORD</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biCompression</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图压缩类型，0：不压缩，1：B1_RLE8压缩类型，2：B1_RLE4压缩类型</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">DWORD</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biSizeImage</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图的大小</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">LONG</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biXPelsPerMeter</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图的水平分辨率，以像素/米为单位</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">LONG</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biYPelsPerMeter</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图的垂直分辨率，以像素/米为单位</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">DWORD</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biClrUsed</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图实际使用的颜色表中的颜色数</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">DWORD</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">biClrImportant</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图显示过程中重要的颜色数</span>
<span style="font-weight:bold;color:#b4b4b4;">}</span>&nbsp;<span style="font-weight:bold;color:gold;">BITMAPINFOHEADER；</span></pre>

#### 3. 颜色表

8位位图文件中，每个像素占8位，可表示256种颜色，8位位图文件含有颜色表，而24位真彩色位图文件中不含颜色表。颜色表用于说明位图中的颜色，它有若干个表项，每个表项是一个RGBQUAD类型的结构，定义一种颜色。

<pre style="font-family:Inziu Iosevka SC;font-size:17;color:gainsboro;background:#1e1e1e;"><span style="font-weight:bold;color:#569cd6;">typedef</span>&nbsp;<span style="font-weight:bold;color:#569cd6;">struct</span>&nbsp;<span style="font-weight:bold;color:gold;">tagRGBQUAD</span>&nbsp;<span style="font-weight:bold;color:#b4b4b4;">{</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">BYTE</span>&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">rgbBlue</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//蓝色的亮度（值范围&nbsp;0~255）</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">BYTE</span>&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">rgbGreen</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//绿色的亮度（值范围&nbsp;0~255）</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">BYTE</span>&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">rgbRed</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//红色的亮度（值范围&nbsp;0~255）</span>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">BYTE</span>&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">rgbReserved</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//保留，必须为0</span>
<span style="font-weight:bold;color:#b4b4b4;">}</span>&nbsp;<span style="font-weight:bold;color:gold;">RGBQUAD</span><span style="font-weight:bold;color:#b4b4b4;">;</span></pre>

#### 4. 位图信息

位图信息头和颜色表组成位图信息 BITMAPINFO

<pre style="font-family:Inziu Iosevka SC;font-size:17;color:gainsboro;background:#1e1e1e;"><span style="font-weight:bold;color:#569cd6;">typedef</span>&nbsp;<span style="font-weight:bold;color:#569cd6;">struct</span>&nbsp;<span style="font-weight:bold;color:gold;">tagBITMAPINFO</span>&nbsp;<span style="font-weight:bold;color:#b4b4b4;">{</span>
&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">BITMAPINFOHEADER</span>&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">bmiHeader</span><span style="font-weight:bold;color:#b4b4b4;">;</span>&nbsp;<span style="color:#57a64a;">//位图信息头</span>
&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:gold;">RGBQUAD</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="font-weight:bold;color:darkkhaki;">bmiColors</span><span style="font-weight:bold;color:#b4b4b4;">[</span><span style="font-weight:bold;color:#b5cea8;">1</span><span style="font-weight:bold;color:#b4b4b4;">];</span>&nbsp;<span style="color:#57a64a;">//颜色表</span>
<span style="font-weight:bold;color:#b4b4b4;">}</span>&nbsp;<span style="font-weight:bold;color:gold;">BITMAPINFO</span><span style="font-weight:bold;color:#b4b4b4;">;</span></pre>

#### 5. 位图数据

位图数据记录了位图的每一个像素值，记录顺序是在扫描行内是从左到右,扫描行之间是从下到上。位图的一个像素值所占的字节数:

当biBitCount=1时，8个像素占1个字节;

当biBitCount=4时，2个像素占1个字节;

当biBitCount=8时，1个像素占1个字节;

当biBitCount=24时，1个像素占3个字节;

##### 4字节对齐问题
Windows规定一个扫描行所占的字节数必须是4的倍数(即以long为单位)，不足的以0填充。

### 其他文件格式

#### 1. TIFF文件

TIFF（Tag Image File Format，标签图像文件格式）文件最初由Aldus公司与微软公司为PostScript打印联合开发。

TIFF最初的设计目的是为了1980年代中期桌面扫描仪厂商达成一个公用的扫描图像文件格式，而不是每个厂商使用自己专有的格式。在刚开始的时候，TIFF只是一个二值图像格式，随着扫描仪的功能越来越强大，并且桌面计算机的磁盘空间越来越大，TIFF逐渐开始支持灰阶图像和彩色图像。

#### 2. GIF文件

GIF（Graphics Interchange Format，图像交换格式），由美国CompuServe公司在1987年提出，最初的目的是希望每个BBS使用者能够通过GIF图像文件轻易存储并交换图像数据。

GIF采用了一种改进LZW压缩算法，通常称之为GIF-LZW压缩算法，是一种无损的压缩算法，压缩效率比较高，并且GIF支持在一幅GIF文件中存放多幅彩色图像，并且可以按照一定的顺序和时间间隔将多幅图像依次读出并显示在屏幕上，形成一种简单的动画效果。

GIF支持24位色彩，即最多可表示256种颜色，图像大小最多是64K*64K像素。

#### 3. JPEG文件

JPEG（Joint Photographic Experts Group，联合图像专家组）是由一个软件开发联合会组织制定，是一种有损压缩格式。JPEG的压缩技术十分先进，它用有损压缩的方式去除冗余数据，在获得极高压缩率的同时能展现十分丰富生动的图像，是目前最常用的图像文件格式。

#### 4. RAW文件

RAW文件格式是CMOS或者CCD图像感应器将捕捉到的光源信号转换为数字信号的原始数据。RAW文件是一种记录了数码相机传感器的原始信息，同时记录了由相机拍摄所产生的一些元数据（Metadata，如ISO的设置、快门速度、光圈值、白平衡等）的文件。RAW是未经处理、也未经压缩的格式，可以把RAW概念化为“原始图像编码数据”或更形象的称为“数字底片”。

## Visual C++处理数字图像的基本方法

### 使用DIB处理数字图像

DIB位图可以在不同的机器或系统中显示位图所固有的图像。与设备相关位图DDB相比，DIB是一种外部的位图格式，经常存储为以BMP为后缀的位图文件（有时也以DIB为后缀）。DIB位图还支持图像数据的压缩。

以下为Win32 SDK支持的一些重要的DIB操作函数：

函数 | 功能描述
---|---
GetDIBits | 从DDB中获取位图的图像位，用于将DDB转换为DIB格式
SetDIBits | 设置DIB位图的图像位，用于将DIB转换为DDB格式
CreateDIBitmap | 用指定的DIB来创建DDB，并用DIB信息初始化位图的图像位
SetDIBitmap | 直接将DIB位图的图像位输出到设备，用于显示DIB
StretchDIBits | 将DIB位图映射输出到设备的一个矩形区域，位图可能被缩放
CreateDIBPatternBrush | 用DIB位图来创建模式画刷
CreateDIBSection | 创建一个可直接写入的DIB
GetDIBColorTable | 获取DIB颜色表
SetDIBColorTable | 设置DIB颜色表

相关函数说明：<PAGE-23>

### 使用自定义类CDib处理数字图像

FrozenSky's CDib_Ex

https://github.com/FrozenSky7124/Project_MSVS/tree/master/C++/FrozenSky/DLL_CDib

