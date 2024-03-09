
htb namp 备忘录

10.10.10.0/24|目标网络范围|
|:----- | :----: |
-sn|	                禁用端口扫描。
-Pn|                    禁用 ICMP 回显请求
-n|	                    禁用 DNS 解析。
-PE|	                使用 ICMP 回显请求对目标执行 ping 扫描。
--packet-trace|	        显示所有发送和接收的数据包。
--reason|	            显示特定结果的原因。
--disable-arp-ping|	    禁用 ARP Ping 请求。
--top-ports=<num>|	    扫描已定义为最频繁的指定顶级端口。
-p-|	                扫描所有端口。
-p22-110|	            扫描 22 到 110 之间的所有端口。
-p22,25|	            仅扫描指定端口 22 和 25。
-F|	                    扫描前 100 个端口。
-sS|	                执行 TCP SYN 扫描。
-sA|	                执行 TCP ACK 扫描。
-sU|	                执行 UDP 扫描。
-sV|	                扫描发现的服务的版本。
-sC|	                使用归类为“默认”的脚本执行脚本扫描。
-O|	                    执行操作系统检测扫描以确定目标的操作系统。
-A|	                    执行操作系统检测、服务检测和跟踪路由扫描。
-D RND:5|           	设置将用于扫描目标的随机诱饵的数量。
-e|	                    指定用于扫描的网络接口。
-S 10.10.10.200|    	指定扫描的源 IP 地址。
-g|	                    指定扫描的源端口。
--dns-server <ns>|	    DNS 解析是使用指定的名称服务器进行的。

输出选项
|Nmap选项|	描述
|:----- | :----: |
-oA filename|	以“文件名”开头的所有可用格式存储结果。
-oN filename|	以正常格式存储结果，名称为“文件名”。
-oG filename|	以“grepable”格式存储结果，名称为“filename”。
-oX filename|	以 XML 格式存储结果，名称为“filename”。

性能选项

|Nmap选项|	描述
|:----- | :----: |
--max-retries <num>|	    设置扫描特定端口的重试次数。
--stats-every=5s|	        每 5 秒显示一次扫描状态。
-v/-vv|	                    在扫描期间显示详细输出。
--initial-rtt-timeout 50ms|	将指定的时间值设置为初始 RTT 超时。
--max-rtt-timeout 100ms|	将指定的时间值设置为最大 RTT 超时。
--min-rate 300|	            设置同时发送的数据包数量。
-T <0-5>|	                指定具体的计时模板。



## 防火墙和 IDS/IPS 规避

### 入侵检测系统/入侵防御系统

与防火墙一样，入侵检测系统（IDS）和入侵防御系统（IPS）也是基于软件的组件。
IDS扫描网络中是否存在潜在的攻击，对其进行分析并报告任何检测到的攻击。如果检测到潜在攻击，则采取特定的防御措施进行IPS补充。
IDS此类攻击的分析基于模式匹配和签名。如果检测到特定模式（例如服务检测扫描），IPS可能会阻止挂起的连接尝试。

#### 确定防火墙及其规则

-sA 可以确定目标靶机的过滤规则

当端口显示为已过滤时，可能有多种原因。在大多数情况下，防火墙设置了某些规则来处理特定的连接。数据包可以是dropped、 或rejected。数据dropped包将被忽略，并且主机不会返回任何响应

当使用SYN或者TCP扫描时，发送的通常是一个带有标志的数据包，通常带有标志的数据包是允许入站出站的，而使用ACK扫描时，ACK数据包不带有标志，如果此时防火墙对这个端口做了限制，那么这个ack包就被过滤了，因此确定防火墙及其规则的方式即是使用-sT和-sA各扫一次

```bash
fforu@htb[/htb]$ sudo nmap 10.129.2.28 -p 21,22,25 -sS -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 14:56 CEST
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:22 S ttl=53 id=22412 iplen=44  seq=4092255222 win=1024 <mss 1460>
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:25 S ttl=50 id=62291 iplen=44  seq=4092255222 win=1024 <mss 1460>
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:21 S ttl=58 id=38696 iplen=44  seq=4092255222 win=1024 <mss 1460>
RCVD (0.0329s) ICMP [10.129.2.28 > 10.10.14.2 Port 21 unreachable (type=3/code=3) ] IP [ttl=64 id=40884 iplen=72 ]
RCVD (0.0341s) TCP 10.129.2.28:22 > 10.10.14.2:57347 SA ttl=64 id=0 iplen=44  seq=1153454414 win=64240 <mss 1460>
RCVD (1.0386s) TCP 10.129.2.28:22 > 10.10.14.2:57347 SA ttl=64 id=0 iplen=44  seq=1153454414 win=64240 <mss 1460>
SENT (1.1366s) TCP 10.10.14.2:57348 > 10.129.2.28:25 S ttl=44 id=6796 iplen=44  seq=4092320759 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.0053s latency).

PORT   STATE    SERVICE
21/tcp filtered ftp
22/tcp open     ssh
25/tcp filtered smtp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
```

