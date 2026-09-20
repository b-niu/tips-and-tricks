# Shell 技巧（PowerShell / Bash）

## PowerShell 片段

```powershell
# 历史命令模糊搜索（PowerShell + PSReadLine）
Set-PSReadLineOption -PredictionSource History

# 批量重命名
Get-ChildItem *.txt | Rename-Item -NewName { $_.Name -replace 'old', 'new' }

# 查看网卡工作速度（名称、链路速度、状态）
Get-NetAdapter | Select Name, LinkSpeed, Status
```

## Bash 片段

```bash
# Ctrl+R 反向搜索历史

# 后台运行且退出终端不中断
nohup python job.py > job.log 2>&1 &

# 查看网卡工作速度与协商模式
ifconfig | grep media
```

## 踩坑记录

- （示例）PowerShell 的 `>` 重定向默认是 UTF-16，处理中文文本先 `| Out-File -Encoding utf8`。
-
