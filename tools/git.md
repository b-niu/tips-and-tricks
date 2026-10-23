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

## 分支操作

### 查看分支

```bash
# 列出本地分支（当前分支前带 * 号）
git branch

# 列出所有分支（本地 + 远程），并显示最后一次 commit
git branch -a -v

# 只看远程分支
git branch -r

# 查看分支及其上游（跟踪）关系，能看出本地分支对应哪个远端分支
git branch -vv

# 查看已合并 / 未合并到当前分支的分支（清理分支前很有用）
git branch --merged
git branch --no-merged

# 查看每个分支的最新提交与提交者、时间（比 git branch -v 信息更全）
git for-each-ref --sort=-committerdate refs/heads/ \
  --format='%(committerdate:short)  %(refname:short)  %(authorname)  %(contents:subject)'
```

> `git branch` 只查本地数据库，不联网。要看远端最新状态先 `git fetch -p`（`-p` 会顺带清理已删除的远端分支引用）。

### 切换分支

```bash
# 切换到已有分支
git switch dev

# 切回上一个分支（两个分支间来回切换，不用记名字）
git switch -

# 新建分支并立即切换
git switch -c feature/login

# 基于远程分支创建同名本地分支并建立跟踪关系
git switch feature/login          # 若同名远程分支唯一，Git 会自动跟踪
git switch -c feature/login origin/feature/login   # 显式指定上游

# 切换并带上当前未提交的改动（改动会跟着走，不丢）
git switch dev                    # 无冲突时 Git 自动携带工作区改动

# 强制切换，丢弃当前工作区改动（危险，改动不可恢复）
git switch -f dev
```

> 老版本 Git（< 2.23）用 `git checkout`：`git checkout dev` / `git checkout -b feature/login`。`switch` 只负责切分支，语义比 `checkout` 清楚，推荐新命令。

### 删除分支

```bash
git branch -d feature/login       # 已合并的分支，安全删除
git branch -D feature/login       # 未合并也强删（先确认改动不要了）
git push origin --delete feature/login   # 删除远程分支
```

> 删除前用 `git branch --no-merged` 确认没有未合并的提交。

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
