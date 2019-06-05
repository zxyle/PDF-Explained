# 第6章 - 文本和字体

在上一章中，我们看到了如何使用一系列图形运算符来绘
制页面上的内容，方法是引用它们的操作数和基于堆栈
的图形状态。

在本章中，我们将查看操作符和状态，以便从字体中选择
字符并在页面上打印它们。然后，我们将看到如何定义
字体及其指标并将其嵌入PDF文档中。 
最后，我们讨论了从文档中提取通用文本的复杂任务。

## Text and Fonts in PDF
可以定义一个页面描述语言，其中没有执行任何文本布局，
并且提供了纯文本以及要即时填充的框和列，就像桌面发布包一样。
相反，可以根本不用定义没有字体或文本的页面描述语言，
仅仅依赖于在生成文档时将文本转换为轮廓形状，
例如，已经在文字处理器中进行了布局。

PDF采用中间立场 - 保留了字体和小尺寸文本布局的思想，
但必须提前进行大规模的段落布局。这具有以下优点：
* 完全控制布局，因为大规模布局（段落，换行符）是生成PDF的程序的工作。该文件看起来应该是这样的。
* 支持可预测的小规模文本布局，例如具有固定字符间距的字符串，因此不需要明确说明每个字符的位置。
* 通过使用字体作为字符形状库来节省空间，并简单地包含现有字体文件，从而最大限度地减少兼容性和可移植性问题。
* 保留原始字符和一些布局元素，因此通常可以进行复制和粘贴以及文本提取。

## Text State
文本状态参数和修改它们的运算符总结在表6-1中。

|Parameter| Description| Operands| Operators| Initial value|
|---|---|---|---|---|
|Tc|Character spacing|charSpace|Tc sets the character spacing to charSpace, expressed in unscaled text space units.|0|
|Tw|Word spacing|wordSpace|Tw sets the word spacing to wordSpace, expressed in unscaled text units.|0|
|Th |Horizontal spacing|scale|Tz sets the horizontal scaling to (scale / 100). |100 (normal spacing)|
|Tl|Leading|leading|TL sets the text leading to leading, expressed in unscaled text space units.|0|
|Tf, Tfs |Font, Font Size|font, size|Tf selects the font font at size size points.|None. Must be specified.|
|Tmode| Rendering Mode|render|Tr sets the text rendering mode to render, an integer.|0|
|Trise|Rise|rise|Ts sets the text rise to rise, expressed in un- scaled text space units.|0|

我们在第75页的“文本空间和文本定位”中讨论了“未缩放的文本空间单位”这一短语。文本状态与图形状态一起存储，并使用上面的运算符进行操作。 当前文本状态受堆栈运算符q和Q的影响，就像图形状态一样。

## Printing Text
在页面上打印文本需要：
1. 选择字体。
1. 选择位置，大小和方向。
1. 选择间距，颜色，文本渲染模式和其他参数。
1. 从字体中选择字符，并在页面上显示。

### Text Sections
运算符BT（开始文本）和ET（结束文本）在文本部分周围形成括号。
用于在页面的内容流中显示文本的运算符可能仅出现在BT和ET之间。
但是，用于改变文本状态的操作符不受这种限制。
文本部分还可能包含其他操作员改变一般图形状态。

举个例子，我们回到第2章中的“Hello，World！”文件：
```
1. 0. 0. 1. 50. 700. cm Position at (50, 700) 
BT Begin text block
  /F0 36. Tf Select /F0 font at 36pt
  (Hello, World!) Tj Place the text string 
ET End text block
```

在这里，我们使用带有字体名称和大小运算符的Tf运算符来选择字体，使用Tj运算符来显示文本字符串。
我们依靠图形运算符cm来定位文本。现在，我们将讨论更改文本位置的其他方法。

### Text Space and Text Positioning
文本空间是定义文本的坐标系。从此文本空间到用户空间的转换（然后像往常一样转换到设备空间）确定文本在页面上的放置位置。
文本字符串中第一个字形的原点位于文本空间的原点。

