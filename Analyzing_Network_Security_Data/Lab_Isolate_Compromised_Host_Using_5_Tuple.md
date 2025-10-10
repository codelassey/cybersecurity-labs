# Lab Report: Analyzing logs to Isolate a Compromised Host Using 5-Tuple

## Objectives

In this lab, I will review logs that were gathered during the
exploitation of a documented vulnerability to determine the compromised
hosts and the contents of the compromised file.

## Background

The 5-tuple is used by IT administrators to identify requirements for
creating an operational and secure network environment. The components
of the 5-tuple include a source IP address and port number, destination
IP address and port number, and the protocol in use in the data payload.
This is the protocol field of the IP packet header. After the attack,
the users no longer have access to the file named **confidential.txt**.
Now I will review the logs to determine how the file was compromised.

## Tools Used
- Security Onion virtual machine
- Sguill
- Wireshark
- capME
- ELK Stack

## Methodology

### Part 1: Investigating Security alerts with Sguill

I launched and logged in to the Security Onion VM with the default
credentials.

![part1](media/Tuples/image1.png)

Next, Sguil was opened and logged into. I then selected all the
interfaces and started SGUIL.

![part1](media/Tuples/image2.png)
![part1](media/Tuples/image3.png)
![part1](media/Tuples/image4.png)

Reviewing the events listed in the Event Message column, I realized one
of these messages was **GPL ATTACK_RESPONSE id check returned root**.
This message indicated that root access may have been gained during the
attack. The host at 209.165.200.235 returned root access to
209.165.201.17. The alert ID was 5.1 as seen below

![part1](media/Tuples/image5.png)

I selected the Show Packet Data and Show Rule checkboxes to view each
alert in more detail.

![part1](media/Tuples/image6.png)

Right-clicking the alert ID 5.1, I selected **Transcript** to get more
information of the session.

![part1](media/Tuples/image7.png)
![part1](media/Tuples/image8.png)

### Part 2: Using Wireshark to Investigate the session using PCAPS

Here, I selected the alert that provided me with the transcript from the
previous step. Right-clicking the alert ID 5.1 and selecting Wireshark,
its main window displays three views of the packet.

![part2](media/Tuples/image9.png)
![part2](media/Tuples/image10.png)

To view all packets in the TCP conversation, I right-clicked the second
packet (could be any packet though) and I selected Follow \> TCP Stream
which gave me similar information from the entire conversation. After
reviewing the information, I closed Wireshark in order to pivot to
another tool for analysis.

![part2](media/Tuples/image11.png)

### Part 3: Pivoting to Kibana for more information

With the same alert highlighted in Sguil, I right-clicked on the source
Ip address and selected **Kibana IP Lookup** \> **SrcIP**. This opened
up Kibana which will allow me to use elasticsearch to investigate the
logs for the alert.

![part3](media/Tuples/image12.png)
![part3](media/Tuples/image13.png)

By default in the Kibana dashboard, it shows alerts for the last 24
hours but the event of interest happened on June 11th 2020.

![part3](media/Tuples/image14.png)

Hence, I clicked on the last 24 hours and selected an absolute timerange
for the month of June which showed 136 events.

![part3](media/Tuples/image15.png)
![part3](media/Tuples/image16.png)

In the displayed results, there was a list of different data types. I
was told that the file **confidential.txt** was no longer accessible. In
the Sensors - Sensors and Services, ftp and ftp-data were present in the
list, hence I will use that to determine if FTP was used to steal the
file.

I filtered for bro_ftp by hovering over the empty space next to the
count of bro_ftp data types and selected + to filter for only FTP
related traffic.

![part3](media/Tuples/image17.png)
![part3](media/Tuples/image18.png)

I then scrolled down to the **All Logs** section. There were two entries
listed. I identified the source and destination IP addresses involved in
the FTP traffic. I then expanded and viewed both entries for more
information.

