# 第4章 - 文档结构

在本章中，我们将PDF文件的位和字节留下，并考虑逻辑结构。
我们考虑预告片字典，文档目录和页面树。
我们枚举每个对象中的必需条目。
然后我们看看PDF文件中的两个常见结构：文本字符串和日期。

图4-1显示了典型文档的逻辑结构。


## Trailer 字典
这个字典驻留在文件的预告片而不是文件的主体中，是程序想要读取PDF文档时要处理的第一件事。它包含允许读取交叉引用表的条目，从而读取文件的对象。其重要条目总结在表4-1中。


## Document Information字典
## Document Catalog
## Pages and Page Trees
## Text Strings
## Dates
## Putting it together
这是一个手动创建的文本，由*pdftk*使用第2章介绍的方法处理成有效的PDF文件。它是一个三页文档，包含文档信息字典和页面树。图4-3显示了Acrobat Reader中显示的此文档。图4-4是相应的对象图。
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
>> startxref 
0
%%EOF
```
