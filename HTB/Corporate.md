
Nmap Scanning:

~~~
nmap 10.10.11.246 -sC -sV 
~~~

~~~
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-27 14:43 IST
Nmap scan report for corporate.htb (10.10.11.246)
Host is up (0.49s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    OpenResty web app server 1.21.4.3
|_http-server-header: openresty/1.21.4.3
|_http-title: Corporate.HTB

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 90.92 seconds
~~~

![[Pasted image 20240127144835.png]]


