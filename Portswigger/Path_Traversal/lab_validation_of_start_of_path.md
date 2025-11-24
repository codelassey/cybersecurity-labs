# Lab: File path traversal, validation of start of path

## Objectives
To solve the lab, retrieve the contents of the /etc/passwd file.

## Background

This lab contains a path traversal vulnerability in the display of
product images. The application transmits the full file path via a
request parameter, and validates that the supplied path starts with the
expected folder.

## Tools Used
- Kali Linux
- Burpsuite

## Methodology
I opened Burpsuite to intercept the requests with its built in browser.

![image1](media/Path_Trav5/image1.png)

I started the lab by navigating to `https://0aed00b2042840c980b08f8800090018.web-security-academy.net/`
which has the file path traversal vulnerability with the application
validating that the supplied requests path starts with the expected
folder.

![image2](media/Path_Trav5/image2.png)

From the intercepted requests, I realized that the site loaded quite a
number of images which are are stored on disk in the location
/var/www/images/.

![image3](media/Path_Trav5/image3.png)

I forwarded the request to repeater to observe the result.

![image4](media/Path_Trav5/image4.png)

The response had a 200 OK HTTP status code which was a clear indication
that the image loaded succesfully.

![image5](media/Path_Trav5/image5.png)

Now, I tried to exploit the path traversal vulnerability by modifying
the request that fetches the product image from **/var/www/images/58.jpg**
to **../../../etc/passwd** to observe what happens when the absolute path is
included.

![image6](media/Path_Trav5/image6.png)

After confirming that the application blocks input with traversal
sequences, I then specified the path /var/www/images/ with path
traversal sequences to get out of the specified folder and further
exploit its vulnerabilities.

![image7](media/Path_Trav5/image7.png)

## Results
I succesfully dumped the contents of the etc/passwd file.

![image7](media/Path_Trav5/image7.png)

![image8](media/Path_Trav5/image8.png)

## Reflection
Through this lab, I was able to read arbitrary files on a server running
an application via path traversal although the application blocks
traversal sequences. I noticed that the application transmited the full
file path via a request parameter, and validated that the supplied path
started with the expected folder. Through that, I exploited its
vulnerabilities. Solved! On to the next.
