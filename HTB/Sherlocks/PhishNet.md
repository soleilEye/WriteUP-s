# Introduction

> An accounting team receives an urgent payment request from a known vendor. The email appears legitimate but contains a suspicious link and a .zip attachment hiding malware. Your task is to analyze the email headers, and uncover the attacker's scheme.

> Бухгалтерский отдел получает срочный запрос на оплату от известного поставщика. Электронное письмо выглядит законным, но содержит подозрительную ссылку и вложение в формате .zip, скрывающее вредоносное ПО. Ваша задача — проанализировать заголовки письма и раскрыть схему злоумышленника.

---

# Evidence
We have:

* File with extension .eml
* Attachment with .zip extension
* Suspicious link

# Tools

* Microsoft Outlook for checking headers
* 7Zip for unzip attachment file

---

Questions and Answers

1.  What is the originating IP address of the sender?
>   45.67.89[.]10
2.  Which mail server relayed this email before reaching the victim?
> 
3.  What is the sender's email address?
> 
4.  What is the 'Reply-To' email address specified in the email?
> 
5.  What is the SPF (Sender Policy Framework) result for this email?
> 
6.  What is the domain used in the phishing URL inside the email?
> 
7.  What is the fake company name used in the email?
> 
8.  What is the name of the attachment included in the email?
> 
9.  What is the SHA-256 hash of the attachment?
> 
10.  What is the filename of the malicious file contained within the ZIP attachment?
> 
11.  Which MITRE ATT&CK techniques are associated with this attack?
> 
---
# My thoughts and investigation process
## Q1. What is the originating IP address of the sender? (Какой IP-адрес отправителя?)

For answer this question we need explore headers of our mail. Let's open email and navigate to headers. 
<img width="1226" height="953" alt="06-18-26-212437" src="https://github.com/user-attachments/assets/dc7675fe-092d-40cd-abc5-9be394cd4294" />

<img width="1066" height="523" alt="06-18-26-212459" src="https://github.com/user-attachments/assets/90393c27-e5bf-4b03-a817-a5613233b191" />

So... The Originating-IP is 45.67.89[.]10, because the X-Originating-IP and X-Sender-IP have this value

> The The X-Sender-IP (or X-Originating-IP) is a non-standard email header field that records the public IP address of the device used to submit an email 

<img width="1834" height="1196" alt="image" src="https://github.com/user-attachments/assets/f89cbedf-7d4d-4c27-8110-3f15fbd566aa" />

The answer for 1 question - 45.67.89[.]10

## Q2. Which mail server relayed this email before reaching the victim? (Какой почтовый сервер переслал это электронное письмо до того, как оно достигло жертвы?)

We need check "Received" headers. They give as information about mail servers which relayed email. For undestand this information we need start from bottom and go up.







