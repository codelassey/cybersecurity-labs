# Lab Report: Examining Telnet and SSH in Wireshark

## Objectives

In this lab, I will examine a Telnet and an SSH Session with Wireshark

## Background

I will configure a router to accept SSH connectivity and use Wireshark
to capture and view Telnet and SSH sessions. This will demonstrate the
importance of encryption with SSH.

## Tools Used

- CyberOps Workstation virtual machine

- Wireshark

## Methodology

### Part 1:Examining a Telnet Session with Wireshark

I started and logged in to the CyberOps Workstation VM. I then opened a
terminal window and started Wireshark.

![terminal](media/TELSSH/image1.png)

Then I started the capture on the Loopback interface

![capture](media/TELSSH/image2.png)

In a new terminal window, I started a telnet session to the localhost

![telnet_session](media/TELSSH/image3.png)

After providing the credentials for the telnet connection, I stopped the
Wireshark capture.

![stop_capture](media/TELSSH/image4.png)

I then entered **telnet** in the filter field and clicked **Apply** to
display only telnet-related traffic.

![filter](media/TELSSH/image5.png)

I right-clicked one of the Telnet lines in the Packet list section of
Wireshark, and from the drop-down list, I selected Follow \> TCP Stream.

![stream](media/TELSSH/image6.png)

After I had finished reviewing the Telnet session in the Follow TCP
Stream window, I clicked Close and exited the Telnet session by typing
exit in the terminal.

![close_session](media/TELSSH/image7.png)

### Part 2:Examining an SSH Session with Wireshark

I started another Wireshark capture using the Loopback: lo interface
then I established an SSH session with the localhost.

![ssh](media/TELSSH/image8.png)

![ssh](media/TELSSH/image9.png)

I applied an SSH filter on the Wireshark capture data by entering ssh in
the filter field and clicking Apply.

![filter](media/TELSSH/image10.png)

I right-clicked one of the SSHv2 lines in the Packet list section of
Wireshark, and in the drop-down list, I selected the Follow \> TCP
Stream.

![stream](media/TELSSH/image11.png)

After examining the SSH session, I closed Wireshark. At this point, I
went to get some chilled malt haha cos three labs done in a row was
solid.

## Results

The Follow TCP Stream window displays the data for the Telnet session
with the CyberOps Workstation VM. The entire session is displayed in
plaintext, including my password. I noticed that the username that I
entered was displayed with duplicate characters. This is caused by the
echo setting in Telnet to allow me to view the characters that I typed
on the screen.

![examining_telnet](media/TELSSH/image12.png)

Examining the Follow TCP Stream window of the SSH session, I noticed
that the data had been encrypted and was unreadable.

![examining_ssh](media/TELSSH/image13.png)

## Reflection

The Secure Shell Protocol (I mean SSH) allows users to communicate with
remote systems securely by encrypting the communications. This prevents
any sensitive information, such as usernames and passwords, from being
captured during the transmission.