有两个矩阵需要考虑：
* 文本矩阵，定义下一个字形的当前转换。它由文本定位和显示运算符的文本改变。
* 文本行矩阵，它是当前行开头的文本矩阵的状态。因此，通过使用操作员移动到下一行，可以垂直对齐文本行，而无需手动跟踪行的开始位置。

这些矩阵不会从文本部分持续到文本部分，而是在每个文本部分的开头重置为单位矩阵。
结合字体大小，水平缩放和文本上升，这两个矩阵定义了从文本空间到用户空间的转换。

表6-2总结了修改文本位置的运算符。

|操作数| 操作符|功能|
|---|---|---|
|x,y |Td |Move the text position to the next line, offset by (x,y). The parameters are expressed in unscaled text space units.|
|x,y| TD |Move the text position to the next line, offset by (x,y). Sets the leading to -y. The parameters are expressed in unscaled text space units.|
|- |T* |Move the text position to the next line. Equivalent to the sequence 0 leading Td (where leading is the current text leading).|
|a,b,c,d,e,f| Tm|Sets the text matrix and text line matrix to [a b 0 c d 0 e f 1]. Unlike the graphics matrix operator cm, the matrix replaces the current matrix, rather than being concatenated with it.|


### Showing Text
Tj运算符在当前位置显示文本。这与我们已经看到的文本定位算子相结合就足够了。
但是，为方便起见，还提供了三个附加操作符（'，''和TJ）。这些是文本显示和文本定位的常见组合的快捷方式。
表6-3总结了显示操作员的文本。

|Operands|Operator |Function|
|---|---|---|
|string |Tj |Show string at the current position.|
|string|' |Go to the next line, taking into account the leading and text matrices, and show string at the new position. The same as using T* followed by Tj.|
|wordspace, charspace, string|'' |Set the word spacing to wordspace and the character spacing to charspace. Go to the next line, taking into account the leading and text matrices, and show string at the new position. The same the sequence wordspace Tw charspace Tc string '.|
|array|TJ |This operator allows a text string to be shown with adjustments for individual glyph positions (for example, kerning). The array contains strings and numbers, in any combination. String entries are shown as normal; number entries adjust the text matrix horizontally by subtracting that amount (expressed in thousandths of a unit of text space).|

为简单起见，我们现在将使用标准字体和基于Latin-1的PDFDocEncoding来显示一些显示文本的示例。
与往常一样，这些示例可以在在线资源中找到。

#### Character and word spacing
这是我们的第一个例子，我们使用各种运算符显示一些文本行。结果如图6-1所示：

```
BT
/F0 36 Tf
1 0 0 1 120 350 Tm
50 TL
(Character and Word Spacing) Tj T* 
3 Tc
(Character and Word Spacing) Tj T* 
10 Tw
(Character and Word Spacing) Tj
ET
```

在这个例子中，我们有：

1. Used Tf to select font /F0 at 36 points.
2. Used Tm to set the text position to (120, 350).
3. Used TL to set the leading to 50 points.
4. Shown a string with Tj, and used T* to move to the next line.
5. Set the character spacing to 3 points, and drawn the string again.
6. Set the word spacing to 10 points, and drawn the string a third time.

![](./images/figure%206-1.png)

#### Text transforms
在此示例中，我们将展示文本转换如何与图形转换相结合，以确保文本定位操作（例如，移动到下一行）正常工作，即使整个文本部分已转换。
结果如图6-2所示：
```
0.96 0.25 -0.25 0.96 0 0 cm BT
/F0 48 Tf
48 TL
1 0 0 1 270 240 Tm
(Text and graphics) Tj T* 
(transforms combined) Tj T* 
(with newlines) Tj
ET
```

在这里，我们有：

1. Set up the graphics matrix to rotate anticlockwise around the origin with cm. 
2. Selected a font and set the leading with Tf and TL.
3. Set the text matrix to offset the start by (270, 240) with Tm.
4. Written three lines with Tj and T*.


![](./images/figure%206-2.png)

#### Text rise
Ts运算符可用于调整文本的垂直位置：
```
BT
/F0 72 Tf
1 0 0 1 140 290 Tm (Text) Tj
20 Ts
(Up) Tj
0 Ts
(and) Tj
-20 Ts
(Down) Tj
ET
```

