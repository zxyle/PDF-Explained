# 第7章 - 文档元数据和导航

在本章中，我们将讨论与PDF文档的视觉外
观无关的四个主题，但讨论辅助数据，
这些辅助数据也可用于交互式，屏幕上
文档的使用，以及用于携带文档以供使用的
额外信息的元数据 通过PDF工作流程中的程序。

Destinations
* 定义文件中位置的数据结构。它们可用于指定书签或超链接指向的位置。书签（适当地称为文档外联）用作文档的目录。

XML元数据
* 包含指定格式的XML文件的流，包含与文档信息字典相同的元数据，以及其他字段。

文件附件
* 允许将整个文件封装在文档中，就像电子邮件附件一样。

注释
* 允许文本和图形应用于PDF页面的顶部，与主页面内容分开，以供屏幕阅读器显示。
一种特殊的注释是超链接，它允许用户单击页面上的某个位置并重定向到文件中其他位置的目标。


## 书签和Destinations
文档的书签（适当地称为文档大纲）是条目树（通常是章节，章节，段落等的标题），
可以在PDF查看器中单击以在文档中移动。每个条目都有一些文本和目的地描述它链接到的位置。


### Destinations
目的地在PDF文件中定义一个位置，包括页码，该页面内的位置以及查看该页面时使用的
放大率。目的地可以明确定义（我们将为简单起见）或由名称引用，并在列出所有目的
地的文档范围名称树中查找。书签通常与PDF查看器中的文档一起显示。

目标是使用数组对象定义的，内容取决于目标的类型。目标语法总结在表7-1中。

|数组|描述|
|---|---|
|[page /Fit]|以水平和垂直方向适合窗口中整个页面的比例显示页面|
|[page /FitH top]|显示窗口顶部边缘垂直坐标顶部的页面，并设置放大倍率以水平放置文档|
|[page /FitV left]|显示窗口左边缘水平坐标的页面，并设置放大倍率以垂直放置文档|
|[page /XYZ left top zoom]|在窗口的左上角显示（左，上）页面，并通过因子缩放放大页面。任何参数的空值表示没有变化|
|[page /FitR left bottom right top] |显示缩放的页面以显示由left，bottom，right和top指定的矩形|
|[page /FitB]|显示像/Fit这样的页面，但使用页面内容的边界框，而不是裁剪框|
|[page /FitBH top] |显示像/FitH这样的页面，但使用页面内容的边界框，而不是裁剪框|
|[page /FitBV left]|显示像/FitV这样的页面，但使用页面内容的边界框，而不是裁剪框|

### The Document Outline(Bookmarks)
文档大纲由大纲条目树和大量项目词典定义的大纲条目树组成。大纲字典由文档目录中的/Outlines条目指向。条目的子条目（子）可以默认显示（打开）或默认隐藏，仅通过单击（关闭）显示。大纲字典总结在表7-2和7-3中。

|键|值类型|值|
|---|---|---|
|/Type |name|如果存在，必须是/Outlines|
|/First|间接引用字典|文档大纲中第一个顶级项的大纲项字典。如果存在任何文档大纲条目，则必需|
|/Last |间接引用字典 |文档大纲中最后一个顶级项的大纲项字典。如果存在任何文档大纲条目，则必需|
|/Count| 整数|大纲所有部分中的开放大纲条目总数。如果没有打开的条目，可以省略|


### 建立一个例子
考虑一个有三页的文件。我们希望构建以下层次结构：
```
Part 1 (points to page one)
  Part 1A (points to page two) 
  Part 1B (points to page three)
```

结果代码如例7-1所示。对于第一，第二和第三页，本文档中的页面对象具有对象编号3,5和7。
对象12是文档目录。对象11是文档概要字典，对象8,9和10是文档概要项目字典。
```
Example 7-1. An example document outline
8 0 obj
<< /Parent 10 0 R /Title (Part endobj
9 0 obj
<< /Parent 10 0 R /Title (Part endobj
10 0 obj
<< /Parent 11 0 R /First 9 0 R endobj
11 0 obj
<< /First 10 0 R /Last 10 0 R endobj
12 0 obj
<< /Outlines 11 0 R /Pages 1 0
1B) /Dest [ 7 0 R /Fit ] /Prev 9 0 R >>
1A) /Dest [ 5 0 R /Fit ] /Next 8 0 R >>
/Dest [ 3 0 R /Fit ] /Title (Part 1) /Last 8 0 R >>
>>
R /Type /Catalog >>
```

