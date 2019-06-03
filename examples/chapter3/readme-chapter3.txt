Chapter 3: File Structure
=========================

helloworld10.pdf: A PDF file with 10 pages, each page numbered.

helloworld-lin.pdf: A linearized PDF generated from helloworld10.pdf

The linearized file was generated using the pdfopt program which ships
with GhostScript, using the following command line:

pdfopt helloworld10.pdf helloworld-lin.pdf

