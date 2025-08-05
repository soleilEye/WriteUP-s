# Introduction
> *John Grunewald was deleting some old accounting documents when he accidentally deleted an important document he had been working on. He panicked and downloaded software to recover the document, but after installing it, his PC started behaving strangely. Feeling even more demoralised and depressed, he alerted the IT department, who immediately locked down the workstation and recovered some forensic evidence. Now it is up to you to analyze the evidence to understand what happened on John's workstation.*

> *Джон Грюневальд удалял некоторые старые бухгалтерские документы, когда случайно удалил важный документ, над которым работал. Он запаниковал и скачал программное обеспечение для восстановления документа, но после его установки его компьютер начал вести себя странно. Чувствуя себя еще более деморализованным и подавленным, он предупредил ИТ-отдел, который немедленно заблокировал рабочую станцию и извлек некоторые улики судебной экспертизы. Теперь вам предстоит проанализировать улики, чтобы понять, что произошло на рабочей станции Джона.*
***
# Evidence
We have:
* disk artifacts\disk_artifacts.ad1
* memory capture\memory.vmsn and memory capture\memory.vmem
* packet capture\network.pcapng
***
# Tools
I would use for disk artifacts - [FTK Imager](www.exterro.com/ftk-product-downloads/ftk-imager-4-7-3-81)
For memory capture - [Volatility](https://github.com/volatilityfoundation/volatility3)
For packet capture - [Wireshark](https://www.wireshark.org/download.html)
***
# Questions and Answers

1. What is the build version of the operating system?
> *19041*

2. What is the computer hostname?
> *DESKTOP-38NVPD0*

3. What is the name of the downloaded ZIP file?
> *Data_Recovery.zip*

4. What is the domain of the website (including the third-level domain) from which the file was downloaded?
> *praetorial-gears.000webhostapp.com*

5. The user then executed the suspicious application found in the ZIP archive. What is the process PID?
> *484*

6. What is the full path of the suspicious process?
> *C:\Users\John\Downloads\Data_Recovery\Recovery_Setup.exe*

7. What is the SHA-256 hash of the suspicious executable?
> *C34601c5da3501f6ee0efce18de7e6145153ecfac2ce2019ec52e1535a4b3193*

8. When was the malicious program first executed?
> *2023-05-30 02:06:29*

10. How many times in total has the malicious application been executed?
> *2*

11. The malicious application references two .TMP files, one is IS-NJBAT.TMP, which is the other?
> *IS-R7RFP.TMP*

12. How many of the URLs contacted by the malicious application were detected as malicious by VirusTotal?
> *4*

13. The malicious application downloaded a binary file from one of the C2 URLs, what is the name of the file?
> *puk.php*

14. Can you find any indication of the actual name and version of the program that the malware is pretending to be?
> *FinalRecovery v3.0.7.0325*

***
# My Thoughts and Investigation Process
## Task1. What is the build version of the operating system?
Alright, we have memory dump, so i can use volatility and his modules to answer this question. First of all I use module `windows.info.Info`. Why I decide pick windows module? Because I opened disk artifacts and see what we have Windows on dump :D

<img width="701" height="946" alt="image" src="https://github.com/user-attachments/assets/0bac7bd3-c2e0-429f-9897-4d814db9f5a8" />

Information from volatility module:

```
Kernel Base     0xf8073e400000
DTB     0x1ad000
Symbols file:///X:/DFIR/volatility3/volatility3/symbols/windows/ntkrnlmp.pdb/CA8E2F01B822EDE6357898BFBF862997-1.json.xz
Is64Bit True
IsPAE   False
layer_name      0 WindowsIntel32e
memory_layer    1 VmwareLayer
base_layer      2 FileLayer
meta_layer      2 FileLayer
KdVersionBlock  0xf8073f00f368
Major/Minor     15.19041
MachineType     34404
KeNumberProcessors      2
SystemTime      2023-05-30 02:09:03+00:00
NtSystemRoot    C:\Windows
NtProductType   NtProductWinNt
NtMajorVersion  10
NtMinorVersion  0
PE MajorOperatingSystemVersion  10
PE MinorOperatingSystemVersion  0
PE Machine      34404
PE TimeDateStamp        Wed Jan  4 04:27:11 1995
```

<img width="1076" height="506" alt="image" src="https://github.com/user-attachments/assets/79113e4f-51d9-4308-b932-0b979d59a3c8" />

Alright, we would work with:

* OS: Windows 10
* Kernel version: 10.0.**19041**
* Architecture: x64 (Is64Bit True, MachineType 34404 corresponds to x64)
* System path: C:\Windows

** We have Kernel version and his build is last digits. So this is the answer. Let's go next. **

## Task2. What is the computer hostname?
We have network logs, so I decide to look for them and scrolling a packets i saw a NBNS (NetBIOS protocol) packets. 
NBNS (NetBIOS Name Service) is a protocol that resolves (searches for) NetBIOS names to IP addresses on local networks. It is part of the NetBIOS over TCP/IP (NBT) protocol family and operates on UDP port 137.

<img width="1225" height="874" alt="image" src="https://github.com/user-attachments/assets/a81e51e7-d20b-4540-82f2-391985f6f7ea" />

And we got Desktop name - DESKTOP-38NVPD0. We continue.

## Task3. What is the name of the downloaded ZIP file?
So, I opened disk artefacts and goes to the folder `Downloads`. The full path is `C:\Users\John\Downloads\Data_Recovery.zip`

<img width="1332" height="359" alt="image" src="https://github.com/user-attachments/assets/02ed3930-aed2-483a-9da0-b358de0b51e9" />

So the answer is - Data_Recovery.zip

## Task4. What is the domain of the website (including the third-level domain) from which the file was downloaded?
Abother one :D Goes to network logs. We have ip.address of our victim machine. The logic is simple - we visited sites and application protocol is HTTP or HTTPS to view some information from site. Sooo, we can config our filter: `http and ip.src == 192.168.116.133`. And then search some mention about *Data_Recovery* and we see some logs:

<img width="1300" height="1147" alt="image" src="https://github.com/user-attachments/assets/7066be7e-8bbb-408e-bc70-b685c1e98701" />

And if we go to Hypertext Transfer Protocol section we can see Full Request URI:
`hXXp[://]praetorial-gears[.]000webhostapp[.]com/wp-content/uploads/2023/05/Data_Recovery.zip`
FQDN of the website is - `praetorial-gears[.]000webhostapp[.]com` (without defang)

## Task5. The user then executed the suspicious application found in the ZIP archive. What is the process PID?
Well, well, well. We need return to start and remember about Volatillity. We can use modules `windows.pslist` or `windows.pstree` or `windows.psxview` all of them. So, I choose `windows.pslist` and output of module below:

<img width="1196" height="386" alt="image" src="https://github.com/user-attachments/assets/c84311c8-f177-41b3-851c-fda91121c273" />

<img width="1400" height="1299" alt="image" src="https://github.com/user-attachments/assets/cdca0eb8-6c9c-487e-8356-0f88b4b32526" />

From FTK Imager we grab info about process in Archive, soo it's `Recovery_Setup.exe` and we found it pid - it's 484.

## Task6. What is the full path of the suspicious process?
So from Task3 and FTK Imager the full path is `C:\Users\John\Downloads\Data_Recovery\Recovery_Setup.exe`

<img width="1384" height="376" alt="image" src="https://github.com/user-attachments/assets/60457d21-6950-4a23-acda-33107f8b8abc" />

## Task7. What is the SHA-256 hash of the suspicious executable?
From FTK Imager we can Extract Files to our Machine and calculate hashsum with powershell OR we can use Wireshark and download file. I prefer FTK Imager. So:
1) I downloaded file

<img width="1408" height="714" alt="image" src="https://github.com/user-attachments/assets/ca039d36-1524-479d-a541-7c1aac015590" />

2) Launch Powershell comandlet `Get-FileHash` with arguments:
`Get-FileHash .\Recovery_Setup.exe -Algorithm SHA256`

