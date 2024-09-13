
~~~
root@kali:~# nmap 192.168.100.0/24 -sN
Starting Nmap 7.92 ( https://nmap.org ) at 2024-09-08 11:41 IST
Nmap scan report for ip-192-168-100-1.ap-south-1.compute.internal (192.168.100.1)
Host is up (0.00087s latency).
All 1000 scanned ports on ip-192-168-100-1.ap-south-1.compute.internal (192.168.100.1) are in ignored states.
Not shown: 1000 open|filtered tcp ports (no-response)
MAC Address: 02:BF:41:74:81:63 (Unknown)

Nmap scan report for ip-192-168-100-50.ap-south-1.compute.internal (192.168.100.50)
Host is up (0.00073s latency).
All 1000 scanned ports on ip-192-168-100-50.ap-south-1.compute.internal (192.168.100.50) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:94:BD:43:9C:C3 (Unknown)

Nmap scan report for ip-192-168-100-51.ap-south-1.compute.internal (192.168.100.51)
Host is up (0.00098s latency).
All 1000 scanned ports on ip-192-168-100-51.ap-south-1.compute.internal (192.168.100.51) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:DB:33:FB:F1:23 (Unknown)

Nmap scan report for ip-192-168-100-52.ap-south-1.compute.internal (192.168.100.52)
Host is up (0.0018s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE         SERVICE
21/tcp   open|filtered ftp
22/tcp   open|filtered ssh
80/tcp   open|filtered http
139/tcp  open|filtered netbios-ssn
445/tcp  open|filtered microsoft-ds
3306/tcp open|filtered mysql
3389/tcp open|filtered ms-wbt-server
MAC Address: 02:DA:C1:15:3D:C5 (Unknown)

Nmap scan report for ip-192-168-100-55.ap-south-1.compute.internal (192.168.100.55)
Host is up (0.00099s latency).
All 1000 scanned ports on ip-192-168-100-55.ap-south-1.compute.internal (192.168.100.55) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:07:65:FB:A9:2D (Unknown)

Nmap scan report for ip-192-168-100-63.ap-south-1.compute.internal (192.168.100.63)
Host is up (0.00082s latency).
All 1000 scanned ports on ip-192-168-100-63.ap-south-1.compute.internal (192.168.100.63) are in ignored states.
Not shown: 1000 open|filtered tcp ports (no-response)
MAC Address: 02:A0:8A:CC:E2:5B (Unknown)

Nmap scan report for ip-192-168-100-67.ap-south-1.compute.internal (192.168.100.67)
Host is up (0.00059s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE         SERVICE
22/tcp open|filtered ssh
MAC Address: 02:99:71:30:AF:C9 (Unknown)

Nmap scan report for ip-192-168-100-5.ap-south-1.compute.internal (192.168.100.5)
Host is up (0.0000030s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE         SERVICE
22/tcp   open|filtered ssh
3389/tcp open|filtered ms-wbt-server
5910/tcp open|filtered cm

Nmap done: 256 IP addresses (8 hosts up) scanned in 40.87 seconds
~~~

samba 

~~~
root@kali:~# nmap --script smb-os-discovery.nse -p  445  192.168.100.52 
Starting Nmap 7.92 ( https://nmap.org ) at 2024-09-08 11:52 IST
Nmap scan report for ip-192-168-100-52.ap-south-1.compute.internal (192.168.100.52)
Host is up (0.00063s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:DA:C1:15:3D:C5 (Unknown)

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.13.17-Ubuntu)
|   Computer name: ip-192-168-100-52
|   NetBIOS computer name: IP-192-168-100-52\x00
|   Domain name: ap-south-1.compute.internal
|   FQDN: ip-192-168-100-52.ap-south-1.compute.internal
|_  System time: 2024-09-08T06:22:07+00:00

Nmap done: 1 IP address (1 host up) scanned in 0.34 seconds
~~~


~~~
nmap --script smb-enum-shares -p  445  192.168.100.52 
Starting Nmap 7.92 ( https://nmap.org ) at 2024-09-08 11:54 IST
Nmap scan report for ip-192-168-100-52.ap-south-1.compute.internal (192.168.100.52)
Host is up (0.0013s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:DA:C1:15:3D:C5 (Unknown)

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\192.168.100.52\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (ip-192-168-100-52 server (Samba, Ubuntu))
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\192.168.100.52\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|     Current user access: <none>
|   \\192.168.100.52\shared: 
|     Type: STYPE_DISKTREE
|     Comment: shared
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\auditor\shared
|     Anonymous access: READ
|_    Current user access: READ

~~~


www-data@ip-192-168-100-52:/home/auditor$ cat fla
cat flag.txt 
5775004cc3ba4330b83bc9088d593784




    'default' => 
    array (
      'database' => 'drupal',
      'username' => 'drupal',
      'password' => 'syntex0421',
      'host' => 'localhost',
      'port' => '3306',
      'driver' => 'mysql',
      'prefix' => '',
    ),
  ),
);





+-----+----------+---------------------------------------------------------+----------------------+-------+-----------+------------------+------------+------------+------------+--------+------------------+----------+---------+----------------------+------+
| uid | name     | pass                                                    | mail                 | theme | signature | signature_format | created    | access     | login      | status | timezone         | language | picture | init                 | data |
+-----+----------+---------------------------------------------------------+----------------------+-------+-----------+------------------+------------+------------+------------+--------+------------------+----------+---------+----------------------+------+
|   0 |          |                                                         |                      |       |           | NULL             |          0 |          0 |          0 |      0 | NULL             |          |       0 |                      | NULL |
|   1 | admin    | $S$D67i0qFmSLMLwZ9PU7VEocSS9fvV1JaSeJxQMgCid80hGbq6wXZH | admin@syntex.com     |       |           | NULL             | 1650232322 | 1650248652 | 1650248498 |      1 | America/New_York |          |       0 | admin@syntex.com     | b:0; |
|   2 | auditor  | $S$DV.wsqkmKY3y5VW.icW/g5NTU3h.UA01nxqL9Cro27GaSBYpH4WC | auditor@syntex.com   |       |           | filtered_html    | 1650234408 |          0 |          0 |      1 | America/New_York |          |       0 | auditor@syntex.com   | b:0; |
|   3 | dbadmin  | $S$DZcGD5qcb6xso1E/Mu6DJP4uPi5DfY28kBEyuIab8Pod1saBaImN | dbadmin@syntex.com   |       |           | filtered_html    | 1650248436 |          0 |          0 |      1 | America/New_York |          |       0 | dbadmin@syntex.com   | b:0; |
|   4 | Vincenzo | $S$DGnS.dK3q2FeWeNbLikdI5Hk/XdBFI2jBFkmPvv/v9Ln8vjIanIu | vincenzo@syntext.com |       |           | filtered_html    | 1650248490 |          0 |          0 |      1 | America/New_York |          |       0 | vincenzo@syntext.com | b:0; |
|   5 | admin1   | $S$DC0eO.Q2eYm8Lpo3zXUsIjFYzOjkuZRQFZkv47p2wGKPo5e56l.1 | admin@syntexd.com    |       |           | filtered_html    | 1725782023 |          0 |          0 |      0 | America/New_York |          |       0 | admin@syntexd.com    | NULL |
+-----+----------+---------------------------------------------------------+----------------------+-------+-----------+------------------+------------+------------+------------+--------+------------------+----------+---------+----------------------+------+





| Host      | User   | Password                                  | Select_priv | Insert_priv | Update_priv | Delete_priv | Create_priv | Drop_priv | Reload_priv | Shutdown_priv | Process_priv | File_priv | Grant_priv | References_priv | Index_priv | Alter_priv | Show_db_priv | Super_priv | Create_tmp_table_priv | Lock_tables_priv | Execute_priv | Repl_slave_priv | Repl_client_priv | Create_view_priv | Show_view_priv | Create_routine_priv | Alter_routine_priv | Create_user_priv | Event_priv | Trigger_priv | Create_tablespace_priv | Delete_history_priv | ssl_type | ssl_cipher | x509_issuer | x509_subject | max_questions | max_updates | max_connections | max_user_connections | plugin      | authentication_string | password_expired | is_role | default_role | max_statement_time |
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
| localhost | root   | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | Y          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 | unix_socket |                       | N                | N       |              |           0.000000 |
| %         | root   | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | N          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 |             |                       | N                | N       |              |           0.000000 |
| localhost | drupal | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | N          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 |             |                       | N                | N       |              |           0.000000 |
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
3 rows in set (0.000 sec)

















# FULL NMAP SCAN

root@kali:~# nmap 192.168.100.50-55 -A
Starting Nmap 7.92 ( https://nmap.org ) at 2024-09-08 17:58 IST
Nmap scan report for wordpress.local (192.168.100.50)
Host is up (0.00090s latency).
Not shown: 990 closed tcp ports (reset)
PORT      STATE SERVICE            VERSION
80/tcp    open  http               Apache httpd 2.4.51 ((Win64) PHP/7.4.26)
|_http-title: Syntex
|_http-server-header: Apache/2.4.51 (Win64) PHP/7.4.26
|_http-generator: WordPress 5.9.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows Server 2012 R2 Standard 9600 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=WINSERVER-01
| Not valid before: 2024-09-07T05:59:10
|_Not valid after:  2025-03-09T05:59:10
|_ssl-date: 2024-09-08T12:29:59+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-01
|   NetBIOS_Domain_Name: WINSERVER-01
|   NetBIOS_Computer_Name: WINSERVER-01
|   DNS_Domain_Name: WINSERVER-01
|   DNS_Computer_Name: WINSERVER-01
|   Product_Version: 6.3.9600
|_  System_Time: 2024-09-08T12:29:52+00:00
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49160/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 02:94:BD:43:9C:C3 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=9/8%OT=80%CT=1%CU=37079%PV=Y%DS=1%DC=D%G=Y%M=0294BD%TM
OS:=66DD98C7%P=x86_64-pc-linux-gnu)SEQ(SP=FD%GCD=1%ISR=109%TI=I%CI=I%II=I%S
OS:S=S%TS=7)OPS(O1=M2301NW8ST11%O2=M2301NW8ST11%O3=M2301NW8NNT11%O4=M2301NW
OS:8ST11%O5=M2301NW8ST11%O6=M2301ST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000%W
OS:5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M2301NW8NNS%CC=Y%Q=)T1(R=Y%DF=
OS:Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q
OS:=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%
OS:A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%
OS:DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%
OS:O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD
OS:=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: WINSERVER-01, NetBIOS user: <unknown>, NetBIOS MAC: 02:94:bd:43:9c:c3 (unknown)
| smb2-time: 
|   date: 2024-09-08T12:29:53
|_  start_date: 2024-09-08T05:59:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 0s, deviation: 1s, median: 0s
| smb-os-discovery: 
|   OS: Windows Server 2012 R2 Standard 9600 (Windows Server 2012 R2 Standard 6.3)
|   OS CPE: cpe:/o:microsoft:windows_server_2012::-
|   Computer name: WINSERVER-01
|   NetBIOS computer name: WINSERVER-01\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-09-08T12:29:54+00:00
| smb2-security-mode: 
|   3.0.2: 
|_    Message signing enabled but not required

TRACEROUTE
HOP RTT     ADDRESS
1   0.90 ms wordpress.local (192.168.100.50)

Nmap scan report for ip-192-168-100-51.ap-south-1.compute.internal (192.168.100.51)
Host is up (0.0010s latency).
Not shown: 989 closed tcp ports (reset)
PORT      STATE SERVICE            VERSION
21/tcp    open  ftp                Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 04-19-22  02:25AM       <DIR>          aspnet_client
| 04-19-22  01:19AM                 1400 cmdasp.aspx
| 04-19-22  12:17AM                99710 iis-85.png
| 04-19-22  12:17AM                  701 iisstart.htm
|_04-19-22  02:13AM                   22 robots.txt.txt
80/tcp    open  http               Microsoft IIS httpd 8.5
|_http-svn-info: ERROR: Script execution failed (use -d to debug)
|_http-server-header: Microsoft-IIS/8.5
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Server Date: Sun, 08 Sep 2024 12:29:52 GMT
|   Server Type: Microsoft-IIS/8.5
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, DELETE, MOVE, PROPPATCH, MKCOL, LOCK, UNLOCK
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   Directory Listing: 
|     http://ip-192-168-100-51.ap-south-1.compute.internal/
|     http://ip-192-168-100-51.ap-south-1.compute.internal/aspnet_client/
|     http://ip-192-168-100-51.ap-south-1.compute.internal/cmdasp.aspx
|     http://ip-192-168-100-51.ap-south-1.compute.internal/iis-85.png
|     http://ip-192-168-100-51.ap-south-1.compute.internal/iisstart.htm
|_    http://ip-192-168-100-51.ap-south-1.compute.internal/robots.txt.txt
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=WINSERVER-02
| Not valid before: 2024-09-07T05:59:09
|_Not valid after:  2025-03-09T05:59:09
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-02
|   NetBIOS_Domain_Name: WINSERVER-02
|   NetBIOS_Computer_Name: WINSERVER-02
|   DNS_Domain_Name: WINSERVER-02
|   DNS_Computer_Name: WINSERVER-02
|   Product_Version: 6.3.9600
|_  System_Time: 2024-09-08T12:29:52+00:00
|_ssl-date: 2024-09-08T12:29:59+00:00; 0s from scanner time.
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49156/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 02:DB:33:FB:F1:23 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=9/8%OT=21%CT=1%CU=34956%PV=Y%DS=1%DC=D%G=Y%M=02DB33%TM
OS:=66DD98C7%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10D%TI=I%CI=I%II=I%
OS:SS=S%TS=7)OPS(O1=M2301NW8ST11%O2=M2301NW8ST11%O3=M2301NW8NNT11%O4=M2301N
OS:W8ST11%O5=M2301NW8ST11%O6=M2301ST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000%
OS:W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M2301NW8NNS%CC=Y%Q=)T1(R=Y%DF
OS:=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%
OS:Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A
OS:%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y
OS:%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR
OS:%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RU
OS:D=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2024-09-08T12:29:52
|_  start_date: 2024-09-08T05:58:58
| smb2-security-mode: 
|   3.0.2: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: WINSERVER-02, NetBIOS user: <unknown>, NetBIOS MAC: 02:db:33:fb:f1:23 (unknown)

TRACEROUTE
HOP RTT     ADDRESS
1   1.04 ms ip-192-168-100-51.ap-south-1.compute.internal (192.168.100.51)

Nmap scan report for ip-192-168-100-52.ap-south-1.compute.internal (192.168.100.52)
Host is up (0.0013s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.100.5
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
|_-rw-r--r--    1 65534    65534         318 Apr 18  2022 updates.txt
22/tcp   open  ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 53:34:cb:d5:1d:83:5d:77:c5:f7:d9:bf:ce:b1:b9:ef (RSA)
|   256 f4:93:8d:37:e4:78:d0:1b:f7:d4:73:aa:69:7c:03:70 (ECDSA)
|_  256 2a:6f:3c:ae:da:05:dc:b0:91:05:ec:7f:8c:00:53:fa (ED25519)
80/tcp   open  http          Apache httpd 2.4.41
|_http-title: Index of /
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2018-02-21 17:28  drupal/
|_
139/tcp  open  netbios-ssn   Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn   Samba smbd 4.13.17-Ubuntu (workgroup: WORKGROUP)
3306/tcp open  mysql         MySQL 5.5.5-10.3.34-MariaDB-0ubuntu0.20.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.34-MariaDB-0ubuntu0.20.04.1
|   Thread ID: 102
|   Capabilities flags: 63486
|   Some Capabilities: Speaks41ProtocolNew, Support41Auth, Speaks41ProtocolOld, InteractiveClient, SupportsLoadDataLocal, IgnoreSigpipes, DontAllowDatabaseTableColumn, ODBCClient, FoundRows, SupportsTransactions, IgnoreSpaceBeforeParenthesis, LongColumnFlag, SupportsCompression, ConnectWithDatabase, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: \^Bl}PU=p;u^VtdwMWOc
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server xrdp
MAC Address: 02:DA:C1:15:3D:C5 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=9/8%OT=21%CT=1%CU=32179%PV=Y%DS=1%DC=D%G=Y%M=02DAC1%TM
OS:=66DD98C7%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=109%TI=Z%CI=Z%II=I%
OS:TS=A)OPS(O1=M2301ST11NW7%O2=M2301ST11NW7%O3=M2301NNT11NW7%O4=M2301ST11NW
OS:7%O5=M2301ST11NW7%O6=M2301ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4
OS:B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M2301NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=
OS:40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%
OS:O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=4
OS:0%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%
OS:Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=
OS:Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: Host: IP-192-168-100-52; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.13.17-Ubuntu)
|   Computer name: ip-192-168-100-52
|   NetBIOS computer name: IP-192-168-100-52\x00
|   Domain name: ap-south-1.compute.internal
|   FQDN: ip-192-168-100-52.ap-south-1.compute.internal
|_  System time: 2024-09-08T12:29:53+00:00
|_nbstat: NetBIOS name: IP-192-168-100-, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 0s, deviation: 1s, median: 0s
| smb2-time: 
|   date: 2024-09-08T12:29:53
|_  start_date: N/A

TRACEROUTE
HOP RTT     ADDRESS
1   1.34 ms ip-192-168-100-52.ap-south-1.compute.internal (192.168.100.52)

Nmap scan report for ip-192-168-100-55.ap-south-1.compute.internal (192.168.100.55)
Host is up (0.0013s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server 2019 Datacenter 17763 microsoft-ds
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-03
|   NetBIOS_Domain_Name: WINSERVER-03
|   NetBIOS_Computer_Name: WINSERVER-03
|   DNS_Domain_Name: WINSERVER-03
|   DNS_Computer_Name: WINSERVER-03
|   Product_Version: 10.0.17763
|_  System_Time: 2024-09-08T12:29:51+00:00
| ssl-cert: Subject: commonName=WINSERVER-03
| Not valid before: 2024-09-07T05:58:27
|_Not valid after:  2025-03-09T05:58:27
|_ssl-date: 2024-09-08T12:29:57+00:00; -2s from scanner time.
MAC Address: 02:07:65:FB:A9:2D (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=9/8%OT=80%CT=1%CU=35876%PV=Y%DS=1%DC=D%G=Y%M=020765%TM
OS:=66DD98C7%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=110%TI=I%CI=I%II=I%
OS:SS=S%TS=U)OPS(O1=M2301NW8NNS%O2=M2301NW8NNS%O3=M2301NW8%O4=M2301NW8NNS%O
OS:5=M2301NW8NNS%O6=M2301NNS)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6
OS:=FF70)ECN(R=Y%DF=Y%T=80%W=FFFF%O=M2301NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%
OS:DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O
OS:=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80
OS:%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q
OS:=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y
OS:%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2019 Datacenter 17763 (Windows Server 2019 Datacenter 6.3)
|   Computer name: WINSERVER-03
|   NetBIOS computer name: WINSERVER-03\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-09-08T12:29:51+00:00
| smb2-time: 
|   date: 2024-09-08T12:29:51
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_clock-skew: mean: -1s, deviation: 0s, median: -2s
|_nbstat: NetBIOS name: WINSERVER-03, NetBIOS user: <unknown>, NetBIOS MAC: 02:07:65:fb:a9:2d (unknown)

TRACEROUTE
HOP RTT     ADDRESS
1   1.30 ms ip-192-168-100-55.ap-south-1.compute.internal (192.168.100.55)

Post-scan script results:
| clock-skew: 
|   0s: 
|     192.168.100.51 (ip-192-168-100-51.ap-south-1.compute.internal)
|     192.168.100.50 (wordpress.local)
|_    192.168.100.52 (ip-192-168-100-52.ap-south-1.compute.internal)
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 6 IP addresses (4 hosts up) scanned in 87.67 seconds
