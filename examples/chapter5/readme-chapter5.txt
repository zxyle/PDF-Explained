第 5 章：图形
====================

这些是用于在第 5 章中创建图形的文件。它们是未压缩的 PDF 文件，若
在 Acrobat 无法打开，可以尝试使用浏览器打开。同时我们可以在文本
编辑器中打开以直接了解相关的图形流代码。

graphics-line.pdf 使用 m、l 和 S 运算符绘制直线。

building-lines.pdf 使用 w 运算符来更改线宽和 d 运算符更改破折号模式。

bezier-curve.pdf 使用 c 运算符绘制曲线。

fill-shape.pdf 使用 c、y 和 h 运算符构建闭合形状，并且使用 B 运算符对
其进行填充和描边。

winding-rules.pdf 演示了自相交形状的奇偶缠绕和非零缠绕规则之间的区别。

colors.pdf 中使用了 g 和 re 运算符来绘制灰度块。

stacks.pdf 演示了如何使用 q 和 Q 运算符来隔离状态以改变运算符的影响。

transforms.pdf 使用 cm 运算符来改变比例、位置和旋转的一条路径。

path-clipping.pdf 使用 W 运算符创建剪切路径。

transparent.pdf 演示了如何使用 gs 运算符加载外部图形状态（在本例中为
透明度）。

pattern-shading.pdf 演示了如何使用模式和阴影字典绘制轴向渐变阴影图案。

pattern-shading-2.pdf 与前一个文件是基本相同的，但使用的是径向着色。

form-xobjects.pdf 展示了如何使用 Form XObject 来允许重用的无重复的图形
内容。

image-xobject.pdf 定义了一个位深度为 1 的图像并使用不同的比例来绘制它。
