# Introduction
> *The incident happened around 4:30 PM on Friday, "The Last day of the week" at the Accounts/Marketing department of "Finance XYZ" company. There weren't many people in the department, and the remaining were not paying much attention to their surroundings as they were getting ready to head home. After the weekend on Monday while reviewing the security cam footage member of an IT team saw "Eddie" a new intern in the Accounts/Marketing department, plugging a USB into an unauthorized computer (containing sensitive financial and marketing documents), interacting with computer and unplugging the USB before heading out. As the incident happened 2 days ago, and not having enough knowledge of what Eddie did the security team use caution while asking around and gathering intel to avoid causing suspicion. The only information they were able to find out was that Eddie had a "Toshiba" USB. You are provided with a partial image of the “unauthorized computer" as well as a list of important documents, to investigate what he did and if he stole something sensitive or not?*

> *Инцидент произошел около 16:30 вечера в пятницу, "В последний день недели", в отделе бухгалтерского учета и маркетинга компании "Finance XYZ". В отделе было не так уж много людей, а остальные не обращали особого внимания на окружающее, поскольку готовились отправиться домой. После выходных, в понедельник, просматривая запись с камеры видеонаблюдения, член ИТ-команды увидел, как "Эдди", новый стажер в отделе бухгалтерского учета / маркетинга, подключал USB-порт к неавторизованному компьютеру (содержащему конфиденциальные финансовые и маркетинговые документы), взаимодействовал с компьютером и отключил USB-порт перед уходом. Поскольку инцидент произошел 2 дня назад, и не имея достаточных знаний о том, что натворил Эдди, команда безопасности проявляла осторожность, расспрашивая окружающих и собирая информацию, чтобы не вызвать подозрений. Единственная информация, которую им удалось выяснить, заключалась в том, что у Эдди был USB-накопитель "Тошиба". Вам предоставляется частичное изображение “несанкционированного компьютера", а также список важных документов для расследования того, что он сделал и украл ли он что-то конфиденциальное или нет?*
***
# Evidence

We have: 
* Important Files and Folders.txt
* Image.ad1
***
# Tools
FTK Imager for Image.ad1
***
# Questions and Answers
1. Find out the time zone of victim PC. (UTC+xx:xx)
> *UTC+05:00*
2. Employees should be trained not to leave their accounts unlocked. What is the username of the logged in user?
> *MrManj*
3. How many USB storage devices were attached to this host in total?
> *3*
4. What is the attach timestamp for the USB in UTC?
> *2024-02-23 11:37:50*
5. What is the detach timestamp for the USB in UTC?
> *2024-02-23 11:39:12*
6. Which folder did he copy to the USB?
> *Documents*
7. There were subfolders in the folder that was copied. What is the name of the first subfolder? (Alphabetically)
> *Business Proposals*
8. Eddie opens some files after copying them to the USB. What is the name of the file with the .xlsx extension Eddie opens?
> *Business Leads.xlsx*
9. Eddie opens some files after copying them to the USB. What is the name of the file with the .docx extension Eddie opens?
> *Proposal Brnrdr ltd.docx*
10. What was the volume name of the USB?
> *RVT-9J*
11. What was the drive letter of the USB?
> *E*
12. I hope we can find some more evidence to tie this all together. What is Eddie's last name?
> *Homer*
13. There was an unbranded USB in the USB list, can you identify it's manufacturer’s name?
> *Shenzhen SanDiYiXin Electronic Co.,LTD*
***
# My Thoughts and Investigation Process
### Task1. Find out the time zone of victim PC. (UTC+xx:xx)
We got SYSTEM hive from `C:\Windows\System32\Config` folder let's use `Reg Explorer` for analysis and answer the question

<img width="709" height="593" alt="image" src="https://github.com/user-attachments/assets/9c7315f6-9fa5-4664-ab79-65d587538366" />

So from registry `SYSTEM\ControlSet\Control\TimeZoneInformation` we got info about timezone - it's **West Asia Standard Time**.

<img width="1875" height="350" alt="image" src="https://github.com/user-attachments/assets/f05381ab-2f35-4596-be84-9e90746eea6a" />

<img width="871" height="473" alt="image" src="https://github.com/user-attachments/assets/cd1897ad-ea1d-4f56-ac41-d4a321373581" />

From Internet we got info that **The West Asia Standard Time is at UTC +05:00.**

### Task2. Employees should be trained not to leave their accounts unlocked. What is the username of the logged in user?
Weird question, because it's was one person who got userprofile on `C:\Users`

<img width="337" height="66" alt="image" src="https://github.com/user-attachments/assets/ddfc9dd2-8f9a-4076-89ab-9680d164dded" />

So, the answer is **MrManj**

### Task3. How many USB storage devices were attached to this host in total?

