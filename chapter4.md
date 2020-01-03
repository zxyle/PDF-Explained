# 第4章 - 文档结构

在本章中，我们将PDF文件的位和字节留下，并考虑逻辑结构。
我们考虑trailer字典，文档目录和页面树。
我们枚举每个对象中的必需条目。
然后我们看看PDF文件中的两个常见结构：文本字符串和日期。

图4-1显示了典型文档的逻辑结构。

![](./images/figure%204-1.png)


## Trailer 字典
这个字典驻留在文件的trailer而不是文件的主体中，是程序想要读取PDF文档时要处理的第一件事。
它包含允许读取交叉引用表的条目，从而读取文件的对象。其重要条目总结在表4-1中。

|键|值类型|值|
|---|---|---|
|/Size* |整数|文件交叉引用表中的条目总数（通常等于文件中的对象数加1）|
|/Root* |间接引用字典 |文件目录|
|/Info |间接引用字典 |文档的文档信息字典|
|/ID |两个字符串的数组|唯一标识工作流程中的文件。第一个字符串在首次创建文件时确定，第二个字符串在工作流系统修改文件时进行修改|


这是一个示例trailer词典：

```
<<
   /Size 421
   /Root 377 0 R
   /Info 375 0 R
   /ID [<75ff22189ceac848dfa2afec93deee03> <057928614d9711db835e000d937095a2>]
>>
```

一旦处理了trailer字典，我们就可以继续阅读文档信息字典和文档目录。

## 文件信息字典
文档信息字典包含文件的创建日期和发明日期，以及一些简单的元数据（不要与“XML元数据”（第93页）中讨论的更全面的XMP元数据混淆）。

文档信息字典条目在表4-2中描述。典型的文档信息字典在例4-1中给出。

|键|值类型|值|
|---|---|---|
|/Title |文本字符串 |该文件的标题。请注意，这与第一页上显示的任何标题无关|
|/Subject |文本字符串|该文件的主题。同样，这只是元数据，没有关于内容的特定规则|
|/Keywords |文本字符串 |与此文档相关的关键字。没有给出关于如何构建这些的建议|
|/Author |文本字符串 |文件作者的姓名|
|/CreationDate|日期字符串 |文档创建的日期|
|/ModDate|日期字符串|上次修改文档的日期|
|/Creator|文本字符串|最初创建此文档的程序的名称，如果它以另一种格式（例如，“Microsoft Word”）启动|
|/Producer|文本字符串|将此文件转换为PDF的程序的名称，如果它以另一种格式（例如，字处理器的格式）启动|

```
Example 4-1. Typical document information dictionary
<<
   /ModDate (D:20060926213913+02'00') 
   /CreationDate (D:20060926213913+02'00')
   /Title (catalogueproduit-UK.qxd)
   /Creator (QuarkXPress: pictwpstops filter 1.0) 
   /Producer (Acrobat Distiller 6.0 for Macintosh) 
   /Author (James Smith)
>>
```

日期字符串格式（对于/CreationDate和/ModDate）将在第45页的“日期”一节中讨论。
文本字符串格式（描述如何在字符串类型中使用不同的编码）在“文本字符串”中描述 第45页。

## 文件目录
文档目录是主对象图的根对象，可以通过间接引用从中到达所有其他对象。
在表4-3中，我们列出了所需的文档目录字典条目，以及许多可选的文档目录字典条目，
以便介绍我们未在这些页面的其他地方介绍的简要PDF主题。

|键|值类型|值|
|---|---|---|
|/Type* |name|必须是/Catalog|
|/Pages* |间接引用字典 |页面树的根节点。页面树在第42页的“页面和页面树”中讨论|
|/PageLabels |number tree|一个数字树，给出了该文档的页面标签。这种机制允许文档中的页面具有比1,2,3更复杂的编号....例如，书籍的前言可以编号为i，ii，iii ......，而主要内容 再次以1,2,3开始....这些页面标签显示在PDF查看器中 - 它们与打印输出无关|
|/Names |dictionary|名字词典。它包含各种名称树，它们将名称映射到实体，以防止必须使用对象编号直接引用它们|
|/Dests|dictionary|将名称映射到目标的字典。目的地是超链接向用户发送的PDF文档中的位置的描述|
|/ViewerPreferences|dictionary|一个查看器首选项字典，允许标志指定在屏幕上查看文档时的PDF查看器的行为，例如打开文档的页面，初始查看比例等|
|/PageLayout|name|指定PDF查看器要使用的页面布局。值为/SinglePage，/OneColumn，/TwoColumnLeft，/TwoColumnRight，/TwoPageLeft，/TwoPageRight。（默认值：/SinglePage）。详情见ISO 32000-1:2008的表28|
|/PageMode|name|指定PDF查看器要使用的页面模式。值为/UseNone，/UseOutlines，/UseThumbs，/FullScreen，/UseOC，/UseAttachments。 （默认值：/UseNone）。详情见ISO 32000-1:2008的表28|
|/Outlines|间接引用字典|大纲字典是文档大纲的根，通常称为书签|
|/Metadata|间接引用流|文档的XMP元数据 - 请参阅第93页的“XML元数据”|

