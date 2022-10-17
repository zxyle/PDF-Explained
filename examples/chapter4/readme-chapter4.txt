第 4 章：文档结构
==============================

threepages-source.pdf 是手动创建的无效 PDF 文件，存有三页文档信息，
且带有页面旋转和文档信息字典。此文件代码已使用 PDF 注释进行过注释，
您可以在文本编辑器中打开它，并查看有关注释。

threepages.pdf 则是一个完全有效的 PDF 文件，由 threepages-source.pdf 
生成，可以参考以下命令实现：

pdftk threepages-source.pdf output threepages.pdf

它可以在 PDF 查看器中打开。您同样也可以在文本编辑器中打开它，看看 pdftk 
添加了些什么。