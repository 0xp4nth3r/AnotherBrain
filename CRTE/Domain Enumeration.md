. 
~~~
# Users
Get-NetUser
# To list a specific property of all the users, say, samaccountname
Get-ADUser -Filter * | Select -ExpandProperty samaccountname

# computers
Get-NetComputer
# Now, to enumerate member computers in the domain we can use Get-ADComputer
Get-ADComputer –Filter * | select –expand name

# domain admin
Get-NetDomain
# To see attributes of the Domain Admins group
Get-ADGroup -Identity 'Domain Admins' -Properties *

# See Attributes of the Domain Admins Group
Get-NetGroup -GroupName "Domain Admins" -FullData

# To enumerate members of the Domain Admins group
Get-ADGroupMember -Identity 'Domain Admins'
# Get Members of the Domain Admins group
Get-NetGroupMember -GroupName "Domain Admins"

# To enumerate members of the Enterprise Admins group:
Get-ADGroupMember -Identity 'Enterprise Admins' -Server techcorp.local

# Forest
Get-NetDomainTrust
Get-NetForestDomain | Get-NetDomainTrust

# If bidirectional
Get-NetForestDomain -Forest eurocorp.local -Verbose | Get-NetDomainTrust

#Kerberoastable users
Get-NetUser -SPN 

# Bloodhound
. .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All,LoggedOn

# SQL
Import-Module .\PowerUpSQL.psd1
Get-SQLInstanceDomain
~~~

