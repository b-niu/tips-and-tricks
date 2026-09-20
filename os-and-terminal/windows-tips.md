# Windows 技巧

## 常用操作

```powershell
# 查看占用某端口的进程并结束它
netstat -ano | findstr :8080
taskkill /PID <pid> /F

# PowerShell 查看环境变量
$env:PATH -split ';'
```

## 其他操作

```powershell
# 列出当前目录大小
Get-ChildItem | ForEach-Object { "{0:N2} MB" -f ((Get-ChildItem $_.FullName -Recurse -ErrorAction SilentlyContinue | Measure-Object Length -Sum).Sum / 1MB) }

# 创建符号链接（需管理员）
New-Item -ItemType SymbolicLink -Path C:\link -Target D:\real\target
```

## 踩坑记录

- （示例）PowerShell 执行策略限制脚本：`Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`。
-
