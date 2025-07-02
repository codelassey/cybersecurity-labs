# Exploring Processes, Threads, Handles, and Windows Registry

## Objectives

In this lab, I explored the processes, threads, and handles using
Process Explorer in the SysInternals Suite. I also used the Windows
Registry to change the value for a registered key.

## Part 1:Exploring Processes

In this part, I explore processes. Processes are programs or
applications in execution. I explored the processes using Process
Explorer in the Windows SysInternals Suite. I then started and observed
a new process.

After downloading the Sysinternals suite from my previous lab, I
navigated to the SysinternalsSuite folder with all the extracted files
and opened procexp.exe. I accepted the Process Explorer License
Agreement when I was prompted. The Process Explorer displays a list of
currently active processes as seen in the screenshot below.

![procexp](images/media/image1.png)

To locate the web browser process, I dragged the **Find Window\'s
Process** icon into the opened web browser window. Chrome was used.

![](images/media/image2.png)

The Chrome process was terminated in the Process Explorer by
right-clicking the selected process and selecting **Kill Process**. The
web browser window was closed afterwards.

![](images/media/image3.png)

I opened Command Prompt and dragged the **Find Windows Process** icon
into the Command Prompt window and located the highlighted Command
Prompt process in Process Explorer. The process for the Command Prompt
is **cmd.exe**. Its parent process is **explorer.exe** process. The
cmd.exe has a child process, **conhost.exe**.

![](images/media/image4.png)

I started a ping in cmd and observed a change in cpu usage for the
cmd.exe process during the ping.

![](images/media/image5.png)

As I reviewed the list of active processes, I found that the child
process **conhost.exe** may be suspicious. To check for malicious
content, I right-clicked **conhost.exe** and selected **Check
VirusTotal**. When prompted, I clicked **Yes** to agree to VirusTotal
Terms of Service.

![](images/media/image6.png)

I then expanded the Process Explorer windowand saw the VirusTotal
column. I clliced the link under the VirusTotal column. The link opens
in chrome with the results regarding the malicious content of
conhost.exe. No vender had flagged it. It is not malicious.

![](images/media/image7.png)

I then killed the cmd.exe process and observed conhost.exe also
terminate through the Process Explorer.

---

## Part 2: Exploring Threads and Handles

In this part, I explored threads and handles. Processes have one or more
threads. A thread is a unit of execution in a process and a handle is an
abstract reference to memory blocks or objects managed by an operating
system. I used Process Explorer (procexp.exe) in Windows SysInternals
Suite to explore the threads and handles.

The Properties window for conhost.exe provides details such as thread
ID, start time, state, priority, kernel time, user time, context
switches, cycles, and ideal processor.

![](images/media/image8.png)

In the Process Explorer, I clicked **View** > select **Lower Pane
View** > **Handles** to view the handles associated with the
conhost.exe process. The handles point to various system resources,
including ALPC ports, desktop, events, files, and kernel objects.

![](images/media/image9.png)

---

## Part 3: Exploring Windows Registry

The Windows Registry is a hierarchical database that stores most of the
operating systems and desktop environment configuration settings.

To access the Windows Registry, I clicked Start > Search for regedit
and select Registry Editor. Clicked **Yes** when asked to allow this app
to make changes.

The Registry Editor has five hives. These hives are at the top level of
the registry.

![](images/media/image10.png)

![](images/media/image11.png)

-   oHKEY_CLASSES_ROOT is actually the Classes subkey of
    HKEY_LOCAL_MACHINE\\Software\\. It stores information used by
    registered applications like file extension association, as well as
    a programmatic identifier (ProgID), Class ID (CLSID), and Interface
    ID (IID) data.

-   oHKEY_CURRENT_USER contains the settings and configurations for the
    users who are currently logged in.

-   oHKEY_LOCAL_MACHINE stores configuration information specific to the
    local computer.

-   oHKEY_USERS contains the settings and configurations for all the
    users on the local computer. HKEY_CURRENT_USER is a subkey of
    HKEY_USERS.

-   oHKEY_CURRENT_CONFIG stores the hardware information that is used at
    bootup by the local computer.

In the previous lab, I had accepted the EULA for Process Explorer. I
navigated to the EulaAccepted registry key for Process Explorer by
clicking to select Process Explorer in HKEY_CURRENT_USER \> Software \>
Sysinternals \> Process Explorer. I scrolled down to locate the key
EulaAccepted. Currently, the value for the registry key EulaAccepted is
0x00000001(1).

![](images/media/image12.png)

I double-clicked EulaAccepted registry key. The value data is set to 1.
The value of 1 indicates that the EULA has been accepted by me.

![](images/media/image13.png)

I changeed the 1 to 0 for Value data. The value of 0 indicates that the
EULA was not accepted and I clicked OK to continue.

![](images/media/image14.png)

The screenshot below shows the value for this registry key in the Data
column after changing from 1 to 0.

![](images/media/image15.png)

I navigated to the folder where I downloaded SysInternals and tried
opening procexp.exe. Below is what I saw...

![](images/media/image16.png)

---

## Reflection

This lab has been incredibly insightful in deepening my understanding of system processes, threads, handles, and the Windows Registry. Using Process Explorer from the SysInternals Suite allowed me to visually explore active processes like cmd.exe and conhost.exe, and observe their relationships, such as parent-child dynamics. Learning to monitor CPU usage during a ping operation and checking for malicious content via VirusTotal gave me practical skills in system monitoring and security. Exploring threads and handles provided a clearer picture of how processes manage execution and system resources, enhancing my grasp of operating system internals. The Windows Registry section showed how configuration settings are stored and modified. Changing the EulaAccepted value and observing its impact on Process Explorer's behavior demonstrated the registry's real-time influence on system applications. Overall, this lab has strengthened my technical skills, boosted my confidence in troubleshooting, and highlighted the importance of understanding system-level operations for effective IT management.
