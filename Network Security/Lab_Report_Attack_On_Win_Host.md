# Investigating an Attack on a Windows Host

## Objectives
I aim to investigate the attack with Sguil and use Kibana and wireshark to dive deeper and
investigate Alerts. Specifically, I will obtain information on the
specific time of the attack, the host computer that was infected and
what that computer was infected with. This lab is based on an exercise
from the website **malware-traffic-analysis.net** which is an excellent
resource for learning how to analyze network and host attacks.

## Scenario
In March 2019, network security monitoring tools alerted that a Windows
computer on the network was infected with malware.

## Tools Used
-   Security Onion
-   Kibana
-   Sguill
-   Wireshark
-   NetworkMiner

## Methodology

### Part 1: Investigating the Attack with Sguil

I started and logged in to the Security Onion VM

![image](media/win_host/image1.png)

I then launched Sguill from the desktop and logged in.

![image](media/win_host/image2.png)
![image](media/win_host/image3.png)

I then located the group of alerts from 19 March 2019

![image](media/win_host/image4.png)

In Sguil, I clicked on the first of the alerts on 3-19-2019 with the
alert ID 5.439. I also checked the Show Packet Data and Show rule
checkboxes to examine the packet header information and the IDS
signature rule related to the alert.

![image](media/win_host/image5.png){

By right-clicking on the alert ID, I then pivoted to Wireshark to learn
more about the alert.

![image](media/win_host/image6.png)

In Sguil, I selected the second of the alerts on 3-19-2019,
right-clicked the **Alert ID 5.440** and selected **Transcript**.

![image](media/win_host/image7.png)

Looking at the reply (in the transcript) represented in red, I looked
for the file signature in the initial few characters of the file.

![image](media/win_host/image8.png)

The initial few characters of the file was MZ hence I made a google
search on the file signature to learn more about it. In fact, I saw that
it was indeed a windows executable.

![image9](media/win_host/image9.png)

I closed the transcript and used Wireshark to export the executable file
for malware analysis (File \> Export Objects \> HTTP...). saving the
file to the analyst\'s home folder.

![image10](media/win_host/image10.png)
![image11](media/win_host/image11.png)

I opened a terminal in Security Onion VM and created a SHA256 hash from
the exported file as seen in the command showed below..

![image12](media/win_host/image12.png)

I then copied the file hash and submitted it to the Cisco Talos file
reputation center at <https://talosintelligence.com/talos_file_reputation>

![image13](media/win_host/image13.png)

Back to Sguill, I selected the alert with **Alert ID** **5.480.** The
IDS signature detected the Remcos RAT based on the binary hex codes at
the beginning of that communication.

![image](media/win_host/image14.png)

I then right-clicked on the event id and selected to view the transcript
from which I made a few deductions.

![image](media/win_host/image15.png)

Looking through the remaining alerts for March 19, 2019, the alert IDs
5.482, 5.483, 5.485, 5.497, 5.509, 5.521 and 5.533 were related to a
second executable file download.

![image16](media/win_host/image16.png)

The file named **f4.exe** was downloaded from the server at **217.23.14.81:80**

![image17](media/win_host/image17.png)

Next, I exported the file, computed its hash and submitted the hash to
at Cisco Talos File Reputation Center to see if it matches known
malware.

![image18](media/win_host/image18.png)
![image19](media/win_host/image19.png)

I observed the remaining three alerts from 3-19-2019 by looking at the
header information in Show Packet Data, the IDS signature in Show Rule,
and the Alert ID Transcripts.

![image20](media/win_host/image20.png)

### PART 2: Using Kibana to Investigate Alerts

I logged in to Kibana and set the time range to March 1, 2019 to March
31, 2019.

![image21](media/win_host/image21.png)
![image22](media/win_host/image22.png)

I then clicked the total log count over time timeline to narrow focus to
the specific time range of the attack. From below, I noticed the attack
unfolded at midnight.

![image23](media/win_host/image23.png)

Upon scrolling, the variety of log types related to the attack were
many. About 15 of them.

![image24](media/win_host/image24.png)
![image25](media/win_host/image25.png)

I scrolled to the NIDS Alert summary, clicked the magnifier to filter
the second alert from source IP 31.22.4.176. It was that same alert I
came across earlier on the blacklist malicious SSL certificate from the
Sguill interface.

![image26](media/win_host/image26.png)

I then scrolled down to All logs and clicked to expand the first log in
the list with source IP 31.22.4.176 trying to figure out from which
country did that alert triggered.

![image27](media/win_host/image27.png)

I now scrolled back to the top of the page and clicked **HTTP** in the
dashboard link under Zeek Hunting in Navigation. I then scrolled through
the HTTP dashboard to jot down some useful info on the attack.

![image28](media/win_host/image28.png)

After exploring HTTP, it was time for DNS.

![image29](media/win_host/image29.png)

## Results

According to Sguil, the timestamps for the first and last of the alerts
that occurred on **3-19-2019** were **01:45:03** and **04:54:34** respectively.

The timestamps of all the alerts on 3-19-2019 were somehow interesting to
me because, it showed suspicious activities which occurred in under 4
hours particularly at midnight. Well, I must also admit the fact that
majority of the alerts took place in a very short time .. some less than
5 minutes actually.

After pivoting to Wireshark from the first Alert ID associated with the
attack, I made a few observations of which I'll put in the table below
the image.

![image30](media/win_host/image30.png)
![image31](media/win_host/image31.png)
![image32](media/win_host/image32.png)

| Field                              | Details                                                                 |
|------------------------------------|-------------------------------------------------------------------------|
| **Source IP & Port**               | 10.0.90.215:52609                                                       |
| **Destination IP & Port**          | 10.0.90.9:53                                                            |
| **Protocol**                       | UDP                                                                     |
| **Traffic Type**                   | Dynamic DNS update request and response                                 |
| **IDS Alert Message**              | Alert udp $EXTERNAL_NET any -> $HOME_NET 53, msg: "ET POLICY DNS Update from External net" |
| **Analyst Thought**                | Not legitimate suspicious communication; likely due to misconfigured IDS. The communication was a dynamic DNS update from an internal host to the DNS server. |
| **Hostname**                       | Bobby-Tiger-PC                                                          |
| **Domain Name**                    | littletigers.info                                                       |
| **Source Host IP**                 | 10.0.90.215                                                             |

From the transcript of the second alert, the souce and destination IP
addresses and port numbers were **10.0.90.215:49204** and
**209.141.34.8:80** respectively.

![image33](media/win_host/image33.png)

Looking at the request only, (shown in blue below) it was clearly a
**GET** request for the executable **test1.exe**.

![image34](media/win_host/image34.png)

After submitting the file hash for test1.exe to Cisco Talos, Talos did
recognize the file hash and identified it as a malware, specifically
**win32 trojan-spy-agent**.

![image35](media/win_host/image35.png)
![image36](media/win_host/image36.png)

After right-clicking to view the transcript of the event with id 5.480,
I realized that the destination port of the **encrypted** communication
was port 2404 which isn't really a well-known port. Now, I asked
myself.. what at all is the Remos RAT Checkin 23? So I looked it up on
google. From the results, Remcos fully meant Remote Control and
Surveillance which is just a for of RAT (Random Access Trojan). The type
of communication possibly being transferred would be user credentials,
keystroke or user information (That's the tool's overall capabilities)
and the reasons why detection was bypassed is shown in the screenshots
below (the third)

![image37](media/win_host/image37.png)
![image38](media/win_host/image38.png)
![image39](media/win_host/image39.png)

Results from Cisco Talos after submitting the hash for the second
executable showed that it is a **PE32 executable**, **trojan
downloader** and its AMP detection name **Win.Dropper.Cridex::1201**

![image40](media/win_host/image40.png)

All the last three alerts from March 19 2019 were alerts triggered by a
blacklisted malicious SSL certificate called **Dridex**.

![image41](media/win_host/image41.png)

Back in Kibana when I scrolled to All logs and clicked to expand the
first log in the list with source IP 31.22.4.176, I observed the geo
country and city location to be United Kingdom and Newcastle upon Tyne
respectively.

![image42](media/win_host/image42.png)

Viewing the zeek conections by destination country, I discovered several
other countries as seen in the screenshot below.

![image43](media/win_host/image43.png)

The HTTP dashboard from the Zeek Hunting in navigation showed four log
counts from United States and Netherlands. The URIs for the files that
were downloaded include f4.exe, /ncsi.txt, /pki/crl/products/CSPCA.crl,
and /test1.exe.

![image44](media/win_host/image44.png)
![image45](media/win_host/image45.png)
![image46](media/win_host/image46.png)

I needed answers to what really **CSPA.crl** and **ncsi.txt** files were so I
made a google search and had results shown below

![image47](media/win_host/image47.png)

The DNS queries visualization showed a number of DNS queries. Well some
of the domains seemed malicious to me hence I submitted the domain
toptoptop1.online to virustotal where 13/93 security vendors flagged the
domain as malicious.

![image48](win_host/media/image48.png)
![image49](win_host/media/image49.png)

## Conclusion
This investigation successfully identified a multi-stage malware attack
on Windows host Bobby-Tiger-PC (10.0.90.215) within the
littletigers.info domain that occurred on March 19, 2019, between
01:45:03 and 04:54:34 UTC. Using Security Onion\'s Sguil and Kibana, I
traced the attack progression from initial malware downloads (test1.exe
and f4.exe) through the establishment of Remcos RAT C2 communications
and encrypted Dridex SSL connections to international threat actors.

### Key Takeaway: 
This lab reinforced the critical importance of using
multiple analysis tools in correlating IDS alerts, performing deep
packet inspection, analyzing file hashes, and leveraging threat
intelligence to build a complete picture of an attack. The ability to
pivot between different data sources and tools proved essential for
understanding not just that an attack occurred, but how it unfolded and
what specific threats compromised the system. I have practiced
investigative skills which are fundamental for effective incident
response in real-world security operations.
