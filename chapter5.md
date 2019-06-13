# 第5章 - 图形

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

![](./images/figure%205-1.png)

## 运算符和图形状态
内容流由一系列运算符组成，每个运算符前面都有零个或多个操作数。
表5-1列出了6组中的78个图形运算符。在本章中，我们将关注前四组中的选定运算符。

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
|Cap样式|整数|Square butt caps (0)|
|线划线图案|整数数组|实线|
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
![](./images/figure%205-2.png)

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
|0|对接帽。在线的尽头摆平|
|1|圆帽。每条线末端附有半圆形|
|2|投射方帽。在线末端的项目为线宽的一半，然后平方|

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

![](./images/figure%205-3.png)

#### 用贝塞尔曲线绘制圆圈
有趣的是，不可能在PDF中绘制精确的圆圈。
但我们可以使用几条Bézier曲线来近似地逼近一条曲线。
我们将使用四条对称曲线（最小数量以获得良好结果），每个象限一条。
对于以（1,0）为中心的单位圆的样本象限，坐标如图5-4所示。数字k约为0.553。

![](./images/figure%205-4.png)


### 填充形状和绕组规则
通过将另一个操作符从表5-6中替换为我们之前使用的S操作（这里，我们使用B来填充和描边路径），
可以填充和描边路径。图5-5显示了使用以下代码填充和描边的形状：
```
2.0 w
0.75 g 将填充颜色更改为浅灰色
250 250 m 移动到路径的开头
350 350 450 450 550 250 c 第一条曲线
450 250 350 200 y 第二条曲线
h B 关闭并填充
```

我们使用g运算符来设置填充颜色。这在第60页的“颜色和颜色空间”中进行了解释。
对于第二条曲线，我们使用了y运算符，它类似于c，除了第二个控制点和终点是同一个，所以只有四个需要操作数。

区分填充运算符有两个因素：

* 路径是否在填充前自动关闭。关闭涉及从当前点到当前子路径的起始点添加直线段。可以使用h运算符手动关闭路径。
* 卷绕规则确定在填充自交叉或由多个重叠的子路径组成的对象时所做的选择。
图5-6显示了两个缠绕规则对自相交对象和由两个重叠矩形子路径构成的路径的影响。

图5-6的代码是：
```
100 350 200 200 re
120 370 160 160 re f Non-zero 
400 350 200 200 re
420 370 160 160 re f* Even-odd
150 50 m 150 250 l 250 50 l 50 150 l 350 150 l h f 
550 50 m 550 250 l 650 50 l 450 150 l 750 150 l h f*
```

![](./images/figure%205-5.png)

在这里，我们还使用了re运算符。这将创建一个矩形的闭合路径，给出四个参数：最小x，最小y，宽度和高度。

|操作符|功能|
|---|---|
|n|结束路径没有视觉效果。这用于更改当前剪切路径（请参阅第65页的“剪切”）|
|b|关闭，填充和描边路径（非零缠绕规则）|
|b*|关闭，填充和描边路径（奇数绕组规则）|
|B|填充和描边路径（非零缠绕规则）|
|B*|填充和描边路径（奇数绕组规则） |
|f or F |填充路径（非零缠绕规则）|
|f*|填充路径（偶数奇数绕组规则）|
|S |划出路径|
|s|关闭并抚摸路径|


![](./images/figure%205-6.png)


## Colors and Color Spaces
要更改PDF图形流中的填充或描边颜色，我们需要使用
一个运算符更改当前颜色空间，然后使用另一个运
算符更改颜色。填充和描边颜色空间是分开的 - 例如，
当前填充颜色空间可以是DeviceRGB和描边颜色空间DeviceGray。

在本节中，我们将介绍基本的DeviceGray，DeviceRGB和
DeviceCMYK颜色空间（PDF标准中涵盖了更复杂的颜色空间）：
* DeviceGray颜色空间有一个添加组件，从0.0（黑色）到1.0（白色）不等。
* DeviceRGB色彩空间有三个用于红色，绿色和蓝色的附加组件。它们各自的范围从0.0（例如，没有红色）到1.0（例如，全红色）。
* DeviceCMYK颜色空间有四个减色组件，分别用于青色，品红色，黄色和键（黑色）。它们各自的范围从0.0（无颜料）到1.0（全颜料）。

