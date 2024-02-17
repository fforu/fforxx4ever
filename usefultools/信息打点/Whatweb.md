## whatweb

### 常规扫描

```shell
whatweb 域名
```



### 批量扫描

```shell
whatweb -i /kali/home/workspace/target.txt
```

将要扫描的网址放在target.txt下

### 参数说明：

```shell
-i : 指定要扫描的文件

-v : 详细显示扫描的结果

-a : 指定运行级别（1-4）

--log-brief=FILE 简要的记录，每个网站只记录一条返回信息

--log-verbose=FILE 详细输出

--log-xml=FILE 返回XML格式的日志

--log-json=FILE 以 json 格式记录日志

--log-json-verbose=FILE 记录详细的json日志

--log-magictree=FILE XML的树形结构
```



## 高级用法

```shell
whatweb -v 192.168.194.0/24 -url-suffix=":8080"
```

- -url-prefix : 添加前缀
- -url-suffix : 添加后缀
- -url-pattern : 在中间加入参数

```shell
whatwen -v -proxy-user admin:password url
```

- 以指定用户名和密码进行探测

```shell
whatweb -v -c=cookie url
```

- -c : 指定 cookie

```shell
whatweb -l
```

- -l : 列出所有的插件列表

```shell
whatweb -info-plugins="plugigns"
```

- -info-plugins : 查看插件的具体信息

