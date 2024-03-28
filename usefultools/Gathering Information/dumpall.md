# Dump all: 多种泄漏形式，一种利用方式



## 💫 Usage

```bash
# 下载文件（源代码）
dumpall -u <url> [-o <outdir>]

# 示例
dumpall -u http://example.com/.git/
dumpall -u http://example.com/.svn/
dumpall -u http://example.com/.DS_Store
dumpall -u http://example.com/
```

```bash
$ dumpall --help
Usage: dumpall.py [OPTIONS]

  信息泄漏利用工具，适用于.git/.svn/.DS_Store，以及目录列出下载

  Example: dumpall -u http://example.com/.git

Options:
  --version          Show the version and exit.
  -u, --url TEXT     指定目标URL，支持.git/.svn/.DS_Store，以及类index页面
  -o, --outdir TEXT  指定下载目录，默认目录名为主机名
  -p, --proxy TEXT   指定代理 scheme://[user:pass@]hostname:port
  -f, --force        强制下载（可能会有蜜罐风险）
  -d, --debug        调试模式
  --help             Show this message and exit.
```
