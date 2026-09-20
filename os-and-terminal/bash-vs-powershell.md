# Bash (Linux/macOS) vs PowerShell 命令对照表

## 一、文件基础操作

| Bash命令 | PowerShell简写 | PowerShell全写 | 功能介绍 |
|---|---|---|---|
| `cat test.txt` | `gc test.txt` | `Get-Content test.txt` | 读取文件全部内容 |
| `cat -n test.txt` | `gc test.txt \| nl` | `Get-Content test.txt \| Number-Object` | 读取并显示行号 |
| `cp -r dirA dirB` | `cp dirA dirB -r` | `Copy-Item dirA dirB -Recurse` | 递归复制目录 |
| `cp -rv dirA dirB` | `cp dirA dirB -r -v` | `Copy-Item dirA dirB -Recurse -Verbose` | 递归复制并输出详细日志 |
| `cp -v a.txt b.txt` | `cp a.txt b.txt -v` | `Copy-Item a.txt b.txt -Verbose` | 复制并输出详细日志 |
| `cp a.txt b.txt` | `cp a.txt b.txt` | `Copy-Item a.txt b.txt` | 单文件复制 |
| `head -10 test.txt` | `gc test.txt -f 10` | `Get-Content test.txt -First 10` | 查看头部 N 行 |
| `mv a.txt b.txt` | `mv a.txt b.txt` | `Move-Item a.txt b.txt` | 移动 / 重命名 |
| `rm -rf testdir` | `ri testdir -r -f` | `Remove-Item testdir -Recurse -Force` | 强制递归删除 |
| `rm -rfv testdir` | `ri testdir -r -f -v` | `Remove-Item testdir -Recurse -Force -Verbose` | 递归删除并打印每一步 |
| `rm test.txt` | `ri test.txt` | `Remove-Item test.txt` | 删除单个文件（带交互提示） |
| `stat test.txt` | `gi test.txt` | `Get-Item test.txt` | 查看文件元信息 |
| `tail -10 test.txt` | `gc test.txt -l 10` | `Get-Content test.txt -Last 10` | 查看尾部 N 行 |
| `tail -f test.txt` | `gc test.txt -w` | `Get-Content test.txt -Wait` | 实时监控日志输出 |
| `touch test.txt` | `ni test.txt` | `New-Item test.txt -ItemType File` | 创建空白文件 |
| `which python` | `gcm python` | `Get-Command python` | 查询命令安装路径 |

## 二、文件夹操作 & Find 查找

| Bash命令 | PowerShell简写 | PowerShell全写 | 功能介绍 |
|---|---|---|---|
| `du -sh *` | `gci \| measure -s Length` | `Get-ChildItem \| Measure-Object -Sum Length` | 统计目录大小 |
| `find . -name "*.log"` | `gci . -fi *.log -r` | `Get-ChildItem . -Filter *.log -Recurse` | 递归查找指定后缀文件 |
| `find ./data -name "*.txt" -exec rm {} \;` | `gci ./data -fi *.txt -r \| ri` | `Get-ChildItem ./data -Filter *.txt -Recurse \| Remove-Item` | 查找并批量删除 |
| `ls` | `ls` | `Get-ChildItem` | 列出当前目录文件 |
| `ls -a` | `ls -f` | `Get-ChildItem -Force` | 显示隐藏文件 |
| `ls -l` | `ls -l` | `Get-ChildItem \| Format-List` | 详细信息模式 |
| `mkdir -p a/b/c` | `md a/b/c -f` | `New-Item a/b/c -ItemType Directory -Force` | 递归创建多级目录 |
| `mkdir testdir` | `md testdir` | `New-Item testdir -ItemType Directory` | 新建文件夹 |
| `pwd` | `pwd` | `Get-Location` | 查看当前工作目录 |

## 三、压缩解压（tar / zip）

| Bash命令 | PowerShell简写 | PowerShell全写 | 功能介绍 |
|---|---|---|---|
| `tar cvfz out.tar.gz ./dir` | `Compress-Archive ./dir out.tar.gz` | `Compress-Archive -Path ./dir -DestinationPath out.tar.gz` | 打包压缩为 tar.gz |
| `tar xvfz out.tar.gz` | `Expand-Archive out.tar.gz -d ./out` | `Expand-Archive out.tar.gz -DestinationPath ./out` | 解压 tar.gz |
| `unzip test.zip` | `Expand-Archive test.zip -d ./out` | `Expand-Archive test.zip -DestinationPath ./out` | 解压 zip |
| `zip -r out.zip ./dir` | `Compress-Archive ./dir out.zip` | `Compress-Archive -Path ./dir -DestinationPath out.zip` | 打包目录为 zip |

> 注意：PowerShell 的 `Compress-Archive`/`Expand-Archive` 只支持 zip 格式，处理 tar.gz 建议装 `tar`（Windows 10+ 自带 bsdtar）。

## 四、文本处理、管道过滤、统计

