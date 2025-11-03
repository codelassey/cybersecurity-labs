# Lab: File path traversal, traversal sequences stripped non-recursively

## Objectives
To solve the lab, retrieve the contents of the /etc/passwd file.

## Background

This lab contains a path traversal vulnerability in the display of
product images. The application strips path traversal sequences from the
user-supplied filename before using it.

## Tools Used
- Kali Linux VM
- Burpsuite

##Methodology

I opened Burpsuite to intercept the requests with its built in browser.

![path3](media/Path_Trav3/image1.png)

I started the lab by navigating to `https://0a49007303bac14c80ca2b69009f0042.web-security-academy.net/`
which has the file path traversal vulnerability with traversal sequences
stripped non-recursively.

![path3](media/Path_Trav3/image2.png)

From the intercepted requests, I realized that the site loaded quite a
number of images which perhaps are are stored on disk in the location
/var/www/images/.

![trav3](media/Path_Trav3/image3.png)

I forwarded the request to repeater to observe the result.

![trav3](media/Path_Trav3/Path_Trav3/image4.png)

The response had a 200 OK HTTP status code which was a clear indication
that the image loaded succesfully.

![trav3](media/Path_Trav3/image5.png)

Now, I tried to exploit the path traversal vulnerability by modifying
the request that fetches the product image from 15.jpg to
../../../etc/passwd to observe what happens when the absolute path is
included.

![trav3](media/Path_Trav3/image6.png)

After confirming that the application blocks traversal sequences, I will
use nested traversal sequences \....//....//....//etc/passwd which will revert to simple traversal
sequences when the inner sequence is stripped.

![](media/Path_Trav3/image7.png)

## Results
I succesfully dumped the contents of the etc/passwd file.

![](Path_Trav3/media/image8.png)
![](Path_Trav3/media/image9.png)

## Reflection
Through this lab, I was able to read arbitrary files on a server running
an application via path traversal although the application blocks
traversal sequences. I made use of nested Solved! On to the next.
