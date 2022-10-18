# 第9章 - 使用pdftk

*pdftk*是一个基于iText库的多平台命令行工具（在第116页的“iText for Java和C＃”中有描述）。
它具有合并，拆分和标记文档以及设置和读取元数据的功能。

## 命令行语法
*pdftk*有一个不寻常的命令行界面，其中元素通常必须以特定顺序出现。
我们可以按照指定的顺序将它们分成四组：

1. 输入文件或文件，以及可能的输入密码。
1. 操作及其所需的任何参数。
1. 输出和任何输出密码和权限。
1. 杂项输出和其他选项。

完整的详细信息可以在*pdftk*的手册中找到 - 在本章中，我们仅提供示例所需的子集。

## 合并文档
要合并文档，我们使用cat操作。这是默认操作，因此我们实际上不需要指定cat关键字。例如，要将三个文件的页面合并为一个，我们需要：
`pdftk file1.pdf file2.pdf file3.pdf output output.pdf`

这将按顺序将新文件写入包含file1.pdf，file2.pdf和file3.pdf的所有页面的output.pdf。输出文件可能与任何输入文件不同。

Pdftk允许我们选择从每个文档中获取哪些页面，以及每个输出页面的查看轮换。通过在输入之后按顺序列出它们来使用这样的页面范围。例如：
`pdftk file1.pdf file2.pdf 1-5 even output out.pdf`

从file1.pdf获取第1页到第5页，从file2.pdf获取第2页，第4页，第6页。

要在输出中的两个或多个不同点处包含文件中的页面，我们可以通过写入来协调每个文件的句柄，
例如A = input.pdf，并在给出页面范围时引用这些句柄。

* A1 A B文件A的第一页（复制为封面），然后是整个文件A和B.
* A4-50oddD标记为A的文件的奇数页在4到50之间，旋转180°。

例如:
`pdftk A=file.pdf B=file2.pdf A1 A B output out.pdf`

### 合并文件时会发生什么
要以pdftk的方式执行PDF文件的简单合并，可能会执行以下步骤：
1. 将每个文件读入内存并创建PDF对象的图形，可能是懒惰的（即，根据需要解析对象，因为如果仅包括某些页面，则不需要所有这些对象）。
1. 重新编号对象图中的对象，使它们互斥，即1 ... p，p + 1 ... q，q + 1 ... r等。
1. 将所有这些PDF对象放入新的对象图中。
1. 创建一个新的页面树，其中包含原始文件中所需的页面对象组合。
1. 创建一个新的预告片字典和根对象，链接到新的页面树。
1. 将新文档写入文件。

一个功能齐全的合并还需要：

* 由于使用了页面范围，修剪了对文档中不再存在的页面的引用。如果没有这样做，对不在输出中的页面的单个引用可能导致包含该页面中的所有对象，从而使输出膨胀。
* 删除重复的字体定义。通常，要合并的文件来自同一个源，并共享内容，如字体。这些可以进行重复数据删除以节省空间。
* 合并文件书签，目的地，表格等的其他部分。一般来说，严格按页面生存的数据会自动生存，但文档范围的数据需要特定的合并支持。
* 决定从何处获取元数据和PDF版本号（例如，使用输入中的最高PDF版本号并从第一个给定文件中获取元数据）。

## 分割文档
要从文档中选择页面，我们使用与合并相同的语法，因为我们的操作相当于只将一个文件与页面范围合并：
`pdftk file1.pdf 2-20 output out.pdf`

这将页面2-20包含在输出文件中。Pdftk有一个单独的工具，用于将文件分割成单独的页面，并使用突发操作将它们全部写入磁盘。
`pdftk input.pdf burst`

默认情况下，这会将页面写入pg_0001.pdf，pdf_0002.pdf等。要使用格式不同的名称编写页面，可能会提供内置C函数printf样式的输出字符串。例如：
`pdftk input.pdf burst output page%03d.pdf`

会创建page001.pdf，page002.pdf等。

突发操作还将文档的元数据写入文件doc-data.txt。我们在第111页的“提取和设置元数据”中考虑了此功能。


### 分割文件后会发生什么
为了将PDF分成一个或多个页面的几个部分，像*pdftk*这样的程序将采取以下步骤：

1. 将输入文档加载并解析为对象图，可能是懒惰的（这样就不必处理任何输出中不会出现的页面）。
1. 为每个新文档创建一个新的空PDF数据结构。使用与现有文档相同的对象编号为每个页面范围创建新的页面树。
1. 将输入PDF中的所有对象复制到每个输出PDF中。
1. 删除每个PDF中不需要的所有对象（即不再引用的对象）。

要正确执行最后一步，处理书签，目标和其他跨页对象以删除对不再出现在给定输出文件中的页面的引用非常重要，因为单个错误引用可能会导致源文件的整个对象 图表被包括在内，即使它们都不是必需的。


## 图章和水印
图章是放置在另一个邮票上的PDF页面，以便组合页面内容。水印（*pdftk*称为背景）是相同的，但是标记位于现有页面内容下。如果输入PDF的页面具有彩色背景，则这不起作用，因为水印通常不会显示。

使用*pdftk*，可以使用图章和水印操作来实现，这些图章将图章放在给定范围内的所有页面上（或下面）。如果页面大小不同，则会缩放图章以适合和居中。

例如:
`pdftk file.pdf stamp stamp.pdf output output.pdf`

### 如何添加图章
当像*pdftk*这样的程序为输入PDF添加图章时，必须执行以下步骤：