```bash
fforu@htb[/htb]$ sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 14:57 CEST
SENT (0.0422s) TCP 10.10.14.2:49343 > 10.129.2.28:21 A ttl=49 id=12381 iplen=40  seq=0 win=1024
SENT (0.0423s) TCP 10.10.14.2:49343 > 10.129.2.28:22 A ttl=41 id=5146 iplen=40  seq=0 win=1024
SENT (0.0423s) TCP 10.10.14.2:49343 > 10.129.2.28:25 A ttl=49 id=5800 iplen=40  seq=0 win=1024
RCVD (0.1252s) ICMP [10.129.2.28 > 10.10.14.2 Port 21 unreachable (type=3/code=3) ] IP [ttl=64 id=55628 iplen=68 ]
RCVD (0.1268s) TCP 10.129.2.28:22 > 10.10.14.2:49343 R ttl=64 id=0 iplen=40  seq=1660784500 win=0
SENT (1.3837s) TCP 10.10.14.2:49344 > 10.129.2.28:25 A ttl=59 id=21915 iplen=40  seq=0 win=1024
Nmap scan report for 10.129.2.28
Host is up (0.083s latency).

PORT   STATE      SERVICE
21/tcp filtered   ftp
22/tcp unfiltered ssh
25/tcp filtered   smtp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

ack扫描结果显示，22端口未过滤，而其他两个端口过滤了

#### 检测IDS/IPS

##### 诱饵扫描
诱饵扫描方法（-D）
通过这种方法，Nmap 生成各种随机 IP 地址，插入到 IP 标头中以伪装发送数据包的来源。通过这种方法，我们可以生成随机 ( RND) 特定数量（例如：5）的 IP 地址，并用冒号 ( :) 分隔。然后我们的真实 IP 地址被随机放置在生成的 IP 地址之间。

示例：
```bash
fforu@htb[/htb]$ sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 16:14 CEST
SENT (0.0378s) TCP 102.52.161.59:59289 > 10.129.2.28:80 S ttl=42 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0378s) TCP 10.10.14.2:59289 > 10.129.2.28:80 S ttl=59 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 210.120.38.29:59289 > 10.129.2.28:80 S ttl=37 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 191.6.64.171:59289 > 10.129.2.28:80 S ttl=38 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 184.178.194.209:59289 > 10.129.2.28:80 S ttl=39 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 43.21.121.33:59289 > 10.129.2.28:80 S ttl=55 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
RCVD (0.1370s) TCP 10.129.2.28:80 > 10.10.14.2:59289 SA ttl=64 id=0 iplen=44  seq=4056111701 win=64240 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.099s latency).

PORT   STATE SERVICE
80/tcp open  http
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

|扫描选项        |    	描述    |
|:-----         |     :----:  |
10.129.2.28|	    扫描指定目标。
-p 80|      	    仅扫描指定端口。
-sS|	            在指定端口上执行 SYN 扫描。
-Pn|	            禁用 ICMP Echo 请求。
-n|	                禁用 DNS 解析。
--disable-arp-ping|	禁用 ARP Ping。
--packet-trace|     显示所有发送和接收的数据包。
-D RND:5|	        生成五个随机 IP 地址，指示连接来自的源 IP。

另一种情况是只有个别子网无法访​​问服务器的特定服务。所以我们也可以手动指定源IP地址( -S)来测试是否能得到更好的结果。诱饵可用于 SYN、ACK、ICMP 扫描和操作系统检测扫描。因此，让我们看一下这样的示例，并确定它最有可能是哪种操作系统。

可以通过构造伪ip的方式去扫描目标机
##### 测试防火墙规则
```bash
fforu@htb[/htb]$ sudo nmap 10.129.2.28 -n -Pn -p445 -O

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-22 01:23 CEST
Nmap scan report for 10.129.2.28
Host is up (0.032s latency).

PORT    STATE    SERVICE
445/tcp filtered microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 3.14 seconds
```

##### 构造伪源ip
```bash
fforu@htb[/htb]$ sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-22 01:16 CEST
Nmap scan report for 10.129.2.28
Host is up (0.010s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 3.4 - 3.10 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Synology DiskStation Manager 5.2-5644 (94%), Linux 2.6.32 - 2.6.35 (94%), Linux 2.6.32 - 3.5 (94%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.11 seconds
```
扫描选项|	描述
|:-----         |     :----:  |
10.129.2.28| 	扫描指定目标。
-n|	            禁用 DNS 解析。
-Pn|	        禁用 ICMP Echo 请求。
-p 445|	        仅扫描指定端口。
-O|	            执行操作系统检测扫描。
-S|	            使用不同的源IP地址扫描目标。
10.129.2.200|	指定源IP地址。
-e tun0|	    通过指定接口发送所有请求。

##### 从 DNS 端口进行 SYN 扫描
指定端口去扫描
--source-port 53  从指定源端口执行扫描。



