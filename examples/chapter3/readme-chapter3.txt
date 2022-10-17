第 3 章：文件结构
==========================

helloworld10.pdf：一份 10 页的 PDF 文件，每页都有页码。

helloworld-lin.pdf：由 helloworld10.pdf 生成的线性化 PDF 文件。
（译者注：线性化 PDF 文件是 PDF 文件的一种特殊格式，可以通过
Internet 更快地进行查看。）

使用附带 GhostScript 的 pdfopt 程序可以生成线性化 PDF 文件，
可以参考以下命令行实现：

pdfopt helloworld10.pdf helloworld-lin.pdf