Adobe Reader显示文档及其大纲，如图7-1所示。

![](./images/figure%207-1.png)


## XML 元数据

从PDF 1.4开始，元数据流可用于将XML元数据附加到整个文档或其中的各个元素。
文档级元数据流扩展并取代文档信息字典（为了与旧的PDF程序兼容，几乎总是包含该字典）。

元数据以未压缩和（通常）未加密的方式存储，并且以这样的方式使得不了解PDF
的外部工具可以容易地在PDF文件中找到它。

XML使用由可扩展元数据平台（XMP）定义的标记，该标准在Adobe的XMP：可扩展元数
据平台中进行了描述。该格式包括以与平台无关的方式以其他格式（例如，PDF）嵌入
元数据的方法，使得不能理解封闭格式的程序仍然可以提取XMP数据。有关XMP格
式的详细信息，请访问[Adobe网站](http://www.adobe.com/products/xmp/)。

例7-2中显示了示例XMP元数据。你可以从文档信息词典中看到一些熟悉的条目。
还要注意序列/Type /Metadata /Subtype /XML，它将此流标识为XMP元数据。通过使用文档目录中的/Metadata条目将元数据流添加到文档中。

```
Example 7-2. XML Metadata for the ISO PDF Format reference manual PDF. The ↵ symbol is used to indicate a line which continues without a carriage return. The ␣ symbol is used to represent a space character.

4884␣0␣obj<</Length␣3508/Type/Metadata/Subtype/XML>>stream <?xpacket␣begin='ï»¿'␣id='W5M0MpCehiHzreSzNTczkc9d'?> <?adobe-xap-filters␣esc="CRLF"?> <x:xmpmeta␣xmlns:x='adobe:ns:meta/'␣x:xmptk='XMP␣toolkit␣2.9.1-14,␣framework␣1.6'> <rdf:RDF␣xmlns:rdf='http://www.w3.org/1999/02/22-rdf-syntax-ns#'↵ xmlns:iX='http://ns.adobe.com/iX/1.0/'> <rdf:Description␣rdf:about='uuid:b8659d3a-369e-11d9-b951-000393c97fd8'↵ ␣xmlns:pdf='http://ns.adobe.com/pdf/1.3/'↵ ␣pdf:Producer='Acrobat␣Distiller␣6.0.1␣for␣Macintosh'>↵
</rdf:Description> <rdf:Description␣rdf:about='uuid:b8659d3a-369e-11d9-b951-000393c97fd8'↵ ␣xmlns:xap='http://ns.adobe.com/xap/1.0/'↵ ␣xap:CreateDate='2004-11-14T08:41:16Z'↵ ␣xap:ModifyDate='2004-11-14T16:38:50-08:00'↵ ␣xap:CreatorTool='FrameMaker␣7.0'↵ ␣xap:MetadataDate='2004-11-14T16:38:50-08:00'>↵
</rdf:Description> <rdf:Description␣rdf:about='uuid:b8659d3a-369e-11d9-b951-000393c97fd8'↵ ␣xmlns:xapMM='http://ns.adobe.com/xap/1.0/mm/'↵ ␣xapMM:DocumentID='uuid:919b9378-369c-11d9-a2b5-000393c97fd8'/> <rdf:Description␣rdf:about='uuid:b8659d3a-369e-11d9-b951-000393c97fd8'↵ ␣xmlns:dc='http://purl.org/dc/elements/1.1/'↵
␣dc:format='application/pdf'>↵
<dc:description><rdf:Alt>↵ <rdf:li␣xml:lang='x-default'>␣Adobe␣Portable␣Document␣Format␣(PDF)␣</rdf:li>↵ </rdf:Alt></dc:description>↵
<dc:creator>␣<rdf:Seq>␣<rdf:li>↵
  XML Metadata
| 93
Adobe␣Systems␣Incorporated␣</rdf:li>␣</rdf:Seq>␣</dc:creator>↵ <dc:title>␣<rdf:Alt>↵ <rdf:li␣xml:lang='x-default'>PDF␣Reference,␣version␣1.6␣</rdf:li>␣</rdf:Alt>↵ </dc:title></rdf:Description>↵
</rdf:RDF>
</x:xmpmeta> ␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣␣ (Many more lines of padding)
<?xpacket␣end='w'?>
endstream
endobj
```

## 注释和超链接
PDF中使用注释在页面内容本身之外添加注释或交互元素。每个查看器应用程序
（例如Adobe Reader或Mac OS X Preview）都可以以不同的方式显示这些注释，
甚至可以在软件版本之间进行更改，因此无法依赖精确的视觉效果。注释不会影响打印输出。

可以使用页面字典中的条目/Annots下的数组将一个或多个注释与每个页面相关联。
每个注释都是字典。表7-4中描述了更重要的条目。
每种类型的注释在此词典中都有其他类型。

|键|值类型|值|
|---|---|---|
|/Type |name |如果存在，必须是/Annot|
|/Subtype* |name  |此批注的类型|
|/Rect* |rectangle  |默认用户空间单位中注释的位置和大小|
|/Contents |text string |此注释的文本内容，或者如果没有，则是备用的人类可读描述|


我们将看两种注释：可用于添加注释的文本注释，以及用于在文档中创建超链接的链接注释。
绘制文档还有许多其他类型，突出显示文本和添加打印机标记。
在第96页的“文件附件”中，我们使用文件附件注释向各个页面添加附件。

首先是文本注释。这里，/Subtype是/Text。代码如例7-3所示。
我们将额外的注释字典条目/Open设置为true，以指示在打开文档时注释将是可见的。
使用/C条目将背景颜色设置为白色。

```
Example 7-3. A Text annotation
6 0 obj
<<
  /Subtype /Text
  /Open true
  /Contents (An example text annotation) /Type /Annot
  /Rect [400 100 500 200]
  /C [1 1 1] RGB (1, 1, 1) i.e., White
>>

/Annots [6 0 R] Extra entry in page dictionary
```

Adobe Reader中的结果如图7-2所示。请注意，Adobe Reader会忽略此处的/Rect条目 - 其他查看者可能会使用它。

![](./images/figure%207-2.png)

现在，让我们尝试链接注释，以构建从第一页到第三页的超链接。
链接注释具有子类型/Link和给定目标的/Dest条目（在第90页的“目标”中描述）。/Rect条目定义超链接的区域。

代码如例7-4所示。
```
Example 7-4. A link annotation
6 0 obj
<<
  /Subtype /Link
  /Dest [4 0 R /Fit] /Type /Annot
  /Rect [45 760 260 800]
>>

/Annots [6 0 R] Extra entry in page dictionary
```

Adobe Reader中的结果如图7-3所示。

![](./images/figure%207-3.png)

## 文件附件
附件是一种在PDF文档中包含一个或多个文件（任何类型）的方法。
文件可以作为整体附加到文档，也可以附加到单个页面。
通常，PDF查看器将显示任何附件的列表，允许用户打开或保存它们。
例如，可以使用此工具将示例资源与幻灯片演示文稿的PDF捆绑在一起。

嵌入文件本身只包含在流对象中，其中/Type /Embedded File作为流字典中的附加条目。
示例7-5中显示了示例嵌入文件的代码。

```
Example 7-5. An embedded file
8 0 obj
<< /Type /EmbeddedFile /Length 35 >> stream
This is a text file attachment...

endstream 
endobj
```

嵌入式文件流以两种完全不同的方式引用：一种用于整个文档的附件，另一种用于附加到特定页面。

要附加到整个文档，/EmbeddedFiles条目包含在文档目录中/Names条目引用的名称字典中。
代码如例7-6所示。

```
Example 7-6. PDF Code for an attachment at the document level. The embedded file is object 8 (see Example 7-5).
9 0 obj 
<< /Names
     << /EmbeddedFiles 
       << /Names
           [ (attachment.txt) << /EF << /F 8 0 R >> /F (attachment.txt) /Type /F >> ] >> 
     >>
   /Pages 1 0 R
   /Type /Catalog >> 
endobj
```

要附加到单个页面，将使用一种特殊类型的注释，通常在页面词典的/Annots字典中列出。
代码如例7-7所示。

```
例7-7. 特定页面附件的PDF代码。嵌入文件是对象8 (see Example 7-5).
9 0 obj 
<<
  /Type /Page

  (Other dictionary entries as usual)
  
/Annots
   [ << /FS << /EF << /F 8 0 R >> /F (attachment.txt) /Type /F >>
        /Subtype /FileAttachment
        /Contents (attachment.txt)
        /Rect [ 18 796.88976378 45 823.88976378 ]
   >> ]
>> 
endobj
```

Adobe Reader在侧栏中显示附件如图7-4所示。

![](./images/figure%207-4.png)


[目录](./README.md)&nbsp;|[上一章：文本和字体](./chapter6.md)|&nbsp;[下一章：加密文档](./chapter8.md)
