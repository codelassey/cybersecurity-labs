# Lab: File path traversal, traversal sequences stripped with superfluous URL-decode

## Objectives
To solve the lab, retrieve the contents of the /etc/passwd file.

## Background

This lab contains a path traversal vulnerability in the display of
product images. The application blocks input containing path traversal
sequences. It then performs a URL-decode of the input before using it.

## Tools Used
- Windows Host
- Burpsuite

## Methodology
I opened Burpsuite to intercept the requests with its built in browser.

![url_enc](media/Path_Trav4/image1.png)

I started the lab by navigating to `https://0ab5001c047bf5a380b70dad00b40019.web-security-academy.net/`
which has the file path traversal vulnerability withtraversal sequences
stripped with superfluous URL-decode.

![url_enc](media/Path_Trav4/image2.png)

From the intercepted requests, I realized that the site loaded quite a
number of images which perhaps are are stored on disk in the location
/var/www/images/.

![meth](media/Path_Trav4/image3.png)

I forwarded the request to repeater to observe the result.

![meth](media/Path_Trav4/image4.png)

The response had a 200 OK HTTP status code which was a clear indication
that the image loaded succesfully.

![meth](media/Path_Trav4/image5.png)

Now, I tried to exploit the path traversal vulnerability by modifying
the request that fetches the product image from 20.jpg to
../../../etc/passwd to observe what happens when the absolute path is
included.

![meth](media/Path_Trav4/image6.png)

After confirming that the application blocks input with traversal
sequences, I URL-encoded the traversal sequences
\....//....//....//etc/passwd or ../../../etc/passwd which will revert
to simple traversal sequences when the inner sequence is stripped.

![meth](media/Path_Trav4/image7.png)
![meth](media/Path_Trav4/image8.png)

I then realized that the application performs a url-decode before using
it and hence decoded the path traversal sequences.

![meth](media/Path_Trav4/image9.png)

I then had to encode the decoded URL.. thus, double encoding of the path
traversal sequences.

![meth](media/Path_Trav4/image10.png)
![meth](media/Path_Trav4/image11.png)

## Results
I succesfully dumped the contents of the etc/passwd file.

![meth](media/Path_Trav4/image12.png)
![meth](media/Path_Trav4/image13.png)

## Reflection
Through this lab, I was able to read arbitrary files on a server running
an application via path traversal although the application blocks
traversal sequences. I made use of double URL-encoding to bypass the
initial decoding the application does to identify traversal sequences.
Solved! On to the next.
