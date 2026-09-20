# tips-and-tricks

个人工具箱与配置备忘录。只记「容易忘的」和「踩过坑的」，不搬运官方文档。

## 目录导航

### 🛠 常用工具 [tools/](tools/)

| 笔记 | 内容 |
|------|------|
| [git.md](tools/git.md) | Git 常用命令与踩坑记录 |
| [docker.md](tools/docker.md) | Docker 常用命令 |
| [tmux.md](tools/tmux.md) | tmux 配置与快捷键清单 |

### 💻 系统与终端 [os-and-terminal/](os-and-terminal/)

| 笔记 | 内容 |
|------|------|
| [windows-tips.md](os-and-terminal/windows-tips.md) | Windows 运维与常用命令 |
| [shell-tricks.md](os-and-terminal/shell-tricks.md) | PowerShell / Bash 快捷键与脚本片段 |
| [bash-vs-powershell.md](os-and-terminal/bash-vs-powershell.md) | Bash 与 PowerShell 命令对照表 |
| [ssh-proxy.md](os-and-terminal/ssh-proxy.md) | SSH 免密、代理配置 |

### 🐍 开发环境 [dev-env/](dev-env/)

| 笔记 | 内容 |
|------|------|
| [python-uv.md](dev-env/python-uv.md) | Python 包管理与环境配置 |
| [python-pandas.md](dev-env/python-pandas.md) | pandas 数据处理常用片段 |
| [vscode.md](dev-env/vscode.md) | VS Code 快捷键与推荐配置 |

## 写作约定

1. 只记自己真正用到的参数组合和解法，附上踩坑场景。
2. 代码块标明语言（`bash` / `powershell` / `toml` / `yaml`），获得语法高亮。
3. 一个工具一个文件，超过 300 行考虑拆分。

## 目录索引

<details>
<summary><b>展开查看全部章节</b></summary>

### tools/

- [git.md](tools/git.md)
  - [常用命令](tools/git.md#常用命令) · [进阶命令](tools/git.md#进阶命令) · [配置](tools/git.md#配置) · [踩坑记录](tools/git.md#踩坑记录)
- [docker.md](tools/docker.md)
  - [常用命令](tools/docker.md#常用命令) · [进阶命令](tools/docker.md#进阶命令) · [踩坑记录](tools/docker.md#踩坑记录)
- [tmux.md](tools/tmux.md)
  - [配置（~/.tmux.conf）](tools/tmux.md#配置tmuxconf) · [会话管理](tools/tmux.md#会话管理) · [会话与窗口](tools/tmux.md#会话与窗口) · [面板pane](tools/tmux.md#面板pane) · [复制与滚动](tools/tmux.md#复制与滚动) · [MobaXterm 剪贴板](tools/tmux.md#mobaxterm--ssh鼠标拖选直接进-windows-剪贴板) · [踩坑记录](tools/tmux.md#踩坑记录)

### os-and-terminal/

- [windows-tips.md](os-and-terminal/windows-tips.md)
  - [常用操作](os-and-terminal/windows-tips.md#常用操作) · [其他操作](os-and-terminal/windows-tips.md#其他操作) · [踩坑记录](os-and-terminal/windows-tips.md#踩坑记录)
- [shell-tricks.md](os-and-terminal/shell-tricks.md)
  - [PowerShell 片段](os-and-terminal/shell-tricks.md#powershell-片段) · [Bash 片段](os-and-terminal/shell-tricks.md#bash-片段) · [踩坑记录](os-and-terminal/shell-tricks.md#踩坑记录)
- [bash-vs-powershell.md](os-and-terminal/bash-vs-powershell.md)
  - [一、文件基础操作](os-and-terminal/bash-vs-powershell.md#一文件基础操作)
  - [二、文件夹操作 & Find 查找](os-and-terminal/bash-vs-powershell.md#二文件夹操作--find-查找)
  - [三、压缩解压（tar / zip）](os-and-terminal/bash-vs-powershell.md#三压缩解压tar--zip)
  - [四、文本处理、管道过滤、统计](os-and-terminal/bash-vs-powershell.md#四文本处理管道过滤统计)
  - [五、变量、环境变量、循环语法](os-and-terminal/bash-vs-powershell.md#五变量环境变量循环语法)
  - [六、网络检测工具](os-and-terminal/bash-vs-powershell.md#六网络检测工具)
  - [七、macOS 专属命令](os-and-terminal/bash-vs-powershell.md#七macos-专属命令)
  - [八、系统基础运维命令](os-and-terminal/bash-vs-powershell.md#八系统基础运维命令)
- [ssh-proxy.md](os-and-terminal/ssh-proxy.md)
  - [常用命令](os-and-terminal/ssh-proxy.md#常用命令) · [SSH 配置](os-and-terminal/ssh-proxy.md#ssh-配置) · [代理转发](os-and-terminal/ssh-proxy.md#代理转发) · [踩坑记录](os-and-terminal/ssh-proxy.md#踩坑记录)

### dev-env/

- [python-uv.md](dev-env/python-uv.md)
  - [一、项目管理核心流程](dev-env/python-uv.md#一项目管理核心流程)
  - [二、Python 解释器管理](dev-env/python-uv.md#二python-解释器管理)
  - [三、单文件脚本运行（免创建项目）](dev-env/python-uv.md#三单文件脚本运行免创建项目)
  - [四、全局工具隔离安装（替代 pipx）](dev-env/python-uv.md#四全局工具隔离安装替代-pipx)
  - [五、镜像源与全局配置（pyproject.toml）](dev-env/python-uv.md#五镜像源与全局配置pyprojecttoml)
  - [六、常见故障排除（Troubleshooting）](dev-env/python-uv.md#六常见故障排除troubleshooting)
  - [七、不推荐的 uv 使用方式（反模式）](dev-env/python-uv.md#七不推荐的-uv-使用方式反模式)
- [python-pandas.md](dev-env/python-pandas.md)
  - [常用片段](dev-env/python-pandas.md#常用片段) · [更多片段](dev-env/python-pandas.md#更多片段) · [踩坑记录](dev-env/python-pandas.md#踩坑记录)
- [vscode.md](dev-env/vscode.md)
  - [常用快捷键](dev-env/vscode.md#常用快捷键) · [推荐配置](dev-env/vscode.md#推荐配置) · [推荐插件](dev-env/vscode.md#推荐插件) · [踩坑记录](dev-env/vscode.md#踩坑记录)

</details>
