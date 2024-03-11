ffuf

## 模糊测试目录爆破

ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt:FUZZ -u http://83.136.250.12:56127/FUZZ

## 页面模糊测试

测试网站文件后缀名
ffuf -w web-extensions.txt:FUZZ -u http://83.136.250.12:56127/blogFUZZ

测试指定后缀名的所有文件
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt:FUZZ -u http://83.136.250.12:56127/blog/FUZZ.php

## 递归模糊测试

ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt:FUZZ -u http://83.136.250.12:56127/FUZZ  -recursion -recursion-depth 1 -e .php -v 

-recursion -recursion-depth 1 指定测试方式为递归，递归深度1，即只扫描这一层即下一层
-e 指定后缀名

## 虚拟主机模糊测试

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'

-H 指定标头

## GET 请求模糊测试

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin .php?FUZZ=key -fs xxx

-fs 过滤指定字符

## POST 请求模糊测试

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx