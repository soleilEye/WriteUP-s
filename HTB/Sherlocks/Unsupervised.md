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
> **
5. What is the detach timestamp for the USB in UTC?
> **
6. Which folder did he copy to the USB?
> **
7. There were subfolders in the folder that was copied. What is the name of the first subfolder? (Alphabetically)
> **
8. Eddie opens some files after copying them to the USB. What is the name of the file with the .xlsx extension Eddie opens?
> **
9. Eddie opens some files after copying them to the USB. What is the name of the file with the .docx extension Eddie opens?
> **
10. What was the volume name of the USB?
> **
11. What was the drive letter of the USB?
> **
12. I hope we can find some more evidence to tie this all together. What is Eddie's last name?
>
13. There was an unbranded USB in the USB list, can you identify it's manufacturer’s name?
> **
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

### Task5. What is the detach timestamp for the USB in UTC?

### Task6. Which folder did he copy to the USB?

### Task7. There were subfolders in the folder that was copied. What is the name of the first subfolder? (Alphabetically)

### Task8. Eddie opens some files after copying them to the USB. What is the name of the file with the .xlsx extension Eddie opens?

### Task9. Eddie opens some files after copying them to the USB. What is the name of the file with the .docx extension Eddie opens?

### Task10. What was the volume name of the USB?

### Task11. What was the drive letter of the USB?

### Task12. I hope we can find some more evidence to tie this all together. What is Eddie's last name?

### Task13. There was an unbranded USB in the USB list, can you identify it's manufacturer’s name?