要更改笔触颜色空间，我们使用CS运算符。要更改填充颜色空间，请改用cs。
然后可以使用SC运算符（具有与当前颜色空间中的分量数量相等的多个操作数）来设置笔划颜色，或者使用sc来设置填充颜色。例如：
```
/DeviceRGB CS Set stroke color space
0.0 0.5 0.9 SC Set color to RGB (0.0, 0.5, 0.9)
```

设备颜色空间有快捷键操作符，可在一次操作中设置当前描边或填充颜色空间以及当前描边或填充颜色。
这些在表5-7中进行了总结。

|操作符 |操作数|功能|
|---|---|---|
|G |1|将笔触颜色空间更改为/DeviceGray并设置颜色|
|g |1|将填充颜色空间更改为/DeviceGray并设置颜色|
|RG |3(R,G,B)|将笔触颜色空间更改为/DeviceRGB并设置颜色|
|rg |3(R,G,B)|将填充颜色空间更改为/DeviceRGB并设置颜色|
|K |4(C,M,Y,K)|将笔触颜色空间更改为/DeviceCMYK并设置颜色|
|k |4(C,M,Y,K)|将填充颜色空间更改为/DeviceCMYK并设置颜色|

当内容流开始时，默认颜色空间为/DeviceGray，默认颜色值为0（全黑），因此我们可以立即使用g运算符：
```
200 250 100 100 re f 0.25 g
300 250 100 100 re f 0.5 g
400 250 100 100 re f 0.75 g
500 250 100 100 re f
```

结果如图5-7所示。

![](./images/figure%205-7.png)

## Transformations 转换
到目前为止，我们已经看到运算符改变了跟随它们的所有运算符的图形状态。
为了允许我们将图形对象与其属性（例如颜色）组合在一起，我们可以将一组运算符与q和Q运算符组合在一起。
q运算符将当前图形状态置于一边。然后可以像往常一样改变状态，涂抹物体等。调用Q运算符时，将恢复先前保存的状态。
q/Q对可以嵌套，一对在另一对内：

```
0.75 g 改为浅灰色填充
250 250 100 100 re f
q 保存图形状态
0.25 g 更改为深灰色填充
350 250 100 100 re f
Q 检索先前的图形状态
450 250 100 100 re f 再次浅灰色
```

流中的q/Q运算符必须形成平衡对（除了在图形流的末尾，可以省略任何剩余的Q运算符）。结果如图5-8所示。

![](./images/figure%205-8.png)

q/Q对的最常见用途之一是隔离坐标变换的影响。我们可以使用cm运算符来更改从用户空间坐标到设备空间坐标的转换。
这被称为电流转换矩阵（CTM）。重要的是，对图形状态的这种改变是由q/Q对隔离的，因为撤消是很复杂的。

cm运算符有六个参数，表示要与CTM组成的矩阵。以下是基本的变换：

* Translation by (dx, dy) is specified by 1, 0, 0, 1, dx, dy
* Scaling by (sx, sy) about (0, 0) is specified by sx, 0, 0, sy, 0, 0
* Rotating counterclockwise by x radians about (0, 0) is specified by cos x, sin x, -sin x, cos x, 0, 0

cm运算符将给定的变换附加到CTM，而不是替换它。要围绕任意点（而不是原点）旋转或缩放，请平移到原点，旋转或缩放，然后平移。

任何图形文本都将对这种变换的数学进行全面讨论。请参见第119页的“PDF和图形文档”。

请考虑以下内容，如图5-9所示：

```
2.0 w
0.75 g
100 100 m 200 200 300 300 400 100 c (a) Untransformed shape
300 100 200 50 y h B
q
0.96 0.25 -0.25 0.96 0 0 cm (b) Rotate counterclockwise by 1/4 radian
100 100 m 200 200 300 300 400 100 c
300 100 200 50 y h B
Q
q
0.5 0 0 0.5 0 0 cm (c) Scale original shape by 0.5 about the origin
100 100 m 200 200 300 300 400 100 c
300 100 200 50 y h B
1 0 0 1 300 0 cm (d) Translate (c) by 300 units in the new space, i.e., 150 units in the original space 
100 100 m 200 200 300 300 400 100 c
300 100 200 50 y h B
Q
```