结果如图6-3所示。这是我们第一次使用多个Tj运算符而不启动新行。
请注意，显示文本的Tj运算符将文本位置设置为刚刚绘制的字符串的末尾。
![](./images/figure%206-3.png)

#### Kerning and glyph adjustment
TJ运算符是Tj的替代，用于绘制具有水平字形调整的字符串。这些通常发生在文字放置在文字处理器或打字机中时，特别是如果内容完全合理。
TJ运算符是一种编码此信息的便捷方式，无需为每行文本使用数十个运算符：
```
BT
/F0 72 Tf
90 TL
1 0 0 1 240 330 Tm
[(PJ WAYNE)] TJ T*
[(P)150(J )(W)150(A)80(YN)20(E)] TJ ET
```

我们在这里曾经两次使用过TJ; 一次显示文本正常，第二次包括传递给TJ的数组中的手动kerns。结果如图6-4所示。
![](./images/figure%206-4.png)

#### Text rendering modes
文本有七种渲染模式，使用Tr运算符设置。其中四个用于将文本设置为剪切路径，一个用于编写不可见文本。
我们在这里不考虑这些。 其他三个（模式0,1和2）分别用于填充，抚摸和填充跟随行程。颜色的设置方式与形状绘制相同：
```
0.5 g
BT
/F0 72 Tf
1 0 0 1 160 380 Tm
90 TL 
(Text Mode Zero ) Tj T*
1 Tr 
(Text Mode One ) Tj T*
2 Tr 
(Text Mode Two) Tj
ET
```

结果如图6-5所示。

![](./images/figure%206-5.png)

## Defining and Embedding Fonts
字体是特定字符集的字形（字符形状）的集合。在PDF中，字体由字体字典组成，
字体字典定义度量，字符集和编码（将文本字符串中的字符代码映射到字体中的字符），
以及字体程序（实际的字体文件），以各种格式（Type 1，TrueType等）。


### Font Types in PDF
PDF允许使用主要的流行字体格式以及类型3字体，通过使用PDF图形运算符集合直接定义字符形状，
允许编码任何其他字体类型（例如，传统位图字体）。

Type 1 fonts
在字体字典中引入字体类型/Type1。类型1是最初用于PostScript的Adobe字体格式。标准的14种字体被定义为Type 1字体。 多个Master Type 1字体（/MMType1）是Type 1的扩展，允许从一组轮廓自动生成许多字体样式。

TrueType字体
在字体字典中引入字体类型/ TrueType。基于Apple的True-Type字体格式（也常用于Microsoft Windows）。

Type 3 fonts
引入字体类型/Type3。这些是由PDF图形运算符流组成的字体。这意味着它们可以包含颜色和阴影，因此更灵活，但没有用于在小尺寸下清晰显示的提示机制。通常用于模拟其他字体格式（例如，位图字体）。

CID字体
这些是复合字体，旨在支持多字节字符集（其中字体具有大量字形，例如中文）。本文不讨论它们。

### Type 1 Fonts
我们将使用Type 1字体作为示例。表6-4总结了Type 1字体字典中的条目。

|Key|Value type|Value|
|---|---|---|
|/Type* |name|Must be /Font.|
|/Subtype* |name|Must be /Type1.|
|/BaseFont* |name|The PostScript name for the font.|
|/FirstChar** |integer|The first code in the /Widths array.|
|/LastChar** |integer|The last code in the /Widths array.|
|/Widths**|array of integers|Array of length (/LastChar - /FirstChar + 1), giving the glyph width for those characters in thousandths of text space units.|
|/FontDescriptor** |indirect reference to dictionary|A font descriptor dictionary giving the font’s metrics (other than the glyph widths).|
|/Encoding|name or dictionary|The font’s character encoding, for example /MacRomanEncoding or /WinAnsiEncoding. More complicated ones are described by dictionaries.|
|/ToUnicode|stream|A stream containing instructions for the extraction of text content. See “Extracting Text from a Document” on page 86.|

