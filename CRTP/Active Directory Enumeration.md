

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


Using the Active Directory module


The Active Directory PowerShell module is **a set of cmdlets used for managing Active Directory domains and objects**.

Enumerate all the users in the current domain using the ADModule

~~~
Get-ADUser -Filter *
~~~



Access Control Lists (ACLs) **The Access Control List defines the permissions and rights granted or denied to users or groups**.


enumerate ACLs for the Domain Admins Group:

~~~
Get-DomainobjectAcl -Identity "Domain Admins" -resolveGUIDs -verbose
~~~



Let’s enumerate all domains in the current forest


~~~
Get-ForestDomain -Verbose
~~~

To map all the trusts of the domain:

~~~
Get-DomainTrust
~~~


list only the external trusts in the  forest

~~~
Get-ForestDomain | %{Get-DomainTrust -Domain $_.Name} |
?{$_.TrustAttributes -eq "FILTER_SIDS"}
~~~

To identify external trusts of the dollarcorp domain

~~~
Get-DomainTrust | ?{$_.TrustAttributes -eq "FILTER_SIDS"}
~~~




