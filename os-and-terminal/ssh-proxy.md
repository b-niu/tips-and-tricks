# SSH 与代理

## 常用命令

```bash
# 免密登录三步：生成密钥 → 上传公钥 → 直接登录
ssh-keygen -t ed25519
ssh-copy-id user@host
ssh user@host
```

## SSH 配置

```ssh-config
# ~/.ssh/config
Host myserver
    HostName 192.168.1.100
    User UserName
    Port 22
    IdentityFile ~/.ssh/id_ed25519
```

## 代理转发

```bash
# 本地端口转发（访问本地 7890 = 远程服务）
ssh -L 7890:localhost:7890 user@host

# 让远程服务器走本地代理
ssh -R 7890:localhost:7890 user@host
```

## 踩坑记录

- （示例）Windows 下 `ssh-copy-id` 不存在：用 `type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh user@host "cat >> ~/.ssh/authorized_keys"`。
-