PDF中有14种标准的Type 1字体。这些是在任何PDF应用程序中必须提供度量标准和大纲（或合适的替换字体）的字体。
然而，现在，Adobe建议所有字体都是完全嵌入的，即使是这些。标准字体是：

```
Times-Roman 
Times-Bold 
Times-Italic 
Times-BoldItalic 
Helvetica 
Helvetica-Bold 
Helvetica-Oblique 
Helvetica-BoldOblique 
Courier
Courier-Bold 
Courier-Oblique 
Courier-BoldOblique 
Symbol 
ZapfDingbats
```

例如，这是一个简单的Type 1字体：
```
1 0 obj
<< /Type /Font
   /Subtype /Type1
   /BaseFont /Times-Roman
   /FirstChar 0
   /LastChar 255
   /Widths [ 255 255 255 255 ... 744 268 380 380 380 380 380 380 380 380 380 380 ] 
   /FontDescriptor 2 0 R
   /Encoding /WinAnsiEncoding
>>
```

省略号...是我们省略的内容，不是PDF语言的一部分。我们稍后讨论/FontDescriptor和/Encoding条目。 
/Widths数组为此字体中的每个256个字符提供文本空间单位的千分之一宽度。

### Font Encodings
字体编码描述字符代码（内容流中使用的字符串中的字符）和字体中的字形描述之间的映射。
字体程序有自己的内置编码，但PDF字体可以改变编码以使用带有Microsoft Windows编码的Macintosh字体，
或者使用单字节编码从256字节以上的字体中选择最多256个字符 字形（例如，字符或连字的变体）。

最简单的/Encoding条目只是标准编码之一的名称，这些编码在PDF标准，附录D中定义。
通过使用字典而不是编码名称来定义更复杂的编码。表6-5总结了该词典中的条目。

|Key |Value type|Value|
|---|---|---|
|/Type |name |Must be /Encoding|
|/BaseEncoding| name|The base encoding, from which the /Differences entry defines differences. This is one of the predefined encodings /MacRomanEncoding, /MacExpertEncoding, or /WinAnsiEncoding. If this entry is absent, the differences are from the font file’s built-in encoding.|
|/Differences array of integers and |names|Defines the differences from the base encoding. Contains zero or more sections each beginning with a number n followed by glyph names for character n, n+1, n+2 etc. For example[6 /endash /emdash 34 /space]maps6to/endash,7 to /emdash, and 34 to /space.|

在例6-1中，字体具有一种编码，该编码通过用字符/项目符号（项目符号点）替换字符1来定义与内置字体编码的差异。
这意味着PDF查看器可以正确剪切和粘贴文本，因为它现在知道字符代码1是项目符号点（类似/bullet的名称是在Adobe字形列表中预定义的）。 它对PDF的显示没有任何影响。

```
Example 6-1. A font encoding for a font with the bullet point added
25 0 obj
<< /Type /Font
   /Subtype /Type1
   /Encoding 23 0 R Reference to the encoding dictionary. 
   /BaseFont /Symbol
   /ToUnicode 24 0 R Instructions for conversion to Unicode.
>> 
endobj
23 0 obj Encoding dictionary 
<< /Type /Encoding
   /BaseEncoding /WinAnsiEncoding The base encoding.
   /Differences [ 1 /bullet ] The differences 
>>
endobj
```

### Embedding a Font
创建PDF文件时，必须嵌入字体，以便显示PDF或以其他方式处理PDF的程序可以使用字形描述和编码。要嵌入字体：

1.提取字体文件中的各种细节 - 根据所讨论的字体格式而变化的过程。这些细节（度量，编码等）用于填写字体字典，字体度量和字体编码字典。
2.现在可以从有问题的字体文件中删除这些细节，如果字体格式允许，只留下字形描述 - 所有这些信息现在都在字体字典中。这减小了嵌入字体的大小。
3.字体可以是子集，删除整个字形描述，将字体文件缩减为仅保存实际使用的字符的字体文件。例如，仅用于文档标题的字体实际上可能只使用十个字符。根据字体格式，可能必须更改编码以将所有这些字符放在字体的前几个字符位置，以便它们编号为1,2,3 ....子集字体可以由形成的前缀标识六个大写字母后跟一个+，例如RTFGRF +。当创建子集以允许不同的子集彼此区分时，生成该唯一代码。

