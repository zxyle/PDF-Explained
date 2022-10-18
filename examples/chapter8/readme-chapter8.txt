第 8 章：加密文档
===============================
encrypted.pdf 是使用空用户密码和所有者密码“fred”经过 40 位加密的。
它是使用第 2 章示例中出现的 helloworld.pdf 文件生成的，可以使用以下
pdftk 命令进行创建：

pdftk helloworld.pdf output encrypted.pdf encrypt_40bit owner_pw fred

文件 encrypted-user.pdf 已经被用户密码“charles”和所有者密码“fred”
进行过 40 位加密。当只输入用户密码时，文档将可以被用于打印。此文件是
使用以下 pdftk 命令创建的：

pdftk helloworld.pdf output encrypted-user.pdf encrypt_40bit allow Printing owner_pw fred user_pw charles