1. 将两个文件加载并解析为PDF对象图。
1. 纠正两个PDF中的对象编号，使它们互斥。邮票PDF中的对象现在可以添加到输入PDF中。
1. 标记的页面数据被适当地缩放并且相对于源PDF中每页的页面大小居中。
1. 图章的页面数据被附加到每页上的源PDF的页面数据。必须重命名字体和图像等资源，以免发生冲突。在添加新数据之前，必须匹配任何不匹配的堆栈运算符（q / Q）。
1. 现在可以将PDF写入输出文件。


## 提取和设置元数据
*Pdftk*可以将文档的元数据（作者，标题等）提取为文本文件，可以是ASCII格式（非ASCII字符编码为XML样式的数字实体），也可以是Unicode UTF8。
这是通过dump_data或dump_data_utf8关键字实现的。例如：
`pdftk input.pdf dump_data output data.txt`

将示例9-1中的数据写入data.txt。
```
Example 9-1. Example output of pdftk dump_data operation (ellipses indicate where we have
truncated the output for brevity)
InfoKey: Creator
InfoValue: XSL Formatter V4.3 R1 (4,3,2008,0424) for Linux InfoKey: Title
InfoValue: PDF Explained
InfoKey: Producer
InfoValue: Antenna House PDF Output Library 2.6.0 (Linux) InfoKey: ModDate
InfoValue: D:20110713115225-05'00'
InfoKey: CreationDate
InfoValue: D:20110713115225-05'00'
PdfID0: 57f4673abea4ca58a27e19bf1871dfa
PdfID1: 57f4673abea4ca58a27e19bf1871dfa
NumberOfPages: 90
...
BookmarkTitle: Table of Contents
BookmarkLevel: 1
BookmarkPageNumber: 5
BookmarkTitle: Preface
BookmarkLevel: 1
BookmarkPageNumber: 9
BookmarkTitle: Why Read This Book?
BookmarkLevel: 2
BookmarkPageNumber: 9
BookmarkTitle: Audience
BookmarkLevel: 2
BookmarkPageNumber: 9
...
PageLabelNewIndex: 1
PageLabelStart: 1
PageLabelNumStyle: DecimalArabicNumerals PageLabelNewIndex: 5
PageLabelStart: 5
PageLabelNumStyle: LowercaseRomanNumerals PageLabelNewIndex: 13
PageLabelStart: 1
PageLabelNumStyle: DecimalArabicNumerals
```

该数据列出：

1. 文档信息字典中的值和键
1. 文档中的页数
1. 书签标题，级别和目标页面
1. 页面标签

update_info操作可用于执行反向操作：设置上面列出的信息。还有相应的update_info_utf8操作。
例如，我们可以修改我们创建的data.txt文件，然后使用update_info：
`pdftk input.pdf update_info data.txt output output.pdf`


## 文件附件
PDF文件可以在文档或页面级别添加附件。PDF附件的技术基础将在[第7章](./chapter7.md)中讨论。要在文件级别添加附件：
`pdftk input.pdf attach_files file1.xls file2.xls output output.pdf`

附件将添加到文件级附件列表的末尾。要在页面级别添加附件，请使用to_page关键字：
`pdftk input.pdf attach_files file1.xls to_page 4 output output.pdf`

要从文档中提取附件，将它们写入给定目录，我们可以使用unpack_files关键字：
`pdftk input.pdf unpack_files output outputs/`

这会将附件在其原始文件名下写入输出目录中。

## 加密与解密
Pdftk具有读取加密文件和加密输出文件的功能。

### 解密输入文件
input_pw关键字可用于指定输入文件的所有者密码。密码通过使用句柄与输入相关联，与页面范围一样。
如果没有给出句柄，则假定密码的输入顺序与输入文件的顺序相同。如果给出了用户密码，则大多数pdftk功能将不可用，因为PDF安全模型会阻止它。

例如，要合并两个加密的文件，可以提供密码：
`pdftk file1.pdf file2.pdf input_pw fred charles output out.pdf`

这里，“fred”是file1.pdf的密码，“查询”file2.pdf的密码。


### 加密输出
*Pdftk*可以使用encrypt_40bit和encrypt_128bit关键字使用40位或128位RC4加密方法加密输出。
我们可以使用owner_pw和user_pw关键字指定所有者和用户密码。例如，要使用所有者密码加密具有128位加密的文件，但使用空白用户密码：
`pdftk input.pdf output output.pdf encrypt_128bit owner_pw fred`

请注意，我们省略了user_pw关键字以指示空白用户密码。

我们尚未指定输入用户密码时允许的操作。这可以通过将allow关键字与一个或多个权限一起使用来完成（对应于[第8章](./chapter8.md)中列举的权限）：

```
Printing
DegradedPrinting
ModifyContents
Assembly
CopyContents
ScreenReaders
ModifyAnnotations
FillIn
AllFeatures (all of the above, plus top quality printing)
```

例如，要允许表单填写，但没有别的：
`pdftk input.pdf output output.pdf encrypt_128bit allow FillIn owner_pw fred`


## 压缩
为了查看或编辑页面级内容（如图形运算符流），首先必须删除用于数据流的压缩。这可以通过pdftk uncompress修饰符来实现：
```
pdftk compressed.pdf output uncompressed.pdf uncompress
```

可以通过使用compress来反转该过程（例如，在手动编辑之后）：
```
pdftk uncompressed.pdf output compressed.pdf compress
```

[目录](./README.md)&nbsp;|[上一章：加密文档](./chapter8.md)|&nbsp;[下一章：PDF软件和文档](./chapter10.md)
