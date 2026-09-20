# Git

## 常用命令

```bash
# 撤销最近一次 commit，保留改动（不加 --hard 就不会丢代码）
git reset --soft HEAD~1

# 把最近一次 commit 的作者信息改对（比如邮箱配错了）
git commit --amend --reset-author --no-edit

# 从远程分支强制拉取，本地完全对齐远端
git fetch origin && git reset --hard origin/main
```

## 进阶命令

```bash
# 挑选某个 commit 到当前分支
git cherry-pick <commit-hash>

# 暂存当前改动，处理完紧急事务后恢复
git stash push -m "描述"     # git stash pop
```

## 配置

### 永久保存账号密码（免重复输入）

```bash
# 启用凭证持久化（默认存储在 ~/.git-credentials）
git config --global credential.helper store
```

> 此方式会明文存储密码，安全性较低。建议配合操作系统密钥链使用（如 macOS 的 osxkeychain）：`git config --global credential.helper osxkeychain`。

### 设置全局用户名、邮箱

```bash
git config --global user.name "your-name"
git config --global user.email "your-email@example.com"
```

### 配置 Git 代理（解决 GitHub 访问慢）

```bash
# 全局 HTTP/HTTPS 代理（对所有仓库生效）
git config --global http.proxy http://127.0.0.1:7897
git config --global https.proxy http://127.0.0.1:7897

# 仅对 GitHub 生效，不影响其他仓库
git config --global http.https://github.com.proxy http://127.0.0.1:7897

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
git config --global --unset http.https://github.com.proxy
```

> 需确保本地代理服务（如 Clash、V2Ray）已运行并监听对应端口（示例为 7897）。

### 常用配置查看与修改

```bash
git config --list          # 查看当前配置
git config user.name       # 查看特定配置
git config --global -e     # 编辑全局配置文件
```

以上命令通用，适用于 macOS/Linux/Windows。

## 踩坑记录

- （示例）`.gitignore` 对已跟踪文件不生效：先 `git rm -r --cached .` 再重新 add。
-