注意使用q和Q来隔离变换的效果。

![](./images/figure%205-9.png)


## Clipping
我们可以使用以通常方式构建的路径来设置剪切路径。从那时起，将仅显示路径区域内的内容。
这是通过使用W运算符（对于非零路径）或W *运算符（对于偶数奇数路径）来完成的。

运算符与现有剪切路径给定的路径相交，因此它只能用于使剪切区域更小，而不是更大。
剪切路径保持当前路径，因此可以使用例如S运算符来剪切剪切区域的轮廓。W运算符是绘制操作的修饰符，
因此如果我们不想描绘新剪切路径的轮廓，我们必须替换no-op路径绘制操作符n。这是我们定义剪切路径的示例：

`200 100 m 200 500 l 500 100 l h W S`

这里我们定义了一个封闭的三角形路径，使用W设置剪切区域，然后使用S进行描边。
设置此剪切路径然后绘制与图5-2相同的场景的结果如图5-10所示。

![](./images/figure%205-10.png)


## Transparency

PDF具有复杂但复杂的透明机制，可在多个色彩空间中工作，允许不同类型的混合，并支持分组透明度。
我们这里只考虑简单的透明度。

没有特定的透明度运算符，因此我们使用gs运算符从页面资源的/ExtGState条目中的/ca条目加载填充透明度级别。
/ExtGState条目是外部图形状态集合的字典，我们可以使用gs运算符加载它。

对于我们的示例，资源仅包含/ExtGState条目，具有单个状态集合，称为/gs1。它只包含填充透明度的/ca条目：

```
<< /ExtGState 
  << /gs1
    << /ca 0.5 >> Half transparent 
  >>
>>
```

这是相应的内容流：

```
2.0 w Select 2pt line width
/gs1 gs 从外部图形状态中选择/ gs1
0.75 g 选择浅灰色
200 250 m 300 350 400 450 500 250 c
400 250 300 200 y h B
1 0 0 1 100 100 cm
200 250 m 300 350 400 450 500 250 c
400 250 300 200 y h B
```

结果如图5-11所示。定义透明度使得0表示完全透明，1表示完全不透明。可以用/CA代替（或除了）/ca来改变笔划透明度。

![](./images/figure%205-11.png)

## Shadings and Patterns
除了纯色外，PDF还允许使用各种图案来填充和描边对象：
* 平铺模式，在页面上复制模式单元格。
* 着色图案，其中颜色之间的渐变用于填充对象。有许多类型，有许多选项和设置：
```
Function-based
Axial
Radial
Free-form Gouraud-shaded triangle mesh Lattice-form Gouraud-shaded triangle mesh Coons patch mesh
Tensor-product patch mesh
```

我们只考虑轴向和径向阴影。

通过使用cs运算符更改为/Pattern颜色空间，然后使用scn运算符选择命名模式来调用模式。
模式在页面资源的/Pattern字典中按名称列出。例如：

```
/Pattern
  <<
    /GradientShading Our name for the pattern 
    <<
      /Type /Pattern
      /PatternType 2 A shading pattern 
      /Shading
        <<
          /ColorSpace /DeviceGray
          /ShadingType 2 A linear shading
          /Function << /FunctionType 2 /N 1 /Domain [0 1] >>
          /Coords [150 200 450 500] Coordinates of start and end of gradient 
          /Extend [true true]
        >> 
    >>
  >>
```

这定义了轴向阴影图案。我们命名了我们的模式/GradientShading。阴影的图案类型为2.我们的阴影定义为：

* 颜色空间/DeviceGray
* 阴影类型2（轴向）
* 阴影开始和结束的坐标：（150,200）和（450,500）

我们不在这里讨论/Extend或/Function条目。现在调用该模式，并绘制一个形状：
```
/Pattern cs Choose pattern color space for fills 
/GradientShading scn Choose our pattern as a color 
250 300 m 350 400 450 500 550 300 c
450 300 350 250 y h f
```