![part3](media/Tuples/image19.png)
![part3](media/Tuples/image20.png)

Within the same log entry, I scrolled up back to the **alert_id**
field and clicked the link which opened the packet capture in capME so I
could view the session.

![part3](media/Tuples/image21.png)

Now that I had verified that the attacker has used FTP to copy the
content of the file confidential.txt unto the server and then deleted it
from the targe, I needed to determine was was the content of the test
file. Hence, I navigated to the top of the dashboard and selected
**Files** under the Zeek Hunting heading in the left panel. This will
allow me to review the types of the files that were logged.

![part3](media/Tuples/image22.png)
![part3](media/Tuples/image23.png)

Scrolling to the **Files - Source**, I identified HTTP and FTP file sources.
I filtered for FTP_DATA by hovering over the empty space next to the
Count for FTP_DATA and I clicked the **+** sign. I then scrolled down to
the log.

![part3](media/Tuples/image24.png)
![part3](media/Tuples/image25.png)

I expanded to take a quick look. But then I clicked on the alert id to
open the pcap in capME.

![part3](media/Tuples/image26.png)

## Results

Looking into the signature rule for the IDS that was triggered for the
attack, I reviewed the IP header information, the Transport layer
information and the data itself which showed the root user id and root
group id. This confirmed suspicious traffic going across the network.

![results](media/Tuples/image27.png)

The transcript showed the source requests in red and the destination
responses in blue. Analyzing it, I saw that someone had terminal access
to the system and issued an id command which returned user id 0 for root
and group id 0 for root. A number of commands were issued such as
hostname which returned the server name, ifconfig to look at the network
interface information, whoami, the shadow file which had all the user
account hashes and passwords and more confirming that after the attacker
had access to the system, he run his initial recon to identify the
system and the privilages he had before elevating them. Further, the
attacker tried to echo an account name into the root etc shadow file and
also into the passwd file. See screenshots below..

![results](media/Tuples/image28.png)
![results](media/Tuples/image29.png)
![results](media/Tuples/image30.png)
![results](media/Tuples/image31.png)

In Wireshark, I observed the same results as seen earlier. Here I could
tweak to view the entire conversation or just the request and the
replies.

![results](media/Tuples/image32.png)

After opening the event in Kibana, I identified several log types like
connections, dns, ssh connections, ftp connections and more.

![results](media/Tuples/image33.png)

Inspecting the FTP traffic which had two log entries, I observed the IP
addresses, the location, the event type, the ftp argument from
192.168.0.11 which was a PORT command which was connecting to the FTP
server. Actually, the connection was successful. In the second log, I
got some more information. The ftp argument and command showed that
there was a file and the STORE command uploaded the file to the server
which was very interesting.

![results](media/Tuples/image34.png)
![results](media/Tuples/image35.png)

Reviewing the transcript for the transactions between the attacker and
the target through capME, I drew some solid conclusions as follows: In
the session, the username **analyst** and password **cyberops** was used
to log in to the ftp server successfully. Two commands which were
**PORT** and **STORE** were both executed successfully.

![results](media/Tuples/image36.png)
![](media/Tuples/image37.png)

Reviewing the types of the files that were logged, thus from the Files
dashboard, I identified several file types such as text file, gifs, etc.
Scrolling to the Files - Source, I identified HTTP and FTP file sources.
After filtering for FTP and opening the pcap in capME, I concluded that
the server was an FTP server and the host that uploaded the document
into the server as well as the exact contents of the file.

![results](media/Tuples/image38.png)
![results](media/Tuples/image39.png)

## Reflection

I completed a lab yesterday where the investigation I did was on the
HTTP and DNS traffic which was used to exfiltrate the contents of the
confidential text files. From today's analysis, I have confirmed that
this event took place a day before the data exfltration. With all the
information gathered so far, the password for the username analyst
should be changed throughout the network to prevent further unauthorized
access.
