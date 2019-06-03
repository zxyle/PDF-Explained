Chapter 8: Encrypted Documents
==============================

The file encrypted.pdf is 40-bit encrypted with a blank user password and the
owner password 'fred'. It was produced from the helloworld.pdf file in the
chapter2 directory using the following command:

pdftk helloworld.pdf output encrypted.pdf encrypt_40bit owner_pw fred

The file encrypted-user.pdf is 40-bit encrypted with user password 'charles'
and owner password 'fred'. The document may be printed with just the user
password. It was created with the following command:

pdftk helloworld.pdf output encrypted-user.pdf encrypt_40bit allow Printing
owner_pw fred user_pw charles

(all on one line)

