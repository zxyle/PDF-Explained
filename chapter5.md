在本章中，我们将介绍在PDF页面的内容流中构建图形
的主要方法。所有示例都基于我们在[第2章](./chapter2.md)中手动创建
的相同PDF，并以相同的方式使用*pdftk*处理成有效的
PDF文档。所有示例都包含在在线资源中。

## Looking at Content Streams
PDF页面由一个或多个内容流组成，由页面对象中的/Contents条目定义，
以及由/Resources条目定义的共享资源集。在我们的所有示例中，
只有一个内容流。多个内容流等同于包含其连接内容的单个流。

这是一个示例页面，没有资源和单个内容流：
```
3 0 obj 
<<
  /Type /Page
  /Parent 1 0 R
  /Resources << >> 
  /MediaBox [ 0 0 792 612 ] 
  /Rotate 0
  /Contents [ 2 0 R ] 
>>
endobj
```

这是关联的内容流，由流字典和流数据组成。
```
2 0 obj
<< /Length 18 >> 流字典
stream
200 150 m 600 450 l S 流数据
endstream
endobj
```
我们将在一瞬间发现m，l和S操作符的所作所为。 
数字是以磅为单位的测量值 - 点（或pt）是1/72英寸。 
将此文档加载到PDF查看器（按照第2章使用*pdftk*处理后）的结果如图5-1所示。

完整的手动创建的文件（在使用*pdftk*处理之前）如例5-1所示。 
我们将在本章的其余部分使用此文件的变体。在大多数情况下，
我们只会更改每个示例的内容流，但稍后我们需要向PDF添加一个或多个额外资源。 
所有这些文件都可以在本书的在线资源中找到。

Example 5-1. Skeleton PDF listing for examples in this chapter
```
%PDF-1.0 PDF文件头
1 0 obj 页面树
<< /Kids [2 0 R]
  /Type /Pages
  /Count 1 >>
endobj
2 0 obj 页面对象
<< /Rotate 0
   /Parent 1 0 R
   /MediaBox [0 0 792 612] 
   /Resources 3 0 R
   /Type /Page
   /Contents [4 0 R]
>>
endobj
3 0 obj 资源
<< >>
4 0 obj 页面内容流
<< /Length 19 >>
stream
200 150 m 600 450 l S 
endstream
endobj
5 0 obj 文件目录
<< /Pages 1 0 R
   /Type /Catalog 
>>
endobj xref 骨架交叉引用表
0 6
trailer 文件尾字典
<< /Root 5 0 R
   /Size 6 
>>
startxref
0
%%EOF 文件结束标记
```
内容流几乎总是被压缩，因此要检查现有文档的内容流，我们可以使用*pdftk*解压缩操作。例如，命令：
```
pdftk input.pdf decompress output output.pdf
```
将input.pdf写入output.pdf，并将流解压缩。

## 运算符和图形状态
|Group|用于|运算符|
|---|---|---|
|图形状态运算符|更改图形状态（当前颜色，笔触宽度等）|w J j M d ri i gs q Q cm CS cs SC SCN sc scn G g RG rg Kk|
|路径建设运算符|构建线条，曲线和矩形|m l c v y h re|
|路径绘画运算符|笔划和填充路径，或使用它们来定义剪裁区域|S s f F f* B B* b b* n W W*|
|其他绘画运算符|着色图案和内嵌图像|sh BI ID EI Do|
|文本运算符|选择并以各种字体和方式显示文本|Tc Tw Tz TL Tf Tr Ts Td TD Tm T* Tj TJ ' '' d0 d1|
|标记内容和兼容性运算符|用于划分流的部分|MP DP BMC BDC EMC BX EX|

通过依次考虑每个运算符及其操作数来呈现页面 图形状态始终保持不变，
由一些运算符改变，由其他人咨询。操作数通常是数字，但可以是名称，字典或数组。

表5-2总结了表示我们的示例所需的图形状态部分，如可能出现在典型的PDF实现中。
|条目|类型|初始值|
|---|---|---|
|当前的转换矩阵|矩阵|将默认用户坐标转换为设备坐标的矩阵|
|填色|颜色|黑|
|线条颜色|颜色|黑|
|线宽|real|1.0|
|路径连接样式|整数|Mitered joins (0)|
|Cap style|整数|Square butt caps (0)|
|Line dash pattern|整数数组|实线|
|当前剪切路径|路径|空路径|
|混合模式|名称或数组|正常|
|Soft mask|名字或字典|None|
|透明度常数|real|1.0（完全不透明）|
|Alpha source|布尔|false|

