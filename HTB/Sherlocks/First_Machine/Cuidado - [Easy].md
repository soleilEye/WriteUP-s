# Introduction

*Recently, a user triggered multiple alerts after downloading several potentially unwanted applications (PUAs), prompting concern from the security team. To gain deeper insight into the user's activity, the team began monitoring network traffic from their workstation. Their objective is to assess whether the downloads are linked to more serious malware threats.*

*Недавно пользователь запустил несколько предупреждений после загрузки нескольких потенциально нежелательных приложений (PUA), что вызвало обеспокоенность у службы безопасности. Чтобы получить более глубокое представление об активности пользователя, команда начала отслеживать сетевой трафик со своей рабочей станции. Их цель - оценить, связаны ли загрузки с более серьезными вредоносными угрозами*
***
# Investigation

1. What is the victim's IP address?
> ***192.168.1.152***
Просто посмотрел по протоколу HTTP, кто общался. Выделил для себя UA, которые использовались + посмотрел кто общался с внешкой так и нашел ответ на вопрос.
<img width="1655" height="926" alt="Pasted image 20250728194358" src="https://github.com/user-attachments/assets/c76b442c-40e2-4d04-b2d8-678d9e2ea0b6" />

2. What is the IP address of the attacker from whom the files were downloaded?
> ***94.156.177[.]109***

Скорее всего IP-адрес атакующего будет внешним - и IP-адрес источника общался только с одним внешним IP, он и будет нашим злоумышленником
<img width="1667" height="419" alt="Pasted image 20250728194507" src="https://github.com/user-attachments/assets/7d330f6d-acb5-45ff-8fc2-129ba140ec22" />

3. Which malicious file appears to be the first one downloaded?
> ***sh***

Посмотрел в запросы, самый первый к внешнему - это GET-запрос `/sh` + UA содержит wget
<img width="1683" height="719" alt="Pasted image 20250728194612" src="https://github.com/user-attachments/assets/ac00abbc-c2d8-48ec-810b-247e8687d466" />

4. What is the name of the function that the attacker used to download the payload?
> ***dlr***

Cледующий пакет после `/sh` - идет код, его преобразовываем в ASCII текст и копируем в блокнот и видим название функции, то, что это функция для скачивания указывают wget и curl с флагом -O
<img width="2547" height="985" alt="Pasted image 20250728195259" src="https://github.com/user-attachments/assets/cf662ad8-29c3-497c-b4c5-7c9dd9ed0c55" />

5. Which port does the attacker's server use?
> ***80***

<img width="1130" height="43" alt="image" src="https://github.com/user-attachments/assets/50e6fd59-f357-45f0-87cd-bbba1a9c45f9" />

6. The script checks which directories it can write to by attempting to create test files. What is the size of the second test file? (Size in MB)
> ***2***

Команда создаёт (или перезаписывает) скрытый файл .testfile2 размером ровно 2 мегабайта, заполненный нулевыми байтами
<img width="1194" height="115" alt="image" src="https://github.com/user-attachments/assets/052948a9-a52c-4c49-bc7c-3e299218bb41" />

7. What is the full command that the script uses to identify the CPU architecture?
> ***uname -mp***

В этом же скрипте используется утилита `uname`. 
Утилита uname — это командная строка в UNIX-подобных системах, которая выводит базовую системную информацию. Она используется для отображения данных о имени ядра операционной системы, версии и релизе ядра, архитектуре процессора, имени хоста и других характеристиках системы.
<img width="1019" height="69" alt="image1" src="https://github.com/user-attachments/assets/60c2b385-df37-47c2-99e8-1ac83588109c" />

8. What is the name of the file that is downloaded after the CPU architecture is compared with reference values?
> ***x86_64***

<img width="614" height="61" alt="image" src="https://github.com/user-attachments/assets/f30c0b96-ef91-4d9e-9d98-13d2fd0a2697" />

9. What is the full command that the attacker used to disable any existing mining service?
> ***systemctl disable c3pool_miner***

<img width="252" height="40" alt="image" src="https://github.com/user-attachments/assets/044df418-de54-43c0-bebb-1aceb1cd096b" />

10. Apparently, the attacker used a packer to compress the malware. Which version of this packer was used? (Format X.XX)
> ***4.23***

Упаковщик используется для скрытия исходного кода в ВПО. Для того, чтобы узнать использовался ли упаковщик можно обратить внимание на VirusTotal, в разделе Details -> Basic Information
<img width="1698" height="585" alt="image" src="https://github.com/user-attachments/assets/d6f7f822-647b-4500-a832-22b6a9f71dad" />

11. What is the entropy value of unpacked malware?
> ***6.488449***

Нагуглил первый скрипт на тему "Shannon entropy. Simple python script.". Скомпилировал в VSCode и получил результат.

12. What is the file name with which the unpacked malware was submitted on VirusTotal?
> ***redtail.cuidado***

Снова обратил внимание на VirusTotal, в разделе Details -> Names

13. What MITRE ATT&CK technique ID is associated with the main purpose of the malware?
> ***T1496***
