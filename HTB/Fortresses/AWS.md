
Nmap Scanning:

~~~
nmap 10.13.37.15 -sC -sV
~~~

~~~
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-07 16:32 IST
Nmap scan report for jobs.amzcorp.local (10.13.37.15)
Host is up (0.30s latency).
Not shown: 986 closed tcp ports (conn-refused)
PORT     STATE    SERVICE       VERSION
53/tcp   open     domain        Simple DNS Plus
80/tcp   open     http          Apache httpd 2.4.52 ((Win64))
| http-title:     AWS Console Dashboard -  Sign IN 
|_Requested resource was http://jobs.amzcorp.local/login
| http-server-header: 
|   Apache/2.4.52 (Win64)
|_  gunicorn
88/tcp   open     kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-07 11:02:37Z)
135/tcp  open     msrpc         Microsoft Windows RPC
139/tcp  open     netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open     ldap          Microsoft Windows Active Directory LDAP (Domain: amzcorp.local0., Site: Default-First-Site-Name)
445/tcp  open     microsoft-ds?
464/tcp  open     kpasswd5?
593/tcp  open     ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open     tcpwrapped
2179/tcp open     vmrdp?
3268/tcp open     ldap          Microsoft Windows Active Directory LDAP (Domain: amzcorp.local0., Site: Default-First-Site-Name)
3269/tcp open     tcpwrapped
9917/tcp filtered unknown
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: -20s
| smb2-time: 
|   date: 2024-02-07T11:03:21
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 128.72 seconds
~~~



![[Pasted image 20240207163358.png]]


![[Pasted image 20240207163506.png]]


![[Pasted image 20240207163608.png]]

First Flag 

~~~
AWS{S1mPl3_iD0R_4_4dm1N}
~~~


~~~
curl -s http://jobs.amzcorp.local/api/v4/status | jq  
~~~

