第 2 章：构建简单的 PDF
=================================

helloworld-source.pdf：手动创建的文件。这还不是有效的 PDF 文件。
您可以使用文本编辑器（例如 Notepad++）打开它。

helloworld.pdf：使用 pdftk 处理过的有效 PDF 文件。此文件可以使用
任意 PDF 阅读器（例如 Adobe Acrobat）打开。您也可以将其加载到文本
编辑器中与之前的文件进行对比，看看 pdftk 添加了什么。

将 helloworld-source.pdf 转为有效 PDF 格式的 pdftk 命令是：

pdftk helloworld-source.pdf output helloworld.pdf