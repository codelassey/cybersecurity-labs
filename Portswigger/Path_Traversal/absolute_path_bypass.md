# Lab: File path traversal, traversal sequences blocked with absolute path bypass

## Objectives
To retrieve the contents of the /etc/passwd file.

## Background
This lab contains a path traversal vulnerability in the display of
product images. The application blocks traversal sequences but treats
the supplied filename as being relative to a default working directory.

## Tools Used
- Kali Linux VM
- Burpsuite

## Methodology
I opened Burpsuite to intercept the requests with its built in browser.

![trav2](media/Path_Trav2/image1.png)

I started the lab by navigating to
`https://0a9600a1035a612a80eb7b7000b10073.web-security-academy.net/` which
has the file path traversal vulnerability with traversal sequences
blocked with absolute path bypass.

![](media/Path_Trav2/image2.png)
From the intercepted requests, I realized that the site loaded quite a
number of images which perhaps are are stored on disk in the location
/var/www/images/.

![trav2](media/Path_Trav2/image3.png)

I forwarded the request to repeater to observe the result.

![path](media/Path_Trav2/image4.png)

The response had a 200 OK HTTP status code which was a clear indication
that the image loaded succesfully.

![path](media/Path_Trav2/image5.png)

Now, I tried to exploit the path traversal vulnerability by modifying
the request that fetches the product image to observe what happens when the absolute path is
included.

![path](media/Path_Trav2/image6.png)

After confirming that the application blocks traversal sequences, I will
use the absolute path bypass method to read world-readable files like
/etc/passwd

![path](media/Path_Trav2/image7.png)

## Results
I succesfully dumped the contents of the etc/passwd file.

![path2](media/Path_Trav2/image8.png)
![path2](media/Path_Trav2/image9.png)

## Reflection
Through this lab, I was able to read arbitrary files on a server running
an application via path traversal with absolute path bypass due to the
application blocking traversal sequences. Solved! On to the next.
