
Nmap scanning:

~~~
nmap 10.10.11.229 -sC -sV 
~~~



~~~
starting Nmap 7.94 ( https://nmap.org ) at 2023-10-26 08:16 EDT
Stats: 0:05:49 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.65% done; ETC: 08:22 (0:00:00 remaining)
Nmap scan report for 10.10.11.229
Host is up (0.31s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE    SERVICE  VERSION
22/tcp   open     ssh      OpenSSH 9.0p1 Ubuntu 1ubuntu7.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 9d:6e:ec:02:2d:0f:6a:38:60:c6:aa:ac:1e:e0:c2:84 (ECDSA)
|_  256 eb:95:11:c7:a6:fa:ad:74:ab:a2:c5:f6:a4:02:18:41 (ED25519)
80/tcp   open     http     Apache httpd 2.4.54 ((Ubuntu))
|_http-title: Zipping | Watch store
|_http-server-header: Apache/2.4.54 (Ubuntu)
1805/tcp filtered enl-name
6112/tcp filtered dtspc
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 352.42 seconds
~~~