From Registry we got information about device identification:
- SYSTEM\CurrentControlSet\Enum\USBSTOR
- SYSTEM\CurrentControlSet\Enum\USB

It's **3** USB storage devices were attached to host, because I compared Serial Number from both pat's and only 3 was USB storage devices, others was a VMware or generic drivers

<img width="2062" height="793" alt="image" src="https://github.com/user-attachments/assets/aa31c2a4-4bc6-4b87-af63-9bbe1a5661e8" />

<img width="2081" height="525" alt="image" src="https://github.com/user-attachments/assets/f4a9ba9b-f729-458a-b710-4284d269b7f8" />

Plus, I think if we compare the VID and PID of each device, we can figure out which device is storage and which is not.

### Task4. What is the attach timestamp for the USB in UTC?

To answer this question, you need to refer to the registry SYSTEM. Full path is - `SYSTEM\ControlSet\Enum\USBSTOR\<Device Name>\<Device ID>\Properties\{83da6326-97a6-4088-9453-a1923f573b29}\0066`

<img width="2520" height="773" alt="image" src="https://github.com/user-attachments/assets/f7460fa5-1f0e-43f1-8203-06cedbc8a961" />

The answer is - `2024-02-23 11:37:50`.

### Task5. What is the detach timestamp for the USB in UTC?

To answer this question, you need to refer to the registry SYSTEM. Full path is - `SYSTEM\ControlSet\Enum\USBSTOR\<Device Name>\<Device ID>\Properties\{83da6326-97a6-4088-9453-a1923f573b29}\0067`.

<img width="1285" height="798" alt="image" src="https://github.com/user-attachments/assets/5aab5ce7-d918-44a0-8281-22d08d16acfb" />

The answer is - `2024-02-23 11:39:12`

### Task6. Which folder did he copy to the USB?

Here I didn't quite get it, so I asked for a hint on the Discord server. And one guy told me to look at this path `AppData\Roaming\Microsoft\Windows\Recent`. It contained .lnk files. I found articles that .lnk files can contain this information, since they are created at the time of transmission, but when I mounted the device to perform the search, I got zero results. Perhaps I did something wrong.

<img width="1752" height="1176" alt="image" src="https://github.com/user-attachments/assets/99a68e9a-b89d-4844-8dfb-9cab7eb3a659" />

### Task7. There were subfolders in the folder that was copied. What is the name of the first subfolder? (Alphabetically)

<img width="1560" height="1083" alt="image" src="https://github.com/user-attachments/assets/096117a8-6872-43a0-a290-d2c6dd8da76e" />

### Task8. Eddie opens some files after copying them to the USB. What is the name of the file with the .xlsx extension Eddie opens?

From this .lnk files we got answer for question - **Business Leads.xlsx**

### Task9. Eddie opens some files after copying them to the USB. What is the name of the file with the .docx extension Eddie opens?

From this .lnk files we got answer for question - **Proposal Brnrdr ltd.docx**

### Task10. What was the volume name of the USB?

**RVT-9J**

### Task11. What was the drive letter of the USB?

**E**

### Task12. I hope we can find some more evidence to tie this all together. What is Eddie's last name?

We got this information from thumbcache. 
Thumbcache — это системный кэш миниатюр (эскизов) изображений в операционной системе Windows, предназначенный для ускорения отображения превью изображений и других файлов в Проводнике. Windows создает и хранит уменьшенные копии изображений, чтобы при повторном просмотре папок не пересчитывать эти миниатюры заново, что значительно повышает скорость и удобство работы.
Full path - `C:\Users\<Имя_пользователя>\AppData\Local\Microsoft\Windows\Explorer`
Для просмотра будем использовать ПО - thumbcache_viewer

<img width="462" height="463" alt="image" src="https://github.com/user-attachments/assets/5c9af8b6-1695-4ed5-b240-fc83e2fa3f1b" />

The answer is - **Homer**. 

### Task13. There was an unbranded USB in the USB list, can you identify it's manufacturer’s name?

From Registry we got VID and PID this unbranded USB: VID_346D&PID_5678

<img width="1572" height="102" alt="image" src="https://github.com/user-attachments/assets/5bb87d30-4ab6-4fc0-b4f3-2ad8f6679f49" />

<img width="1582" height="111" alt="image" src="https://github.com/user-attachments/assets/cdeed504-b9c1-43de-bcd6-d667b1ee6e35" />

From [Internet](https://the-sz.com/products/usbid/index.php?v=0x346D) we got information that vendor of this device is - Shenzhen SanDiYiXin Electronic Co.,LTD
0x346D, 0x5678: Shenzhen SanDiYiXin Electronic Co.,LTD Disk 2.0

<img width="1192" height="940" alt="image" src="https://github.com/user-attachments/assets/636d110a-ac6f-4404-be25-d3fbb0796a3f" />