结果如图5-12所示。

![](./images/figure%205-12.png)

如果我们通过将/ShadingType更改为3来更改为径向着色，并将/Coords条目更改为[400 400 0 400 400 200]  - 内径为0且外半径为200的径向着色均以（400,400）为中心：
```
/Coords [400 400 0 400 400 200] 
/ShadingType 3
```

结果如图5-13所示。

![](./images/figure%205-13.png)

## Form XObjects
在第62页的“转换”中，我们使用q和Q运算符使用各种转换显示单个对象。
但是，我们不得不背诵每次绘制对象的操作。Form XObject允许我们存储一组图形指令，
并以不同的比例和位置重复使用它们（甚至在不同的页面上）。

```
3 0 obj Resources of current page <<
  /XObject << /X1 5 0 R >> Our XObject is called /X1 
>>
endobj
5 0 obj The XObject itself << The XObject dictionary
  /Type /XObject 
  /Subtype /Form 
  /Length 69
  /BBox [0 0 792 612]
>>
stream The XObject content
2.0 w
0.5 g
250 300 m 350 400 450 500 500 300 c 
450 300 350 250 y h B
endstream
endobj
```

上面列表中的对象3是页面的/Resources条目。它的/XObject条目是列出该页面中使用的XObject的字典。
我们已经命名了XObject/X1。对象5是XObject本身。它是一个流，其字典中包含以下条目：

* The /Type of this object is /XObject.
* The /Subtype of this XObject is /Form, distinguishing it as a form XObject.
* The /Length is the length in bytes of the stream, as usual.
* The /BBox entry gives a bounding box for the XObject, in this case the same as the page itself.

流包含用于设置线条和宽度的代码以及形状本身。现在，我们可以使用Do运算符从主内容流中使用XObject，
并将XObject的名称作为操作数：

```
/X1 Do Invoke XObject /X1
0.5 0 0 0.5 0 0 cm Scale by 0.5 about the origin 
/X1 Do Invoke the XObject again, at the new scale
```

结果如图5-14所示。

![](./images/figure%205-14.png)

遇到Do运算符时，保存当前图形状态，XObject中的/Matrix条目（如果有）与CTM连接，
绘制内容（由XObject的/BBox剪切），当前图形状态为恢复。


## Image XObjects

使用单独的对象指定图像，再次存储在页面资源字典的/XObject条目中。因此它们与图形内容流分离，因此可以多次重复使用，甚至跨页面重复使用。
为了指定图像，我们提供图像数据（通常使用诸如JPEG之类的许多机制之一进行压缩），其宽度和高度，以及描述从图像数据到其颜色空间中的值的转换的一些参数。

这是图像XObject的资源条目：
`<< /XObject << /X2 5 0 R >> >>`

这定义了一个名为/X2的图像XObject，其参数为：

```
5 0 obj
<<
  /Type /XObject It's an XObject
  /Subtype /Image It's an image
  /ColorSpace /DeviceGray Thecolorspaceoftheimage.Alsodetermineshowmanycomponentsithas. 
  /Length 8 The length of the stream in bytes, as usual
  /Width 8 Image width in pixels
  /Height 8 Image height in pixels
  /BitsPerComponent 1 Number of bits used for each component
>>
stream
@`pxxp`@ The image data 
endstream
```

为了能够手动输入，我们定义了一个每像素一位的黑白图像，只包含64位数据。
通常，图像在每个方向上将是数百或数千个像素，并且每个组件具有高达16位，具有一个，三个或四个组件。

图像始终映射到用户空间中的方形（0,0）...（1,1），因此我们使用cm运算符将图像缩放到适当的大小和位置：

```
q
1 0 0 1 100 100 cm Translate
200 0 0 200 0 0 cm Scale
/X2 Do Invoke image XObject
Q
q
1 0 0 1 400 100 cm And again with a different position and scale 
100 0 0 100 0 0 cm
/X2 Do
Q
```

结果如图5-15所示。

![](./images/figure%205-15.png)


[目录](./README.md)&nbsp;|&nbsp;[下一章：文本和字体](./chapter6.md)

