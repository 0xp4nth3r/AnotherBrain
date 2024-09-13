
Nmap Scanning:

~~~
nmap 10.10.11.251 -sC -sV
~~~

~~~
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-29 09:33 IST
Nmap scan report for 10.10.11.251
Host is up (0.075s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
|_http-title: pov.htb
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.02 seconds
~~~

![[Pasted image 20240129093658.png]]

~~~
dev.pov.htb
~~~


