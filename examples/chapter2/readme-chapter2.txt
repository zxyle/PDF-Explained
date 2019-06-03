Chapter 2: Building a Simple PDF
================================

helloworld-source.pdf: The manually-created file. This is not a valid PDF.
Open this in a text editor.

helloworld.pdf: The valid file, processed with pdftk. This may be opened
in a PDF viewer such as Acrobat. You can load it into a text editor to see
what pdftk has added.

The pdftk command was:

pdftk helloworld-source.pdf output helloworld.pdf