示例6-2中给出了嵌入字体的示例。

```
Example 6-2. An embedded font, including encoding and font descriptor
9 0 obj 
<</Type /Font
   /Subtype /TrueType It's a TrueType font
   /BaseFont /GCCBBY+TT8Et00 Font is TT8Et00. GCCBBY+ prefix identifies as a subset font. 
   /FontDescriptor 8 0 R
   /FirstChar 1 There are 41 characters in this font.
   /LastChar 41
   /Widths
     [603 603 603 603 603 603 603 603 603 603 603 603 603 603 The widths. It's a fixed-width font. 
      603 603 603 603 603 603 603 603 603 603 603 603 603 603
      603 603 603 603 603 603 603 603 603 603 603 603 603]
   /Encoding 14 0 R 
>>

14 0 obj The font encoding.
<< /Type /Encoding
   /BaseEncoding /WinAnsiEncoding The base encoding
   /Differences The changes. In this case, it's a subset font with the characters at position 1 onward.
     [1 /w /i /d /g /e /t /s /T /h /space /r /u /l /a /x /bracketleft 
      /underscore /J /o /n /S /m /quotesingle /A /p /c /bracketright 
      /one /colon /braceleft /b /k /braceright /v /period /parenleft 
      /two /parenright /asterisk /y /P]
>> 
endobj

8 0 obj The font descriptor, giving the remaining metrics. 
<< /Type /FontDescriptor
   /FontName /GCCBBY+TT8Et00 
   /FontBBox [0 -205 602 770] 
   /Flags 4
   /Ascent 770
   /CapHeight 770
   /Descent -205
   /ItalicAngle 0
   /StemV 90
   /MissingWidth 602
   /FontFile2 12 0 R The actual font file, here in TrueType format.
>> 
endobj
```
这里不讨论实际字体格式（Type1，TrueType等）的细节 - 实际上，它们也没有在PDF标准中讨论，而是由来自这些字体格式的提供者的外部文档讨论。

## Extracting Text from a Document
习惯上在文件的字体词典中包含足够的信息，以允许检索实际的字符标识（而不仅仅是字形）。
这对于允许用户从PDF查看应用程序（如Adobe Reader）中搜索和复制文本非常重要。In还可以以更有限的容量使用，以允许对文档的文本内容进行编辑。

有两种机制：字体中的/Encoding条目（将字符代码映射到Adobe Glyph List条目，如/bullet），以及更现代的机制，/ToUnicode条目提供由定义的语言的程序 Adobe将字符代码直接映射到Unicode实体。以下是/ToUnicode程序的示例：
```
23 0 obj
<< /Length 317 >>
stream
/CIDInit /ProcSet findresource begin 12 dict begin begincmap /CIDSystemInfo << 
/Registry (Symbol+0) /Ordering (T1UV) /Supplement 0 >> def
/CMapName /Symbol+0 def
1 begincodespacerange <01> <01> endcodespacerange
1 beginbfrange
<01> <01> <2022> Maps character code 1 to Unicode U+2022, the bullet point
endbfrange
endcmap CMapName currentdict /CMap defineresource pop end end
endstream
endobj
```

提取文本的另一个困难是重构内容流中的文本运算符。操作符可以将文本拆分为字距调整或对齐，并且行末的连字符可以中断字符流。实际上，文本操作符甚至可能出现故障。
但是，通常情况下，可以从大多数现代文件中产生良好的文本重构。


## Resources
除PDF标准外，还有许多其他文档提供了有关本章讨论主题的更多详细信息：

* Unicode由Unicode Consortium出版的Unicode标准5.0版完整描述。一个更容易理解的介绍是O'Reilly自己的由Jukka K. Korpela解释的Unicode。
* Yannis Haralambous（O'Reilly）的字体和编码解释了PDF使用的各种字体格式。
* Adobe字体和类型技术中心是各种字体格式和编码系统的历史和当前文档的集合，包括用于编码外语的预编码方法。

