## Google

### intitle:

```
表示搜索在网页标题中出现第一个关键词的网页。
```

　　例如”intitle:渗透测试 “将返回在标题中出现”渗透测试 “的所有链接。

　　用”allintitle: 黑客技术 Google”则会返回网页标题中同时含有 “渗透测试” 和 “Google” 的链接。
　　intext:返回网页的文本中出现关键词的网页。用allintext:搜索多个关键字。

### inurl:

```
返回的网页链接中包含第一个关键字的网页。
```

### site:

```
在某个限定的网站中搜索。
```

### intext:

```
搜索内容中包含有指定字符的网址
```

### filetype:

```
搜索特定扩展名的文件（如.doc .pdf .ppt）。
```

黑客们往往会关注特定的文件，例如：.pwl口令文件、.tmp临时文件、.cfg配置文件、.ini系统文件、.hlp帮助文件、.dat数据文件、.log日志文件、.par交换文件等等。

### link:

```
表示返回所有链接到某个地址的网页。
```

### related：

```
返回连接到类似于指定网站的网页。
```

### cache:

```
搜索Google缓存中的网页。
```

### info:

```
表示搜索网站的摘要。例如”info:whu.edu.cn”仅得到一个结果：
```

### phonebook: 

```
搜索电话号码簿，将会返回美国街道地址和电话号码列表,这无疑给挖掘个人信息的黑客带来极大的便利。
同时还可以得到住宅的全面信息，
```

结合Google earth将会得到更详细的信息。
相应的还有更小的分类搜索：

rphonebook:仅搜索住宅用户电话号码簿；

bphonebook:仅搜索商业的电话号码簿。

另外，还有一些不常用的搜索指令。
例如列表如下：

```
　　　　author:搜索新闻组帖子的作者。

　　　　group:搜索Google组搜索词汇帖子的题目。

　　　　msgid:搜索识别新闻组帖子的Google组信息标识符和字符串。

　　　　insubject:搜索Google组的标题行。

　　　　stocks:搜索有关一家公司的股票市场信息。

　　　　define:返回一个搜索词汇的定义。

　　　　inanchor:搜索一个HTML标记中的一个链接的文本表现形式。

　　　　daterange:搜索某个日期范围内Google做索引的网页。**
```

## Google hacking

　　Google hacking主要是发现那些 公告文件，安全漏洞，错误信息， 口令文件， 用户文件， 演示页面，登录页面， 安全文件， 敏感目录，商业信息，漏洞主机， 网站服务器检测等信息。攻击规律有：

### **利用”Index of”语法检索出站点的活动索引目录**

Index 就是主页服务器所进行操作的一个索引目录。黑客们常利用目录获取密码文件和其他安全文件。常用的攻击语法如下：
Index of /admin 可以挖掘到安全意识不强的管理员的机密文件：
黑客往往可以快速地提取他所要的信息其他Index of 语法列表如下：



```
Index of /passwd
Index of /password
Index of /mail
“Index of /” +passwd
“Index of /” +password.txt
“Index of /” +.htaccess
“Index of /secret”
“Index of /confidential”
“Index of /root”
“Index of /cgi-bin”
“Index of /credit-card”
“Index of /logs”
“Index of /config”
```

### **利用”inurl:”寻找易攻击的站点和服务器**

(1)利用”allinurl:winnt/system32/”寻找受限目录”system32″，一旦具备 cmd.exe 执行权限，就可以控制远程的服务器。

(2)利用”allinurl:wwwboard/passwd.txt”搜寻易受攻击的服务器。

(3)利用”inurl:.bash_history”搜寻服务器的”.bash_history”文件。这个文件包括超级管理员的执行命令，甚至一些敏感信息，如管理员口令序列等。例如：

(4)利用”inurl:config.txt”搜寻服务器的”config.txt”文件，这个文件包括管理员密码和数据认证签名的hash值。


(5)其他语法的搜索。



```
inurl:admin filetype:txt
inurl:admin filetype:db
inurl:admin filetype:cfg
inurl:mysql filetype:cfg
inurl:passwd filetype:txt
inurl:iisadmin
allinurl:/scripts/cart32.exe
allinurl:/CuteNews/show_archives.php
allinurl:/phpinfo.php
allinurl:/privmsg.php
allinurl:/privmsg.php
inurl:auth_user_file.txt
inurl:orders.txt
inurl:”wwwroot/*.”
inurl:adpassword.txt
inurl:webeditor.php
inurl:file_upload.php
inurl:gov filetype:xls “restricted”
index of ftp +.mdb allinurl:/cgi-bin/ +mailto
```

**利用”intitle:”寻找易攻击的站点或服务器**
（1）利用 intitle:”php shell*” “Enable stderr” filetype:php查找安装了php webshell后门的主机，并测试是否有能够直接在机器上执行命令的web shell。
（2）利用allintitle:”index of /admin”搜寻服务器的受限目录入口”admin”。
上面是一些简单容易了解记忆的搜索技巧，关于谷歌的搜索技巧还有很多，有兴趣的可以网上找找这类语法记住，这些技巧对你以后的黑客学习过程中有很大的作用。



```
site:example.com inurl:login inurl:example.com/admin
inurl:gitlab 公司 filetype:txt
inurl:gitlab 公司 intext:账号
site:*. gitee.com intext:账号 （ ftp://*:* 密码 地址）
site:*. gitee.com filetype:txt 账号 （ ftp://*:* 密码 地址）
site:gitlab.*.com intext:密码
site: code.aliyun.com 2022 服务器地址filetype:xls site: 名单 //信息泄露
inurl:备份 filetype:txt 密码
inurl:config.php filetype:bak
inurl:data
inurl:phpMyAdmin
inurl:ewebeditor
intitle:后台管理
intitle:后台管理 inurl:admin
inurl:baidu.com
inurl:php?id=1
inurl:login
site: "身份证" "学生证" "1992" "1993" "1994" "1995" "1996" "1997" "1998" "1999"
"2000"
site:.cn filetype:xls "服务器" "地址" "账
site:file.*.net（.com/.cn）
inurl:ali "管理平台"
cache：类似于百度快照功能，通过cache或许可以查看到目标站点删除的敏感文件
也可以用来解决找目标站点的物理路径不报错，而无法找到物理路径。
cache:xxx.com
inurl:gitlab 公司 filetype:txt
inurl:gitlab 公司 intext:账号
site:*.gitee.com intext:账号 （ftp://*:* 密码 地址）
site:*.gitee.com filetype:txt 账号 （ftp://*:* 密码 地址）
site:gitlab.*.com intext:密码
site:code.aliyun.com 2022 服务器地址
site:xxxx.com
site:xxxx.com filetype: txt (doc docx xls xlsx txt pdf等)
site:xxxx.com intext:管理
site:xxxx.com inurl:login
site:xxxx.com intitle:管理
site:a2.xxxx.com filetype:asp(jsp php aspx等)
site:a2.xxxx.com intext:ftp://*:*(地址 服务器 虚拟机 password等)
site:a2.xxxx.com inurl:file(load)
site:xxxx.com intext:*@xxxx.com //得到N个邮件地址，还有邮箱的主人的名字什么的
site:xxxx.com intext:电话 //N个电话
Version:0.9 StartHTML:0000000105 EndHTML:0000001624 StartFragment:0000000141
EndFragment:0000001584
```

搜学号：site:pku.edu.cn "xh" && site:pku.edu.cn "学号"

搜身份证，搜地级市身份证开头，直接搜site:pku.edu.cn "110000"