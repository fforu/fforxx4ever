

## 识别hash值

有时，哈希值以某些格式存储。例如，hash:salt或$id$salt$hash。

该哈希值2fc5a684737ce1bf7b3b239df432416e0dd07357:2014是 SHA1 哈希值，其盐为2014.

该散列$6$vb1tLY1qiY$M.1ZCqKtJBxBtZm1gRi8Bbkn39KU0YJW1cuMFzTRANcNKFKR4RmAQVk4rqQQCkaJT6wXqjUkFcA/qNxLyqW.U/包含由 分隔的三个字段$，其中第一个字段是id，即6。这用于识别用于散列的算法类型。下面的列表包含一些 id 及其相应的算法。
```
$1$  : MD5
$2a$ : Blowfish
$2y$ : Blowfish, with correct handling of 8 bit characters
$5$  : SHA256
$6$  : SHA512
```
下一个字段vb1tLY1qiY是散列过程中使用的盐，最后一个字段是实际的散列。
开源和闭源软件使用许多不同类型的哈希格式。例如，ApacheWeb 服务器以 格式存储其哈希值$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.，而WordPress以 形式存储哈希值$P$984478476IagS59wHZvyQMArzfx58u.。

使用工具`hashid`或者`hash-identifier`
可以识别hash

```bash
┌──(kali㉿kali)-[~/workspace]
└─$ hashid '$S$D34783772bRXEx1aCsvY.bqgaaSu75XmVlKrW9Du8IQlvxHlmzLc' 
Analyzing '$S$D34783772bRXEx1aCsvY.bqgaaSu75XmVlKrW9Du8IQlvxHlmzLc'
[+] Drupal > v7.x 
```

hashcat 各个解密模式的编号
https://hashcat.net/wiki/doku.php?id=example_hashes

## 攻击模式

### 字典攻击

hashcat -a 0 -m <hash type> <hash file> <wordlist>

### 组合攻击

```bash
┌──(fforu㉿fforu)-[~/workspace]
└─$ echo 'sunshine
happy
frozen
golden' > wordlist1

┌──(fforu㉿fforu)-[~/workspace]
└─$ echo 'hello   
joy  
secret
apple' > wordlist12

┌──(fforu㉿fforu)-[~/workspace]
└─$ echo '19672a3f042ae1b592289f8333bf76c5' > hash.txt

┌──(fforu㉿fforu)-[~/workspace]
└─$ hashcat -a 1 -m 0 hash.txt wordlist1 wordlist12
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 5.0+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 16.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: cpu-haswell-AMD Ryzen 5 5600H with Radeon Graphics, 6615/13295 MB (2048 MB allocatable), 12MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Dictionary cache built:
* Filename..: wordlist1
* Passwords.: 4
* Bytes.....: 56
* Keyspace..: 4
* Runtime...: 0 secs

Dictionary cache built:
* Filename..: wordlist12
* Passwords.: 4
* Bytes.....: 28
* Keyspace..: 4
* Runtime...: 0 secs

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 3 MB

Dictionary cache hit:
* Filename..: wordlist1
* Passwords.: 4
* Bytes.....: 56
* Keyspace..: 16

The wordlist or mask that you are using is too small.
This means that hashcat cannot use the full parallel power of your device(s).
Unless you supply more work, your cracking speed will drop.
For tips on supplying more work, see: https://hashcat.net/faq/morework

Approaching final keyspace - workload adjusted.

19672a3f042ae1b592289f8333bf76c5:frozenapple

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 19672a3f042ae1b592289f8333bf76c5
Time.Started.....: Thu Mar 14 12:58:55 2024 (0 secs)
Time.Estimated...: Thu Mar 14 12:58:55 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (wordlist1), Left Side
Guess.Mod........: File (wordlist12), Right Side
Speed.#1.........:      325 H/s (0.06ms) @ Accel:1024 Loops:4 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 16/16 (100.00%)
Rejected.........: 0/16 (0.00%)
Restore.Point....: 0/4 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-4 Iteration:0-4
Candidate.Engine.: Device Generator
Candidates.#1....: sunshine                           hello    -> goldenapple

Started: Thu Mar 14 12:58:40 2024
Stopped: Thu Mar 14 12:58:57 2024
```

### 