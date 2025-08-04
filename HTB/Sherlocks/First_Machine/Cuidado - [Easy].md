# Introduction

*Recently, a user triggered multiple alerts after downloading several potentially unwanted applications (PUAs), prompting concern from the security team. To gain deeper insight into the user's activity, the team began monitoring network traffic from their workstation. Their objective is to assess whether the downloads are linked to more serious malware threats.*

*Недавно пользователь запустил несколько предупреждений после загрузки нескольких потенциально нежелательных приложений (PUA), что вызвало обеспокоенность у службы безопасности. Чтобы получить более глубокое представление об активности пользователя, команда начала отслеживать сетевой трафик со своей рабочей станции. Их цель - оценить, связаны ли загрузки с более серьезными вредоносными угрозами*
***
# Investigation

1. What is the victim's IP address?
> ***192.168.1.152***
Просто посмотрел по протоколу HTTP, кто общался. Выделил для себя UA, которые использовались + посмотрел кто общался с внешкой так и нашел ответ на вопрос.



2. What is the IP address of the attacker from whom the files were downloaded?
> ***94.156.177[.]109***

3. Which malicious file appears to be the first one downloaded?
> ***sh***

4. What is the name of the function that the attacker used to download the payload?
> ***dlr***

5. Which port does the attacker's server use?
> ***80***

6. The script checks which directories it can write to by attempting to create test files. What is the size of the second test file? (Size in MB)
> ***2***

7. What is the full command that the script uses to identify the CPU architecture?
> ***uname -mp***

8. What is the name of the file that is downloaded after the CPU architecture is compared with reference values?
> ***x86_64***

9. What is the full command that the attacker used to disable any existing mining service?
> ***systemctl disable c3pool_miner***

10. Apparently, the attacker used a packer to compress the malware. Which version of this packer was used? (Format X.XX)
> ***4.23***

11. What is the entropy value of unpacked malware?
> ***6.488449***

12. What is the file name with which the unpacked malware was submitted on VirusTotal?
> ***redtail.cuidado***

13. What MITRE ATT&CK technique ID is associated with the main purpose of the malware?
> ***T1496***
