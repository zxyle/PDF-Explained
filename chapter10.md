# 第10章 - PDF软件和文档

在本章中，我们列出并描述了使用PDF文件查看，转换，编辑和编程的软件。
我们考虑开源软件和由Adobe或操作系统制造商提供的零成本商业软件。
第三方有各种各样的商业软件，我们在这里不讨论。

我们还列出了进一步文档和信息的来源。

## PDF查看器
PDF查看器的工作是：

* 显示文档的图形和文本内容。
* 允许用户使用书签和超链接与文档交互。
* 启用文本内容搜索，并通过剪切和粘贴提取文本。

并非每个查看器都拥有所有这些功能。由于PDF格式的巨大复杂性及其封装的格式（例如，字体和图像），
性能可能会有很大差异 - 尤其是使用更现代PDF功能的文件。


### Adobe阅读器
Adobe Reader是Adobe自己的免费PDF查看器，
也是唯一一个支持Adobe为PDF制作的各种专有扩展（例如，更现代的表单和注释）。
它带有适用于常见Web浏览器的PDF插件，适用于Microsoft Windows，Mac OS X，
Linux，Solaris和Android。它允许填写表格并以电子方式提交。

Adobe Reader可以在[Adobe网站](http://get.adobe.com/reader/)上找到。

### 预览
许多Mac OS X用户更喜欢随操作系统提供的快速，简单的PDF查看器预览。
它的启动速度更快，使用起来比Adobe Reader更流畅，对搜索和提取文本有很好的支持。
当PDF查看器作为插件加载到Web浏览器窗口中时，快速启动尤为重要。通常，还会在预览
不支持文件的情况下安装Acrobat Reader（例如，带有JavaScript的可填写表单用于纳税申报）。

此外，预览具有有限（但不断增加）的编辑功能，如第119页的“在Mac OS X上使用预览编辑”中所述。

### Xpdf
Xpdf是一个小型，快速，开源的PDF查看器，几乎可在任何类似Unix的计算机上运行，
其中X Window系统可用。对高级PDF设施的支持是有限的，但它是一个高度可靠的程序，
用于其功能范围内的文件。

Xpdf可以在[Foo Labs网站](http://foolabs.com/xpdf)上找到。

### GSview 
GSview是适用于Microsoft Windows和Unix的开源PDF和PostScript查看器。
它基于古老而高度可靠的GhostScript PDF和PostScript解释器。

GSview和GhostScript（GSview需要）可以从[GhostScript网站](http://pages.cs.wisc.edu/~ghost/)下载。


## 软件库
Adobe提供了自己的昂贵的，商业许可的PDF操作库，基于与Acrobat本身相同的代码。
在本节中，我们考虑流行的开源替代方案。

通常，构建库来编写PDF文件比阅读它们要容易得多。
要编写文件，只需要理解特定应用程序所需的小的PDF子集（即，一种压缩机制，一种字体类型等），
并且不需要复杂的解析机制。要读取文件，必须实现整个标准。

### 适用于Java和C＃的iText
iText是一个成熟的开源库，用于读取和编写PDF文档，以及使用段落，列表，表格和图像等高级构建块进行文本报告。
它还支持构建书签，超链接，注释和JavaScript操作。可以构建可填写表单，并支持加密文件。

iText可以从[iText Software网站](http://itextpdf.com/)下载。

### 适用于PHP的TCPDF
TCPDF是用于生成PDF报告的纯PHP库，包括文本布局，表格，HTML转换，注释，超链接和图像。
Web服务可以使用TCPDF动态构建文档，并将其提供给在Web浏览器中运行的PDF查看器，或通过电子邮件发送。

可以下载TCPDF以及[其网站](http://www.tcpdf.org/)上的各种示例。

### 使用Perl处理PDF
在Perl中有大量用于读取，编写和编辑PDF文件的PDF库，其中一些非常成熟，另一些则不那么成熟。
文档通常很少，与可用的广泛功能相悖。

与所有免费的Perl模块一样，[Comprehensive Perl Archive Network](http://www.cpan.org/)包含源代码和文档。


### Mac OS X上的PDF
Apple的PDFKit提供了许多用于Apple支持的编程语言（如Objective C）的类。这些包括：

* PDFView，PDF文档的屏幕视图。
* PDFDocument和PDFPage用于文档和页面级操作。
* 交互式设施的PDFAnnotation，PDFAction，PDFOutline和PDFSelection。

Apple的内置PDF查看器Preview基于这些库构建。PDF套件库记录在Apple的[Mac OS X Developer Library](http://developer.apple.com/)中。


## 格式转换
格式转换分为三类：

* 转换为类似的可缩放矢量格式（例如，PostScript或SVG）。在这种情况下，结构信息通常保存得很好。
* 从PDF转换为光栅图像，例如PNG或TIFF。
* 从光栅图像转换为PDF，这通常只涉及简单的封装，特别是在PDF格式的情况下，如JPEG。


### PDF to PostScript and Back Again
随GhostScript一起提供的*pdf2ps*和*ps2pdf*命令行程序可以在PDF和PostScript之间进行转换。
有时这涉及相当复杂和缓慢的处理，这可能导致更大的文件大小或一些构造的丢失（例如，文本被转换为轮廓）。
毕竟，PDF和PostScript是完全不同的 - 尽管有共同的遗产。

*ps2pdf*和*pdf2ps*可从[GhostScript主页](http://pages.cs.wisc.edu/~ghost/)获得。


### 将PDF格式化为图像
GhostScript附带的gs程序可用于以给定的分辨率将PDF页面渲染为光栅图像，适合打印或屏幕使用。
这是GSView用于显示PDF页面的工具。
这是通过指定对应于图像文件格式的几个特殊输出设备之一来实现的，例如PNG和TIFF。

gs是GhostScript系统的一部分，可从[GhostScript主页](http://pages.cs.wisc.edu/~ghost/)获得。


### Printing Files to PDF
大多数现代文字处理器都具有以PDF格式导出，维护超链接和为目录构建书签的功能。
但是，通常需要从程序中生成PDF输出，这些程序无法将其原始格式转换为PDF。
这可以通过使用打印机驱动程序来实现，该驱动程序将PDF写入文件而不是打印它。

Mac OS X通过打印对话框中的“另存为PDF”选项本机提供此工具。

在Unix平台上，此工具由CUPS打印系统的开源[CUPS-PDF后端](http://cups-pdf.de/)提供。

在Microsoft Windows上，开源[PDFCreator打印机驱动程序](http://sourceforge.net/projects/pdfcreator/)可以完成相同的工作。它在内部使用GhostScript。


## PDF编辑器
PDF最初并不打算进行大量编辑，而是作为一种可扩展的，结构化的发布终端格式。
因此，大多数编辑软件具有受限和特定的编辑功能，例如合并文件，添加注释，
填写表格或对页面内容进行小的编辑。

在[第9章](chapter9.md)中，我们研究了*pdftk*，这是一个用于PDF文件命令行操作的开源程序。
在本节中，我们列出了编辑现有PDF文件的其他方法。


### Adobe Acrobat
Adobe自己的PDF编辑器Acrobat（花费数百美元）具有广泛的功能，超越了免费的Adobe Reader。这包括：

* 打印到PDF，从PostScript转换为PDF。
* 与Microsoft Word和Excel之间的转换。
* 光学字符识别（OCR），生成与扫描文档完全相同但具有可搜索，可编辑文本的PDF文件。
* 重新排序，旋转和编辑页面和内容。
* 印前检查和印刷出版工具。
* 构建PDF表单。
* 创建和验证PDF/A和PDF/X.
* 添加加密和数字签名。

Adobe Acrobat有许多商业第三方插件，提供额外的功能。

### 在Mac OS X使用预览编辑PDF
预览，Mac OS X上的标准PDF查看程序，也有编辑设备，由于它们在界面中不突出，
因此往往未得到充分利用。

预览可以注释PDF文档，突出显示和浏览文本，裁剪页面，添加文本，添加超链接，
删除和重新排列页面以及合并PDF。

预览处理各种文档，并设法在编辑文件的其他方面时保留它不理解的功能。


## PDF和图形文档
编写本书是为了填补PDF文献中的一个显着差距。在这里，我们列出了其他信息和文档来源。

### ISO 32000 and the PDF文件格式
PDF参考手册作为一本书出版，直到PDF版本1.6。现在，唉（但也许是恰当的，考虑到它的主题），它只能作为PDF文档提供。

PDF版本1.7于2008年被批准为ISO标准（标准号ISO 32000-1:2008）。
ISO收取近500美元的PDF副本（通过下载或CD-ROM）。幸运的是，Adobe继续以电子方
式提供PDF版本1.7参考。这是ISO 32000-1:2008的核准副本。特别是，章节，章节和小节编号是相同的。

最近对PDF 1.7的Adobe扩展记录在ExtensionLevel文档中，这些文档不构成ISO标准的一部分，但预计会成为更新版本的一部分。

Adobe的ISO 32000-1:2008副本和ExtensionLevel文档都可以从[Adobe Developer Connection网站](http://www.adobe.com/devnet/pdf/pdf_reference.html)下载。


### PDF Hacks
O'Reilly的另一个PDF标题，Sid Steward的[PDF Hacks](http://oreilly.com/catalog/9780596006556)，强调了解决各种PDF问题的实用解决方案。它包括100个单独的黑客：

* 自定义PDF查看器，使阅读PDF更加舒适。
* 将“巨大的PDF文件”“重写”为更小的文件。
* 在许多平台上使用各种工具创建PDF文件。
* 从gVim文本编辑器编辑PDF文本。
* 使用熟悉的软件创建具有高级导航功能的PDF。
* 使用复杂的导航和交互功能构建PDF。
* 即时生成PDF。
* 将PDF文件与超出简单超链接的网站集成。
* 使用PDF表格在网站上收集数据。
* 索引和比较PDF文件。
* 将传入的传真转换为PDF。
* 编写控制Adobe Acrobat的脚本。

### 相关话题
PDF标准和本书参考了计算机图形学的一般领域（有时还假设了解）。
这些主题的标准参考是计算机图形学原理和实践（Foley等，Addison-Wesley 1990）。
本书包含了Bézier曲线，透明度，仿射变换以及理解如何编写PDF图形流所需的其他主题的所有背景知识。

理解PDF中的词典，树和其他数据结构及其选择原因的一个很好的参考是算法（Cormen等，MIT Press，1990）。
关于算法的任何类似的书都应该足够了。

### 论坛和讨论
有许多地方可以讨论技术PDF主题：

* [Planet PDF论坛](http://forum.planetpdf.com/)是各种技术和非技术PDF讨论的热门场所。
* Adobe的[Adobe Reader论坛](http://forums.adobe.com/community/adobe_reader_forums/adobe_reader)，用于Adobe Reader的技术支持和讨论。
* **comp.text.pdf** usenet新闻组是一个低流量的地方，可以进行更多的技术讨论。

### Adobe的网站资源
对于那些对PDF技术方面感兴趣的人，Adobe网站有两个相关部分：

* [PDF技术中心](http://www.adobe.com/devnet/pdf.html)包含PDF参考文档。
* [Acrobat开发者中心](http://www.adobe.com/devnet/acrobat.html)拥有用于编写Acrobat插件，FDF表单格式和开发人员知识库的资源和文档。

[目录](./README.md)&nbsp;|[上一章：使用pdftk](./chapter9.md)|[下一章：译者注](./Note.md)