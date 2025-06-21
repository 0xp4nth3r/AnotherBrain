
 Check Windows Defender Status

```
Get-MpComputerStatus
```

List AppLocker Rules

```
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

Test AppLocker Policy

```powershell-session
Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone
```

 Patches and Updates
 
 ```powershell-session
Get-HotFix | ft -AutoSize
```

Installed Programs

```powershell-session
Get-WmiObject -Class Win32_Product |  select Name, Version
```

 Listing Named Pipes with PowerShell
 
 ```powershell-session
gci \\.\pipe\
```

 Reviewing LSASS Named Pipe Permissions
 
 ```cmd-session
accesschk.exe /accepteula \\.\Pipe\lsass -v
```


smb share enum 

```
nxc smb 10.10.11.69 -u j.fleischman -d fluffy.htb -p 'J0elTHEM4n1990!' --shares
```