## 构建和绘制路径
我们正在使用风景美国信函页面（宽11英寸或792点;高8.5英寸或612点）。 
默认情况下，PDF坐标系的原点位于页面的左下角，x和y分别向右和向上增加。

让我们使用一些路径构造，描边和线属性操作符来构建一个简单的图形流：
```
100 100 m 300 200 l 700 100 l Move to (100, 100), line to (300, 200), line to (700, 100) 
S Stroke the line
8 w 将线宽从默认值（1.0）更改为8.0
1 J 将行结束上限从默认方块（代码0）更改为舍入（代码1）
100 200 m 300 300 l 700 200 l 定义新路径，相同的形状，但在页面上方100个pts
S Stroke the new line
[20] 0 d 改为20pt破折号
100 300 m 300 400 l 700 300 l 定义新路径, same shape but another 100pts higher up the page S Stroke the new line
```

结果如图5-2所示。

我们使用m运算符移动到新路径的开头，并使用l运算符形成两条线。 
请注意，此时没有绘制任何内容 - 只有当我们使用S运算符来
描边时才会影响页面。S运算符也清除当前路径。

w运算符将图形状态中的线宽设置为8个点。J运算符将行结束设置为圆顶。
使用d运算符设置虚线模式，该运算符采用两个操作数：
一个数组（一个短划线长度，间隙长度，短划线长度等重复序列，在划线时循环）
和一个初始偏移量（移动模式的开始。在我们的例子中，只有一个条目，
因此破折号和间隙都是20pt，相位是0。

表5-3，表5-4和表5-5分别总结了线连接，虚线图案和线帽。

路径可以由多个子路径构成，每个子路径以m运算符开始。 
这可以用于定义由几个不连续的形状构成的单个路径。

|连接数|含义|
|---|---|
|0|斜接|
|1|圆形连接|
|2|斜面连接|


|短划线模式规范|含义|
|---|---|
|[] 0|实线|
|[2] 0|2 on, 2 off, 2 on...|
|[2] 1|1 on, 2 off, 2 on... (phase is set to 1)|
|[2 3] 0|2 on, 3 off, 2 on...|

|Cap数|含义|
|---|---|
|0|Butt caps. Squared off at the end of the line.|
|1|Round caps. Semicircles attached at the end of each line.|
|2|Projecting square caps. Projects at end of line for half the width of the line, and is then squared off.|

### 贝塞尔曲线
除了直线，我们还可以绘制曲线。
有许多不同的可能方案来定义曲线，
但业界已经确定了Bézier曲线，
以汽车工程师Pierre Bézier的名字命名。 
它们易于操作并且可以用鼠标在屏幕上进行操作，
相对容易以任何分辨率或精度绘制，并且易于数学定义。

曲线由四个点（起点和终点）以及两个控制点定义，
这两个控制点定义曲线在开始和结束之间的形状。
曲线不一定通过控制点，但总是完全位于由其四个点定义的凸四边形内。
 
示例曲线，显示起点和终点以及两个控制点（显示
使用来自端点的虚线，因为它们可能在图形编辑器中表示）
如图5-3所示。这是通过使用c运算符生成的：
```
300 200 m 400 300 500 400 600 200 c S
```
我们使用m运算符将当前点移动到曲线的起点。
c运算符再采用三个坐标：第一个控制点，第二个控制点和终点。

有关Bézier曲线的更多信息，请参阅图形文本 - 请参阅第119页的“PDF和图形文档”。

#### 用贝塞尔曲线绘制圆圈
有趣的是，不可能在PDF中绘制精确的圆圈。 
但我们可以使用几条Bézier曲线来近似地逼近一条曲线。 
我们将使用四条对称曲线（最小数量以获得良好结果），每个象限一条。 
对于以（1,0）为中心的单位圆的样本象限，坐标如图5-4所示。 数字k约为0.553。

### Filled Shapes and Winding Rules
通过将另一个操作符从表5-6中替换为我们之前使用的S操作（这里，我们使用B来填充和描边路径），
可以填充和描边路径。 图5-5显示了使用以下代码填充和描边的形状：
```
2.0 w
0.75 g Change fill color to light Gray 250 250 m Move to start of path
350 350 450 450 550 250 c First curve 450 250 350 200 y Second curve
h B Close and fill
```
## Colors and Color Spaces
## Transformations
## Clipping
## Transparency
## Shadings and Patterns
## Form XObjects
## Image XObjects
