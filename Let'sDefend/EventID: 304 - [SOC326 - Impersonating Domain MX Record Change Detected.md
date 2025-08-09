# Description
The MX record of a suspicious domain was changed, suggesting potential phishing activity.
 # Basic Information
```
EventID: 304
Event Time: Sep, 17, 2024, 12:05 PM
Rule: SOC326 - Impersonating Domain MX Record Change Detected
Level: Security Analyst
Source Address: no-reply@cti-report.io
Destination Address: soc@letsdefend.io
Subject: Impersonating Domain MX Record Change Detected
Trigger Reason: The MX record of a suspicious domain was changed, suggesting potential phishing activity.
Domain: letsdefwnd[.]io
Mx_record: mail.mailerhost[.]net
Device Action: Allowed
```
# Investigation

Алерт выглядит следующим образом:

<img width="913" height="1161" alt="image" src="https://github.com/user-attachments/assets/8573a8d1-73f5-4bfa-8438-cd6b262afcfc" />

<img width="674" height="1167" alt="image" src="https://github.com/user-attachments/assets/9424805b-c651-46a7-a5fd-59855b42c548" />

Phishing Domain: letsdefwnd‍[.]io
Following phishing domain's MX record information is discovered: mail‍.mailerhost‍.net 

IP Addresses: 
- 72.14.178[.]174;
- 45.33.30[.]197;
- 72.14.185[.]43;
- 173.255.194[.]134;
- 45.79.19[.]196;
- 45.56.79[.]23;
- 96.126.123[.]244;
- 45.33.20[.]235;
- 45.33.18[.]44;
- 45.33.2[.]79;
- 198.58.118[.]167;
- 45.33.23[.]183 

Согласно информации с VT: сам по себе домен letsdefwnd‍[.]io не является вредоносным, однако ассоциируемые с ним IP-адреса да. 

<img width="1507" height="775" alt="image" src="https://github.com/user-attachments/assets/a3f53a91-93ef-40d1-be61-c0e6bbf9e8e3" />

Репутация первого IP-адреса с ресурса AbuseIPDB: 59 репортов, последний был год назад. ISP - Linode. 
Linode - это хостинг, который предоставлят услуги в сфере VPS/VDS.  

<img width="1272" height="979" alt="image" src="https://github.com/user-attachments/assets/c0757b31-fa5c-4767-93b0-4ab15112509a" />

Далее я проверил все IP-адреса, они все принадлежат Linode. 

Далее покопаем в сторону домена letsdefwnd‍[.]io. По информации из WHOIS домен неактивен, но существует (т.е. занят):

<img width="1314" height="766" alt="image" src="https://github.com/user-attachments/assets/4fb81807-3b00-4df3-90b2-d7e7cfc88fc5" />

У домена отствуют какие-либо DNS записи, DMARC записей. Также не было найдено каких-либо MX записей

<img width="1523" height="396" alt="image" src="https://github.com/user-attachments/assets/9d8630f6-7db8-43c7-958b-25e8115b980e" />

Однако домен крайне схож с оригинальньным letsdefend.io, что может говорить о возможной подготовке атаки типа фишинг. (Что в дальнейшем подтвердится)

Теперь посмотрим как выглядело письмо отправленное с этого домена (на что сработал алерт):

<img width="2053" height="843" alt="image" src="https://github.com/user-attachments/assets/bfec5f8c-a00f-4d68-a809-b5095b32f0ec" />

Зафиксирован переход на ресурс, указанный в письме:

<img width="1838" height="568" alt="image" src="https://github.com/user-attachments/assets/aa9d64b6-cfe4-44ad-a0eb-d101ef03c081" />

Также по сетевым логам фиксируется активность, связанная с IP-адресом: 45.33.23[.]183

Также в это же время (Sep 18 2024 01:32:13) фиксируется запуск процесса C:\Program Files\Google\Chrome\Application\chrome.exe.
Запускаемая команда: "C:\Program Files\Google\Chrome\Application\chrome.exe" --field-trial-handle=1976,i,16069116420983613989,6383740150246034948,262144 /prefetch:8
Родительский процесс: C:\Windows\explorer.exe
Действие выполнялось под учетной записью: EC2AMAZ-ILGVOIN\LetsDefend

<img width="1932" height="368" alt="image" src="https://github.com/user-attachments/assets/573e04ca-42e6-42f2-a715-db397156edb7" />

Далее рассмотрим подробно письмо - мы видим логотип организации, под которое пытается мимикрировать наш отправитель + подпись в теле письма. Также в теле письма есть кнопка, которая перенаправляет на домен отправителя. Используется стандартная схема с выйгрышем определленого приза (в данном случае ваучера на площадке). Данное письмо указывает на подтвержденную атаку типа Фишинг.

<img width="611" height="595" alt="image" src="https://github.com/user-attachments/assets/8bcf1d4f-0c11-4756-8a65-2624df02e688" />

# Mitigations:
1) Заблокировать на МСЭ сетевое взаимодействие с доменом letsdefwnd‍[.]io (72.14.178[.]174, 45.33.30[.]197, 72.14.185[.]43, 173.255.194[.]134, 45.79.19[.]196, 45.56.79[.]23, 96.126.123[.]244, 45.33.20[.]235, 45.33.18[.]44, 45.33.2[.]79, 198.58.118[.]167, 45.33.23[.]183);
2) При наличии возможности изолировать хост mateo (172.16.17.162) от сети и заблокировать учетную запись EC2AMAZ-ILGVOIN\LetsDefend на время расследования инцидента;
3) Проверить состояние штатного антивирусного ПО и проверку актуальных баз;
4) Провести сканирование штатным антивирусным ПО 
5) Направить информацию о разделегировании домена в ответственную компания по регистрации домена (однако такой информации у нас нет)

# IOC's:
```
- 72.14.178[.]174;
- 45.33.30[.]197;
- 72.14.185[.]43;
- 173.255.194[.]134;
- 45.79.19[.]196;
- 45.56.79[.]23;
- 96.126.123[.]244;
- 45.33.20[.]235;
- 45.33.18[.]44;
- 45.33.2[.]79;
- 198.58.118[.]167;
- 45.33.23[.]183

Phishing Domain: letsdefwnd‍[.]io

MX record: mail‍.mailerhost‍.net

Malicious sender: voucher@letsdefwnd.io

Malicious URL from e-mail: hXXp[://]letsdefwnd[.]io/
```

<img width="1386" height="343" alt="image" src="https://github.com/user-attachments/assets/2038f769-a7f9-4881-a172-e2224eed6ca8" />
