## ftp
1、登入 ftp 后，使用二进制模式
输入 `binary` 即可，可以保证下载文件正确

2、键入? 展示所有可用命令

3、输入 prompt，关闭 ftp 的交互模式
可以保证在传输文件中不需要频繁确认

4、`mget *. txt` 可以下载多个文件

## nfs

1.使用命令，`showmount -e IP`，检索是否存在共享文件夹
还有其它参数，例如：
showmount IP // 连接的主机
showmount -d IP // 目录
showmount -a IP // 挂载点

2.将目标机的共享文件夹挂载到本机的新建的临时文件夹
mkdir /temp/
mount -t nfs 10.10.10.5:/ /temp -o nolock