## 页面和页面树
PDF文档中的页面字典汇集了使用这些指令使用的资源（字体，图像和其他外部数据）绘制图形和文本内容（我们在第5章和第6章中考虑）的说明。
它还包括页面大小，以及定义裁剪等的许多其他框。

表4-4总结了页面字典中的条目。

|键|值类型|值|
|---|---|---|
|/Type* |name|必须是/Pages|
|/Parent* |间接引用字典|页面树中此节点的父节点|
|/Resources|dictionary|页面的资源（字体，图像等）。如果完全省略此条目，则资源将从页面树中的父节点继承。如果确实没有资源，请包含此条目但使用空字典|
|/Contents|indirect reference to stream or array of such references|一个或多个部分中页面的图形内容。如果缺少此条目，则页面为空|
|/Rotate |整数|页面的查看旋转，以度为单位，从北向顺时针。值必须是90的倍数。默认值：0。这适用于查看和打印。如果缺少此条目，则其值将从页面树中的父节点继承|
|/MediaBox* |rectangle |页面的媒体框（媒体大小，即纸张）。对于大多数用途，页面大小。如果缺少此条目，则它将从页面树中的父节点继承|
|/CropBox |rectangle|页面的裁剪框。这定义了在显示或打印页面时默认可见的页面区域。如果不存在，则将其值定义为与媒体框相同|

媒体盒和其他框的矩形数据结构是四个数字的数组。这些定义了矩形的对角相对的角 - 数组的前两个元素是一个角的x和y坐标，后两个元素是另一个角的x和y坐标。
通常，给出左下角和右上角。所以，例如：
```
/MediaBox [0 0 500 800] 
/CropBox [100 100 400 700]
```

定义一个500 x 800点的页面，裁剪框在页面的每一侧删除100个点。

页面使用页面树而不是简单的数组链接在一起。这种树结构使得在具有数百或数千页的文档中查找给定页面变得更快。
好的PDF应用程序构建了一个平衡树（一个节点数量最小的树）。这可确保快速定位特定页面。没有子节点的节点就是页面本身。
图4-2显示了七页的示例页面树结构。

![](./images/figure%204-2.png)

这将用PDF对象编写，如例4-2所示。表4-5中总结了中间或根页面树节点中的条目（即，不是页面本身）。


```
1 0 obj Root node
<< /Type /Pages /Kids [2 0 R 3 0 R 4 0 R] /Count 7 >>
endobj
2 0 obj Intermediate node
<< /Type /Pages /Kids [5 0 R 6 0 R 7 0 R] /Parent 1 0 R /Count 3 >> endobj
3 0 obj Intermediate node
<< /Type /Pages /Kids [8 0 R 9 0 R 10 0 R] /Parent 1 0 R /Count 3 >> endobj
4 0 obj Page 7
<< /Type /Page /Parent 1 0 R /MediaBox [0 0 500 500] /Resources << >> >> endobj
5 0 obj Page 1
<< /Type /Page /Parent 2 0 R /MediaBox [0 0 500 500] /Resources << >> >> endobj
6 0 obj Page 2
<< /Type /Page /Parent 2 0 R /MediaBox [0 0 500 500] /Resources << >> >> endobj
7 0 obj Page 3
<< /Type /Page /Parent 2 0 R /MediaBox [0 0 500 500] /Resources << >> >> endobj
8 0 obj Page 4
<< /Type /Page /Parent 3 0 R /MediaBox [0 0 500 500] /Resources << >> >> endobj
9 0 obj Page 5
<< /Type /Page /Parent 3 0 R /MediaBox [0 0 500 500] /Resources << >> >> endobj
10 0 obj Page 6
<< /Type /Page /Parent 3 0 R /MediaBox [0 0 500 500] /Resources << >> >> endobj
```

|键|值类型|值|
|---|---|---|
|/Type*|name|必须是/Pages|
|/Kids*|间接引用数组|此节点的直接子页面树节点|
|/Count*|整数|页节点（不是其他页面树节点）的数量，它们是此节点的最终子节点|
|/Parent|间接引用页面树节点|引用此节点的父节点（此节点是其子节点）。如果不是页面树的根节点，则必须存在|

在此树中，任何页面最多可以找到两个远离根节点的间接引用。

## 文本字符串
页面的实际文本内容之外的字符串（例如，书签名称，文档信息等）被称为文本字符串。
它们使用PDFDocEn编码或（在最近的文档中）Unicode编码。PDFDocEncoding基于ISO Latin-1编码。
它完全记录在ISO标准32000-1:2008的附录D中。