| Bash命令 | PowerShell简写 | PowerShell全写 | 功能介绍 |
|---|---|---|---|
| `echo "hello" > a.txt` | `"hello" > a.txt` | `Write-Output "hello" > a.txt` | 覆盖写入文件 |
| `echo "world" >> a.txt` | `"world" >> a.txt` | `Write-Output "world" >> a.txt` | 追加写入文件 |
| `grep "key" test.txt` | `gc test.txt \| sls key` | `Get-Content test.txt \| Select-String key` | 关键词过滤 |
| `grep -i "key" test.txt` | `gc test.txt \| sls key -nocase` | `Get-Content test.txt \| Select-String key -CaseSensitive:$false` | 忽略大小写检索 |
| `ifconfig \| grep 192` | `ipconfig \| sls 192` | `ipconfig \| Select-String 192` | 筛选内网 IP |
| `sed 's/a/b/g' test.txt` | `gc test.txt \| %{$_ -replace "a","b"}` | `Get-Content test.txt \| ForEach-Object {$_ -replace "a","b"}` | 全局文本替换 |
| `sort test.txt` | `gc test.txt \| sort` | `Get-Content test.txt \| Sort-Object` | 行排序 |
| `uniq test.txt` | `gc test.txt \| sort \| gu` | `Get-Content test.txt \| Sort-Object \| Get-Unique` | 去重（需配合排序） |
| `wc -l test.txt` | `gc test.txt \| measure -l` | `Get-Content test.txt \| Measure-Object -Line` | 统计行数 |
| `wc -w test.txt` | `gc test.txt \| measure -w` | `Get-Content test.txt \| Measure-Object -Word` | 统计单词数 |

## 五、变量、环境变量、循环语法

| Bash | PowerShell | 功能介绍 |
|---|---|---|
| `echo $HOME` | `echo $env:HOME` | 查看家目录 |
| `echo $name` | `echo $name` | 读取变量 |
| `for file in *.txt; do cat $file; done` | `gci *.txt \| %{gc $_}` | 批量读取目录下文件 |
| `for i in 1 2 3; do echo $i; done` | `1,2,3 \| %{echo $_}` | 数字遍历循环 |
| `name="zhangsan"` | `$name = "zhangsan"` | 定义变量 |
| `unset name` | `Remove-Variable name` | 删除变量 |
| `while [ $num -lt 10 ]; do ...; done` | `while($num -lt 10){echo $num;$num++}` | 条件循环 |

## 六、网络检测工具

| Bash命令 | PowerShell简写 | PowerShell全写 | 功能介绍 |
|---|---|---|---|
| `curl -I https://baidu.com` | `iwr https://baidu.com -mhe` | `Invoke-WebRequest https://baidu.com -Method Head` | 仅获取响应头 |
| `curl https://www.baidu.com` | `irm https://baidu.com` | `Invoke-RestMethod https://baidu.com` | HTTP 请求并解析返回 |
| `ifconfig` | `ipconfig` | `ipconfig /all` | 查看网卡配置 |
| `ip addr` | `gni` | `Get-NetIPAddress` | 列出所有网卡 IP |
| `netstat -ntlp` | `netstat -ano` | `Get-NetTCPConnection` | 查看监听端口与进程 |
| `nslookup baidu.com` | `Resolve-DnsName baidu.com` | `Resolve-DnsName baidu.com` | DNS 解析 |
| `ping -c 4 baidu.com` | `Test-Connection baidu.com -c 4` | `Test-Connection baidu.com -Count 4` | 指定次数 ping |
| `ping baidu.com` | `ping baidu.com` | `Test-Connection baidu.com` | 连通性检测 |
| `rsync -avz src user@ip:/dst` | 无原生 | 需安装 cwRsync 第三方工具 | 增量文件同步 |

## 七、macOS 专属命令

| Bash(mac专属) | PowerShell | 功能介绍 |
|---|---|---|
| `open .` | 无原生实现（Windows 用 `ii .`） | 打开当前文件夹 |
| `open test.pdf` | 无原生实现（Windows 用 `ii test.pdf`） | 系统默认程序打开文件 |
| `pbcopy < test.txt` | `gc test.txt \| Set-Clipboard` | 内容复制到剪贴板 |
| `pbpaste > out.txt` | `Get-Clipboard > out.txt` | 剪贴板内容导出到文件 |
| `say "Hello World"` | 无对应命令 | 系统语音朗读 |

## 八、系统基础运维命令

| Bash命令 | PowerShell简写 | PowerShell全写 | 功能介绍 |
|---|---|---|---|
| `clear` | `cls` | `Clear-Host` | 清屏 |
| `date` | `Get-Date` | `Get-Date` | 查看系统时间 |
| `history` | `h` | `Get-History` | 查看历史命令 |
| `kill -9 1234` | `stop 1234` | `Stop-Process -Id 1234 -Force` | 强制杀死进程 |
| `ps aux` | `ps` | `Get-Process` | 查看所有进程 |
| `whoami` | `$env:USERNAME` | `$env:USERNAME` | 查看当前登录用户 |
