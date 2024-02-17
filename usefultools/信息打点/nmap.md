 ### Nmap 扫描

扫网段中所有存活的主机和开放端口

> sudo nmap -sT 192.168.1.1

扫网段中存活的主机

> sudo nmap -sn ip

扫开放端口，10000是最小速率，比较合适，最好扫两遍

> Sudo nmap -sT --min-rate 10000 -p- 192.168.99.11 -oA /home/kali/workspace/nmap/ports

```shell
nmap扫描默认使用-sS进行扫描
-sS 使用的是syn探测，只发送一个syn请求包，容易被发现容易被墙
-sT 使用tcp扫描，可以更加精准并且隐蔽地探测目标主机
-oA 将nmap扫描的结果全部保存至指定目录
```

扫udp开放端口

> Sudo nmap -sU --min-rate 10000 -p- 192.168.194.141 /home/kali/workspace/nmap/udp

> Sudo nmap -sU --top-ports 20 192.168.99.29 /home/kali/workspace/nmap/udp

提取扫描到的端口

> Grep open /home/kali/workspace/nmap/ports.Nmap | awk -F'/'  '{print $1}' |paste -sd ‘,’

```
使用grep过滤端口号

awk -F'/' '{print $1}'
-F 的作用是指定分隔符为/
分割完毕后执行后一行命令，即打印第一个参数

paste -sd 
-s 的作用是将参数以一行的形式打印 d为指定分割符

```

将提取到的端口赋值给 ports 变量
> Ports=$(Grep open /home/kali/workspace/nmap/ports. Nmap | awk -F'/'  '{print $1}' |paste -sd ‘,’ )

echo $ports 即为探测到的端口


探测服务

> Sudo nmap -sT -sV -sC -O -p 80,111,777 192.168.99.19 -oA 
>
> sudo nmap 192.168.194.146 -p 22,80,111,139,443,1024 -sC -sV -O --version-all -oA server_info 

T：指定为tcp

V：返回version

O：返回操作系统

C：默认脚本

--version-all：尽可能多的展示版本信息

利用脚本直接扫漏洞

> sudo nmap  --script=vuln -p80,111,777 ip /home/kali/workspace/nmap/vuln

### 非nmap扫描

> for i in {140..150};do ping -c 1 -W 1 192.168.194.$i;done     
>
> for i in {140..150};do ping -c 1 -W 1 192.168.194.$i | grep from;done  


-c 在多少次应答后停止

-W 超时时间