编码为Unicode的文本字符串通过查看前两个字节来区分：这些字符将是254后跟255.这是Unicode字节顺序标记U + FEFF，表示UTF16BE编码。
这意味着PDFDocEncoding字符串不能以þ（254）后跟ÿ（255）开头，但这在任何合理的情况下都不太可能发生。

## 日期
文档信息字典中的创建和修改日期/CreationDate和/ModDate是PDF日期格式的示例，
对字符串中的日期进行编码，包括有关时区的信息。

日期字符串的格式为： `(D:YYYYMMDDHHmmSSOHH'mm')`

其中括号表示通常的字符串。该日期的其他部分在表4-6中进行了总结。

|Portion |含义|
|---|---|
|YYYY|年份，有四位数，例如2008年|
|MM|月份，从01到12的两位数|
|DD|天数，从01到31的两位数|
|HH|小时，从00到23的两位数|
|mm|分钟，从00到59两位数|
|SS|秒钟，从00到59两位数|
|O|本地时间与世界时的关系，+，- 或Z. +表示本地时间晚于UT， - 更早，Z等于世界时|
|HH'|世界时的偏差绝对值，以小时为单位，以00到23的两位数表示 |
|mm'|通用时间偏移的绝对值，以分钟为单位，从00到59两位数|

一年之后的所有日期都是可选的。例如，（D：1999）完全有效。但是，很明显，如果省略一个部分，
则必须省略后面的所有内容，否则结果将是模糊的。DD和MM的默认值为01，对于所有其他部分，默认值为零。

例如：`(D:20060926213913+02'00')`

代表2006年9月26日下午9:39:13，比世界时间早两个小时的时区。

## 把它放在一起
这是一个手动创建的文本，由*pdftk*使用第2章介绍的方法处理成有效的PDF文件。
它是一个三页文档，包含文档信息字典和页面树。
图4-3显示了Acrobat Reader中显示的此文档。图4-4是相应的对象图。

```
%PDF-1.0 文件头
1 0 obj Top-level of page tree: has two children—page one and an intermediate page tree node 
<< /Kids [2 0 R 3 0 R] /Type /Pages /Count 3 >>
endobj
4 0 obj Contents stream for page one
<< >>
stream
1. 0.000000 0.000000 1. 50. 770. cm BT /F0 36. Tf (Page One) Tj ET
endstream
endobj
2 0 obj Page one
<<
   /Rotate 0 
   /Parent 1 0 R 
   /Resources
     << /Font << /F0 << /BaseFont /Times-Italic /Subtype /Type1 /Type /Font >> >> >> 
   /MediaBox [0.000000 0.000000 595.275590551 841.88976378]
   /Type /Page
   /Contents [4 0 R]
>>
endobj
5 0 obj Document catalog
<< /PageLayout /TwoColumnLeft /Pages 1 0 R /Type /Catalog >> endobj
6 0 obj Page three
<<
  /Rotate 0 
  /Parent 3 0 R 
  /Resources
    << /Font << /F0 << /BaseFont /Times-Italic /Subtype /Type1 /Type /Font >> >> >> 
  /MediaBox [0.000000 0.000000 595.275590551 841.88976378]
  /Type /Page
  /Contents [7 0 R] 
>>
endobj
3 0 obj Intermediate page tree node, linking to pages two and three
<< /Parent 1 0 R /Kids [8 0 R 6 0 R] /Count 2 /Type /Pages >> 
endobj
8 0 obj Page two
<<
  /Rotate 270 
  /Parent 3 0 R 
  /Resources
     << /Font << /F0 << /BaseFont /Times-Italic /Subtype /Type1 /Type /Font >> >> >> 
  /MediaBox [0.000000 0.000000 595.275590551 841.88976378]
  /Type /Page
  /Contents [9 0 R]
>>
endobj
9 0 obj Content stream for page two
<< >>
stream
q 1. 0.000000 0.000000 1. 50. 770. cm BT /F0 36. Tf (Page Two) Tj ET Q
1. 0.000000 0.000000 1. 50. 750 cm BT /F0 16 Tf ((Rotated by 270 degrees)) Tj ET 
endstream
endobj
7 0 obj Content stream for page three
<< >>
stream
1. 0.000000 0.000000 1. 50. 770. cm BT /F0 36. Tf (Page Three) Tj ET
endstream
endobj
10 0 obj Document information dictionary
<<
   /Title (PDF Explained Example) 
   /Author (John Whitington) 
   /Producer (Manually Created) 
   /ModDate (D:20110313002346Z) 
   /CreationDate (D:2011)
>>
endobj xref
0 11
trailer Trailer dictionary 
<<
  /Info 10 0 R
  /Root 5 0 R
  /Size 11
  /ID [<75ff22189ceac848dfa2afec93deee03> <057928614d9711db835e000d937095a2>]
>> 
startxref 
0
%%EOF
```

![](./images/figure%204-3.png)

![](./images/figure%204-4.png)


[目录](./README.md)&nbsp;|[上一章：文件结构](./chapter3.md)|&nbsp;[下一章：图形](./chapter5.md)
