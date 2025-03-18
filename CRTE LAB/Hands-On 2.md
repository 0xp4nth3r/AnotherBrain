

Import-Module C:\AD\Tools\ADModule-master\Microsoft.ActiveDirectory.Management.dll
Import-Module C:\AD\Tools\ADModule-master\ActiveDirectory\ActiveDirectory.psd1

```
C:\AD\Tools\ArgSplit.bat
```


invishell
~~~
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat
~~~

powerview

~~~
. C:\AD\Tools\PowerView.ps1
~~~

• Enumerate following for the us.techcorp.local domain: 
− Restricted Groups from GPO 
− Membership of the restricted groups 
− List all the OUs 
− List all the computers in the Students OU. 
− List the GPOs 
− Enumerate GPO applied on the Students OU



```
Get-DomainGPOLocalGroup
```

![[Pasted image 20250313124427.png]]

Enumerate Group Policy

```
 Get-DomainGPO
```


```
 Get-DomainGPO -Identity '{FCE16496-C744-4E46-AC89-2D01D76EAD68}'
```

![[Pasted image 20250313130722.png]]

Team NOVA

1.Pradyun
2.dhinesh
3.Jack
4.Mannu
5.homelander
6.Hari


Individual 


7.Subhash
8.subhika
9.subhashini


Alternative : iQuberz


10.Guru
11.Shanjai
12.Madhav
13.Partha
14.Navi
15.prathibha






Waiting !





TEAM 2