<img width="1191" height="127" alt="image" src="https://github.com/user-attachments/assets/472ebb79-67ff-40b3-8729-21b8e1246f9f" />

And we got SHA256 Hash: `C34601c5da3501f6ee0efce18de7e6145153ecfac2ce2019ec52e1535a4b3193`

## Task8. When was the malicious program first executed?
I think that Volatillity could have been used, but I decided to go a more in-depth route and practice at the same time :)
There are prefetch files in Windows OS.
Prefetch files (*.pf) are special files that are created and used by the Windows operating system to speed up the launch of applications and the system itself.
Where are they stored: in the folder: C:\Windows\Prefetch
What can be found in a Prefetch file:
- The name of the program's executable file.
- The number of times the application has been launched.
- Lists of files and folders that the program has accessed.
- Time stamps of the first and last launches

The first thing I did was export the C:\Windows\Prefetch directory from FTK

<img width="474" height="202" alt="image" src="https://github.com/user-attachments/assets/ffe022a6-70c7-4af0-a779-4cec80a06be2" />

Then I used a tool from the Zimmerman collection - pecmd to extract infromation from .pf files to csv
`.\PECmd.exe -d "X:\DFIR\HTB\Sherlocks\Trojan\disk artifacts\prefetch" --csvf prefetch.csv --csv "X:\DFIR\HTB\Sherlocks\Trojan\disk artifacts"`

