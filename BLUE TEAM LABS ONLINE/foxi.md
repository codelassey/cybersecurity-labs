# FOXI - BLUE TEAMS LAB ONLINE (WRITE-UP)

## Background
As an Intelligence Analyst you are tasked with assisting the SOC
Analysts with their investigations, providing additional context and
information.

## Tools

-   Linux CLI

-   Mitre Att&ck Matrix

-   Gnumeric

-   Twitter for OSINT

-   Joesandbox.com

-   MalwareBazaar

## Methodology
![](media/FOXI/image1.png)
![](media/FOXI/image2.png)
![](media/FOXI/image3.png)
![](media/FOXI/image4.png)
![](media/FOXI/image5.png)
![](media/FOXI/image6.png)

Within the terminal, I changed directory to the ThreatFox Export directory. I then used the command `grep 'dot.gif' full_urls.csv | wc -l` to count for the word dot.gif

That showed **567.88 I knew I was getting close so I did same with the `Recent additions.csv` since it was explicitly stated: how many urls across all endpoints?

That gave a total of 4. I then did the maths.

![](media/FOXI/image7.png)
![](media/FOXI/image8.png)

Here, I used malware bazaar for intelligence on the malware associated with that hash value.

![](media/FOXI/image9.png)
![](media/FOXI/image10.png)

To investigate the reference link for that hash, I opened the SHA 256
Hash.csv and searched for 6461 using the ctrl+f command. It returned 1
hash value found on the 10^th^ row. From there, I accessed the reference
link attached.

![](media/FOXI/image11.png)

I then got access to the threat name, the C2 domain, IP, and the domain registrar.

![](media/FOXI/image12.png)
![](media/FOXI/image13.png)

So I did visit the link and checked out the Mitre Att&ck Matrix specifically forcusing on the techniques under the collection tactic.

![](media/FOXI/image14.png)
![](media/FOXI/image15.png)
![](media/FOXI/image16.png)
![](media/FOXI/image17.png)

I then visited the twitter link in the reference to check for the C2 domain.

![](media/FOXI/image18.png)
![](media/FOXI/image19.png)

Here, it was likely users were redirected through phishing messages. A
clue from BTLO on this task lead me to T1566 technique

![](media/FOXI/image20.png)
![](media/FOXI/image21.png)
![](media/FOXI/image22.png)

To be honest, for the questions above, they were the last two remaining. I did all I could and I just could not get a clue. I checked online tosee if there were 
anywriteups on it and even how these two questions were solved with <https://www.joesandbox.com/analysis/680865/0/irxml> , I was just wondering!

For me, I really thought there was an issue so I reported them awaiting what BTLO says.

![](media/FOXI/image23.png)

However for the mean time, the link to that xml showed the name of the docx file and jar file when I searched using ctrl+f. (I will update thiswriteup 
with the feedback I get from BTLO with regards to those two questions)

![](media/FOXI/image24.png)
![](media/FOXI/image25.png)
![](media/FOXI/image26.png)

So the above successfully tricked me as the answer was hidden in plain
sight. As I did throughout the investifgation, I grepped for the discord
keyword which returned so many lines. I got confused as all had the same
structure only to realize it was the url up to /attachments/ that was
needed.

![](media/FOXI/image27.png)
![](media/FOXI/image28.png)
![](media/FOXI/image29.png)
![](media/FOXI/image30.png)
![](media/FOXI/image31.png)
![](media/FOXI/image32.png)
![](media/FOXI/image33.png)
![](media/FOXI/image34.png)

To solve this, I went back to my terminal and grepped for Unknown malware. I then manually searched through the various port numbers till I found port 8001.

![](media/FOXI/image35.png)
![](media/FOXI/image36.png)

For this, I was at the right place at the right time.

![](media/FOXI/image37.png)

For the nickname, I tried log4j which did not work so I made a research to learn the correct nickname.

![](media/FOXI/image38.png)

## Results
![](media/FOXI/image39.png)

## Reflection
Generally, this was an interesting investigation. Having to fall back on basic linux commands to filter CSV files to investigate and provide additional context 
and information behind the events. Loved it!
