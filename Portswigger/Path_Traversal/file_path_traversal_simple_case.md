# Lab: File path traversal, simple case

## Objectives
To retrieve the contents of the /etc/passwd file.

## Background
This lab contains a path traversal vulnerability in the display of
product images.

## Tools Used
- Kali Linux VM
- Burpsuite

## Methodology
I opened Burpsuite to intercept the requests with its built in browser.

![path_trav](media/Path_Trav1/image1.png)

I started the lab by navigating to `https://0a9d00b904641cbb867e263000090083.web-security-academy.net/`
which has the file path traversal vulnerability.

![path_trav](media/Path_Trav1/image2.png)

From the intercepted requests, I realized that the site loaded quite a
number of images which perhaps are are stored on disk in the location
/var/www/images/.

![path_trav](media/Path_Trav1/image3.png)

I forwarded the request to repeater to observe the result.

![path_trav](media/Path_Trav1/image4.png)

The response had a 200 OK HTTP status code which was a clear indication
that the image loaded succesfully.

![path_trav](media/Path_Trav1/image5.png)

Now, I exploited the path traversal vulnerability by modifying the
request that fetches the product image from 5.jpg to `../../../etc/passwd`
causing the application to read from the
`/var/www/images/../../../etc/passwd` file path.

![path_trav](media/Path_Trav1/image6.png)

## Results
I succesfully dumped the contents of the etc/passwd file.

![path_trav](media/Path_Trav1/image7.png)
![path_trav](media/Path_Trav1/image8.png)

## Reflection
Through this lab, I was able to read arbitrary files on a server running
an application via path traversal. Solved! On to the next.
