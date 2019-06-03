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
* 1.选择字体。
* 2.选择位置，大小和方向。
* 3.选择间距，颜色，文本渲染模式和其他参数。
* 4.从字体中选择字符，并在页面上显示。

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

|Operands| Operator|Function|
|---|---|---|
|x,y |Td |Move the text position to the next line, offset by (x,y). The parameters are expressed in unscaled text space units.|
|x,y| TD |Move the text position to the next line, offset by (x,y). Sets the leading to -y. The parameters are expressed in unscaled text space units.|
|- |T* |Move the text position to the next line. Equivalent to the sequence 0 leading Td (where leading is the current text leading).|
|a,b,c,d,e,f| Tm|Sets the text matrix and text line matrix to [a b 0 c d 0 e f 1]. Unlike the graphics matrix operator cm, the matrix replaces the current matrix, rather than being concatenated with it.|


### Showing Text
## Defining and Embedding Fonts
### Font Types in PDF
### Type 1 Fonts
### Font Encodings
### Embedding a Font
## Extracting Text from a Document
## Resources
