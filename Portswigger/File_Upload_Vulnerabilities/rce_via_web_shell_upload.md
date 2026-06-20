# Lab: Remote code execution via web shell upload

## Objectives

To solve the lab, upload a basic PHP web shell and use it to exfiltrate
the contents of the file **/home/carlos/secret.** Submit this secret
using the button provided in the lab banner.

You can log in to your own account using the following
credentials: wiener:peter

## Background

This lab contains a vulnerable image upload function. It doesn't
perform any validation on the files users upload before storing them on
the server's filesystem.

## Tools Used

- Kali Linux

- Burpsuite

## Methodology

I started by creating the php web shell which will help me fetch the
contents of /home/carlos/secret.

![](media/File_Upl1/image1.png)

While proxying traffic through Burp, I logged in to the wiener account
to upload an image

![](media/File_Upl1/image2.png)

I then created an arbitrary image, uploaded it and returned to the
account page.

![](media/File_Upl1/image3.png)

![](media/File_Upl1/image4.png)

After a successful upload, I fitered the http history in Burp by MIME
type

![](media/File_Upl1/image5.png)

In the proxy history, I noticed that my image was fetched using
a GET request to /files/avatars/nature.jpg

![](media/File_Upl1/image6.png)

I then sent it to repeater and used the avatar upload function to upload
the malicious PHP file.

![](media/File_Upl1/image7.png)

In Burp repeater, I changed the path of the GET request to point to the
PHP file then I submitted the request

![](media/File_Upl1/image8.png)

## Results

I noticed that the server had executed my script and returned its output
(Carlos's secret) in the response. I then submitted the secret to solve
the lab.

![](media/File_Upl1/image9.png)

![](media/File_Upl1/image10.png)

## Reflection

Through this lab, I was able to exploit unrestricted file uploads to
deploy a web shell.