<img width="1219" height="205" alt="image" src="https://github.com/user-attachments/assets/ad61f2ea-aa22-4de8-a00a-24904570432d" />

After that I used the TimeLine Explorer utility, also from the Zimmerman collection, and found the first launch of the utility. Yes, it's a difficult path, but an exciting one ;)

<img width="2546" height="207" alt="image" src="https://github.com/user-attachments/assets/423611a3-f1c3-453b-814b-99f8cb4abe33" />

And the answer is - `2023-05-30 02:06:29`

## Task9. How many times in total has the malicious application been executed?
From TimeLine Explorer utility - it was 2 times the malicious application been executed

<img width="2043" height="96" alt="image" src="https://github.com/user-attachments/assets/ee4e3d3b-a048-4b3f-939b-45c837dac9fb" />

## Task10. The malicious application references two .TMP files, one is IS-NJBAT.TMP, which is the other?
From TimeLine Explorer utility - So all these actions were not so useless :D

<img width="1237" height="226" alt="image" src="https://github.com/user-attachments/assets/7b803bb2-7499-4776-86d5-3ab43ff91492" />

Answer - `IS-R7RFP.TMP`

## Task11. How many of the URLs contacted by the malicious application were detected as malicious by VirusTotal?
It's simple. We need grab hash from Task7 and go to `www.virustotal.com`. When we paste our hash go to the `Relations` tab

<img width="1755" height="760" alt="image" src="https://github.com/user-attachments/assets/8b2935c1-03a4-4d95-8115-abb46084ea05" />

And then we got an answer - `4`

## Task12. The malicious application downloaded a binary file from one of the C2 URLs, what is the name of the file?
Well, let's go to the network logs again. Well, I go to `File -> Export Objects -> HTTP` 

<img width="426" height="534" alt="image" src="https://github.com/user-attachments/assets/5be2b5db-10ca-4036-983e-db7e575d44f2" />

And found some interesting packets (and some interesting files) from external IP 45[.]12[.]253[.]75

<img width="1354" height="511" alt="image" src="https://github.com/user-attachments/assets/d162cc69-f3d2-44c2-b2c1-69c5aae3204a" />

So, the dropped binary file was `puk.php`

## Task13. 

Use the hash(Task 7) of the malicious file to find reports on the malware.Refer to `https://bazaar.abuse.ch/sample/c34601c5da3501f6ee0efce18de7e6145153ecfac2ce2019ec52e1535a4b3193/#intel`

I used this report `https://hybrid-analysis.com/sample/c34601c5da3501f6ee0efce18de7e6145153ecfac2ce2019ec52e1535a4b3193/682d13fe13c92cca6605eb22` and section Strings 

<img width="1882" height="897" alt="image" src="https://github.com/user-attachments/assets/fb2fb266-85b6-4fcc-be8b-5c0edb3b12cd" />
