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

|Key |Value type|Value|
|---|---|---|
|/Size* |Integer|文件交叉引用表中的条目总数（通常等于文件中的对象数加1）|
|/Root* |Indirect reference to dictionary |文件目录|
|/Info |Indirect reference to dictionary |文档的文档信息字典|
|/ID |Array of two Strings|唯一标识工作流程中的文件。第一个字符串在首次创建文件时确定，第二个字符串在工作流系统修改文件时进行修改|


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

|Key |Value type|Value|
|---|---|---|
|/Title |text string |The document’s title. Note that this is nothing to do with any title displayed on the first page.|
|/Subject |text string|The subject of the document. Again, this is just metadata with no particular rules about content.|
|/Keywords |text string |Keywords associated with this document. No advice is given as to how to structure these. |
|/Author |text string |The name of the author of the document.|
|/CreationDate |date string|The date the document was created.|
|/ModDate|date string|The date the document was last modified.|
|/Creator|text string|The name of the program which originally created this document, if it started as another format (for example, “Microsoft Word”).|
|/Producer|text string|The name of the program which converted this file to PDF, if it started as another format (for example, the format of a word processor).|

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

|Key |Value type|Value|
|---|---|---|
|/Type* |name|Must be /Catalog.|
|/Pages* |indirect reference to dictionary |The root node of the page tree. Page trees are discussed in“Pages and Page Trees” on page 42.|
|/PageLabels |number tree|A number tree giving the page labels for this document. This mechanism allows for pages in a document to have more com- plicated numbering than just 1,2,3.... For example, the preface of a book may be numbered i,ii,iii..., whilst the main content starts again at 1,2,3....These page labels are displayed in PDF viewers—they have nothing to do with printed output.|
|/Names |dictionary|The name dictionary. This contains various name trees, which map names to entities, to prevent having to use object numbers to reference them directly.|
|/Dests|dictionary|A dictionary mapping names to destinations. A destination is a description of a place within a PDF document to which a hyper- link sends the user.|
|/ViewerPreferences|dictionary|A viewer preferences dictionary, which allows flags to specify the behavior of a PDF viewer when the document is viewed on screen, such as the page it is opened on, the initial viewing scale and so on.|
|/PageLayout|name|Specifies the page layout to be used by PDF viewers. Values are /SinglePage, /OneColumn, /TwoColumnLeft, /TwoColumnRight, /TwoPageLeft, /TwoPageRight. (Default: /SinglePage). Details are in Table 28 of ISO 32000-1:2008.|
|/PageMode|name|Specifies the page mode to be used by PDF viewers. Values are /UseNone, /UseOutlines, /UseThumbs, /FullScreen, /UseOC, /UseAttachments. (Default: /UseNone). Details are in Table 28 of ISO 32000-1:2008.|
|/Outlines|indirect reference to dictionary|The outline dictionary is the root of the document outline, commonly known as the bookmarks.|
|/Metadata|indirect reference to stream|The document’s XMP metadata—see “XML Meta- data” on page 93.|

## 页面和页面树
PDF文档中的页面字典汇集了使用这些指令使用的资源（字体，图像和其他外部数据）绘制图形和文本内容（我们在第5章和第6章中考虑）的说明。
它还包括页面大小，以及定义裁剪等的许多其他框。

表4-4总结了页面字典中的条目。

|Key|Value type|Value|
|---|---|---|
|/Type* |name|Must be /Page.|
|/Parent* |indirect reference to dictionary|The parent node of this node in the page tree.|
|/Resources|dictionary|The page’s resources (fonts, images, and so on). If this entry is omitted entirely, the resources are inherited from the parent node in the page tree. If there are really no resources, include this entry but use an empty dictionary.|
|/Contents|indirect reference to stream or array of such references|The graphical content of the page in one or more sections. If this entry is missing, the page is empty.|
|/Rotate |integer|The viewing rotation of the page in degrees, clockwise from north. Value must be a multiple of 90. Default value: 0. This applies to both viewing and printing. If this entry is missing, its value is in- herited from its parent node in the page tree.|
|/MediaBox* |rectangle |The page’s media box (the size of its media, i.e., paper). For most purposes, the page size. If this entry is missing, it is inherited from its parent node in the page tree.|
|/CropBox |rectangle|The page’s crop box. This defines the region of the page visible by default when a page is displayed or printed. If absent, its value is defined to be the same as the media box.|

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

|Key|Value type|Value|
|---|---|---|
|/Type*|name|Must be /Pages.|
|/Kids*|array of indirect references|The immediate child page-tree nodes of this node.|
|/Count*|integer|The number of page nodes (not other page tree nodes) which are eventual children of this node.|
|/Parent|indirect reference to page tree node| 待补充|

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

日期字符串的格式为：

```(D:YYYYMMDDHHmmSSOHH'mm')```

其中括号表示通常的字符串。该日期的其他部分在表4-6中进行了总结。

|Portion |含义|
|---|---|
|YYYY |The year, in four digits, e.g., 2008.|
|MM |The month, in two digits from 01 to 12.|
|DD |The day, in two digits from 01 to 31.|
|HH |The hour, in two digits from 00 to 23.|
|mm |The minute, in two digits from 00 to 59.|
|SS |The second, in two digits from 00 to 59.|
|O |The relationship of local time to Universal Time, either +, - or Z. + signifies local time is later than UT, - earlier, and Z equal to Universal Time.|
|HH' |The absolute value of the offset from Universal Time in hours, in two digits from 00 to 23. |
|mm' |The absolute value of the offset from Universal Time in minutes, in two digits from 00 to 59.|

一年之后的所有日期都是可选的。例如，（D：1999）完全有效。但是，很明显，如果省略一个部分，
则必须省略后面的所有内容，否则结果将是模糊的。DD和MM的默认值为01，对于所有其他部分，默认值为零。

例如：

```(D:20060926213913+02'00')```

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
