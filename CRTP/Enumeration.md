
PowerView is a PowerShell tool to gain network situational awareness on Windows domains. It contains a set of pure-PowerShell replacements for various windows “net *” commands, which utilize PowerShell AD hooks and underlying Win32 API functions to perform useful Windows domain functionality. Several functions for the enumeration and abuse of domain trusts also exist Download script

## Domain

Domains are a hierarchical way of organizing users and computers that work together on the same network

Get Current Domain

~~~
Get-Domain
~~~

Enumerate Other Domains

~~~
Get-Domain -Domain "domain name"
~~~


Get Domain SID

 security identifier (SID) is  unique value that identifies any security entity that the Windows operating system can authenticate

~~~
Get-DomainSID
~~~


## Domain Security policy

A domain security policy : is a security policy that is specifically applied to a given domain or set of computers or drives in a given system. System administrators use a domain security policy to set security protocols for part of a network, including password protocols, access levels and much more Get Domain Policy

~~~
Get-DomainPolicy
~~~

policy configurations of the Domain about system access

~~~
(Get-DomainPolicy)."SystemAccess"
~~~

policy configurations of the Domain about kerberos

~~~
 (Get-DomainPolicy)."kerberospolicy"
~~~


# Domain controller

A domain controller is a server that responds to authentication requests and verifies users on computer networks, keeps all of that data organized and secured

~~~
Get-DomainController
~~~


~~~
Get-DomainController -Domain "domainname"
~~~

# Domain User

A domain user is one whose username and password are stored on a domain controller rather than the computer the user is logging into. When you log in as a domain user, the computer asks the domain controller what privileges are assigned to you. When the computer receives an appropriate response from the domain controller, it logs you in with the proper permissions and restrictions.


~~~
Get-DomainUser
~~~


~~~
Get-DomainUser | select cn
~~~

list of all properties for user

~~~
Get-DomainUser -Identity "username"
~~~


properties of a specific user


~~~
Get-DomainUser -Identity <username> -Properties DisplayName,MemberOf,objectsid,useraccountcontrol | Format-List
~~~

For see the Descriptions of the user by filter

~~~
Get-DomainUser -LDAPFilter "Description=*built*" | Select name,Descritpion
~~~

## Coumputer

Get a list of computers in the current domain

~~~
Get-DomainComputer | select Name
~~~

For particular version of the machine

~~~
Get-DomainComputer -OperatingSystem "*Server 2016"
~~~

to find the computer is live or Not

~~~
Get-DomainComputer -Ping
~~~

~~~
Get-NetComputer -fulldata | select operatingsystem
~~~
Get
## Groups

Get all the groups in the current domain

~~~
Get-DomainGroup | select Name
~~~

~~~
Get-DomainGroup -Domain   targetdomainname
~~~

Get all groups containing the world "admin" in group name

~~~
Get-Domain *admin*
~~~

Get all the member of the Domain Admins group 

~~~
Get-DomainGroupMember -Identity "Domamain Admins" -Recurse
~~~

Get the group membership for a user

~~~
Get-DomainGroup -UserName "student1"
~~~

List All the Local groups On a Machine (needs administrator privs on non-dc machines)

~~~
Get-NetLocalGroup -Computer dcorp-dc -ListGroups
~~~

Get members of all the local group on a machine (needs admin priv on non-dc machines)

~~~
Get-NetlocalGroup -ComputerName dcorp-dc -Recurse
~~~

Get the of the local group "Administrators" on a machines (needs admin priv on non-dc machines)

~~~
Get-NetLocalGroupMember -ComputerName dcorp-dc -GroupName Administrators
~~~

Get actively logged users on a computer (needs local admin rights on the target)

~~~
Get-NetLoggedon -ComputerName servername
~~~

Get locally logged users on a computer (needs local admin rights on the target)

~~~
Get-LoggedonLocal -ComputerName dcorp-dc
~~~

Get the last user on a computer (needs local admin rights on the target)

~~~
Get-lastLoggedon -ComputerName servername
~~~


Find shares on hosts in current domain

~~~
invoke-shareFinder -Verbose
~~~

Find sensitive files on computers in the domain

~~~
Invoke-FileFinder -Verbose
~~~

Get all file servers of the domain 

~~~
Get-NetFileServer
~~~

## Group Policy Object


Get list Of GPO in curent Domain

~~~
Get-DomainGPO
~~~


Get GPOs which use Restricted Groups Or groups.xml for interesting users

~~~
Get-DomainGPOLocalGroup
~~~

Get users which are in a local group of a machine using GPO

~~~
Get-domainGPOComputerLocalGroupMapping -ComputerIdentity dcrop-student1
~~~


Get machines where the given user is member of a specific group

~~~
Get-DomainGPOUserLocalGroupMapping -Identity Student1 -verbose
~~~


## Organizational Units

Get OUs in a domain

~~~
Get-DomainOU
~~~

Get GPO applied on an OU. Read GPOname from gplink attribute from Get-NetOU

~~~
Get-DmainGPO -Identity "{6AC1786C-016F-11D2-945F-00C04FB984F9}"
~~~


## Access Control List

Get the ACLs associated with the specified object

~~~
Get-DomainObjectAcl -SamAccountName student1 -ResolveGUIDS
~~~


Search for interesting ACEs

~~~
Find-InterestingDomainAcl -ResolveGUIDs
~~~

Get the ACLs associated with the Specified path 

~~~
Get-PathAcl -Path "\\\path\\\"
~~~


## Trusts

#### Domain Trust mapping

Get ta list of all domain trusts for the current domain

~~~
Get-DomainTrust
~~~

~~~
Get-DomainTrust -Domain us.dollarcrop.jsf;sdl.local
~~~

#### Forest mapping

Get details about the forest

~~~
Get-Forest
~~~

~~~
Get-Forest -Forest eurocrop.local
~~~

Get the domaind in current forest

~~~
Get-ForestDomain
~~~

~~~
Get-ForestDomain -Forest eurocorp.local
~~~


Get all global Catalogs For the current forest

~~~
Get-ForestGlobalcatalog
~~~

~~~
Get-ForestGlobalCatalog -Forest eurocorp.local
~~~

Map trusts of a forest

~~~
Get-ForestTrust
~~~~

~~~
Get-ForestTrust  -Forest eurocrop.local
~~~


## user Hunting

~~~
Find-LocalAdminAccess -verbose
~~~

Domain For a list of computer 

~~~
Get-NetComputer
~~~

use multi-thread

~~~
Invoke-CheckLocalAdminAccess
~~~


Find computers where a domain admin has sessions

~~~
Find-DomainUserLocation -verbose
~~~

~~~
Find-DomainUserLocation -UserGroupIdentity "RDPUsers"
~~~

Find computers where a domain admin session is available and current user has admin access

~~~
Find-DomainLocation -CheckAccess
~~~

Find computers where a domain admin session is available

~~~
Find-DomainUserLocation -Stealth
~~~



