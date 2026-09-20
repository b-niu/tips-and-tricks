# tmux

## 配置（~/.tmux.conf）

### 先搞清楚：.tmux.conf 不是 bash 脚本

`.tmux.conf` 是 **tmux 自己的配置语言**——本质上是一个"tmux 命令列表"，由 tmux 启动时逐行解析执行，**完全不经过 bash**。它长得像 shell 只是巧合（`#` 注释、`\` 续行、引号字符串恰好语法相同），但 `set`、`bind`、`setw` 都是 tmux 命令，bash 里并不存在。配置里写 `echo hello` 会直接报 `unknown command`。

注释规则（与 shell 一致）：

```bash
# 整行注释
set -g mouse on    # 行内注释也有效
```

- **`\` 续行符后面不能跟注释**——`\` 必须是行尾最后一个字符，写成 `\    # xxx` 会导致解析出错。
- **引号字符串内的 `#` 是内容不是注释**。

如果确实需要在配置里跑 shell 逻辑，用 `run-shell "command"` 出口；tmux 3.0+ 还有 `%if/%endif` 预处理指令可做条件配置，但那也是 tmux 自己的语法，不是 bash。

### 示例配置

```bash
# ===== 基础 =====

# 启用鼠标支持（固定式，写进配置而非每次 set-option 临时开启）
# 生效方式：改完后在 tmux 内执行 Ctrl+b : source-file ~/.tmux.conf
set -g mouse on

# 256 色支持
set -g default-terminal "tmux-256color"
set -ga terminal-overrides ",*256col*:Tc"

# 窗口和面板编号从 1 开始（0 太顺手远）
set -g base-index 1
setw -g pane-base-index 1

# 关闭窗口后自动重新编号
set -g renumber-windows on

# 历史缓冲区加大
set -g history-limit 100000

# ===== 快捷键定制 =====

# r 键重载配置
bind r source-file ~/.tmux.conf \; display "Reloaded!"

# 更直觉的分屏键：| 左右分，- 上下分
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
unbind '"'
unbind %

# 新建窗口/分屏时保留当前路径
bind c new-window -c "#{pane_current_path}"

# vi 风格复制模式
setw -g mode-keys vi
bind v copy-mode
bind -T copy-mode-vi v send-keys -X begin-selection
bind -T copy-mode-vi y send-keys -X copy-pipe-and-cancel "pbcopy"
```

## 会话管理

```bash
tmux new -s work          # 新建名为 work 的会话
tmux ls                   # 列出所有会话
tmux a -t work            # 重新接入会话（detach 后恢复现场）
tmux a                    # 接入最近一个会话
tmux kill-session -t work # 关闭指定会话
```

## 快捷键清单（前缀键：Ctrl+b）

### 会话与窗口

| 快捷键 | 作用 |
|--------|------|
| `Ctrl+b d` | 分离会话（detach），回到原终端 |
| `Ctrl+b $` | 重命名会话 |
| `Ctrl+b c` | 新建窗口 |
| `Ctrl+b ,` | 重命名当前窗口 |
| `Ctrl+b &` | 关闭当前窗口 |
| `Ctrl+b 1-9` | 切换到指定编号窗口 |
| `Ctrl+b n` / `p` | 下一个 / 上一个窗口 |
| `Ctrl+b w` | 窗口与会话列表（可视化选择） |

### 面板（pane）

| 快捷键 | 作用 |
|--------|------|
| `Ctrl+b %` | 左右分屏 |
| `Ctrl+b "` | 上下分屏 |
| `Ctrl+b 方向键` | 按方向切换面板 |
| `Ctrl+b o` | 依次切换面板 |
| `Ctrl+b x` | 关闭当前面板 |
| `Ctrl+b z` | 当前面板全屏 / 恢复（zoom） |
| `Ctrl+b Space` | 循环切换面板布局 |
| `Ctrl+b Ctrl+方向键` | 调整面板边框大小 |

### 复制与滚动

| 快捷键 | 作用 |
|--------|------|
| `Ctrl+b [` | 进入复制/滚动模式（q 退出） |
| 鼠标滚轮 | 直接向上翻看（需 mouse on） |
| 鼠标拖选 | 选中即复制到 tmux 缓冲区（需 mouse on） |
| `Ctrl+b ]` | 粘贴缓冲区内容 |

### 其他

| 快捷键 | 作用 |
|--------|------|
| `Ctrl+b ?` | 列出全部快捷键 |
| `Ctrl+b :` | 命令行模式（如 `source-file ~/.tmux.conf`） |
| `Ctrl+b t` | 显示时钟 |

## MobaXterm + SSH：鼠标拖选直接进 Windows 剪贴板

> 参考文档：[tmux 官方 Wiki - Clipboard](https://github.com/tmux/tmux/wiki/Clipboard)

**原理**：MobaXterm 不支持 OSC 52，走不了 `set-clipboard` 路线；但它自带 X Server，可以把 `xclip` 作为桥梁：`tmux → xclip → MobaXterm X Server → Windows 剪贴板`。tmux 的鼠标拖选是 pane-aware 的（不会跨 pane 选中），只是默认复制进 tmux 内部 buffer，加一步管道就送到 Windows。

### 第一步：验证 X11 链路

```bash
# 1. 确认 X11 转发已生效，应输出 localhost:10.0 之类
echo "$DISPLAY"

# 2. 确认/安装 xclip
which xclip || sudo apt install xclip

# 3. 测试链路：执行后到 Windows 记事本 Ctrl+V
echo "MobaXterm clipboard test" | xclip -selection clipboard
```

第 3 步能在 Windows 里粘贴出来，链路即通。

### 第二步：绑定鼠标释放 → xclip（完整配置过程）

这条绑定**本质上是一条 tmux 命令**，两种用法：交互式执行（`Ctrl+b` `:` 后输入，临时生效，重启即失）或写进 `~/.tmux.conf`（永久生效）。推荐写配置，最终文件示例：

```bash
# ~/.tmux.conf

# 启用鼠标支持（固定式）
set -g mouse on

# 鼠标拖选松开 → xclip → MobaXterm X Server → Windows 剪贴板
# 注意：tmux 默认 mode-keys 是 emacs，鼠标拖选走 copy-mode 表，所以 -T 写 copy-mode
# 只有显式配了 setw -g mode-keys vi，才写 copy-mode-vi
bind -T copy-mode MouseDragEnd1Pane send -X copy-pipe-and-cancel "xclip -selection clipboard -i"
```

逐部分拆解这行绑定：

| 片段 | 含义 |
|------|------|
| `bind` | 定义一个快捷键绑定 |
| `-T copy-mode` | 绑定作用在 emacs 风格复制模式的按键表上（tmux 默认模式）。**必须与 `mode-keys` 匹配**：配了 `mode-keys vi` 才用 `copy-mode-vi`，否则绑定是死代码 |
| `MouseDragEnd1Pane` | 触发事件：鼠标左键拖选结束、松开的瞬间 |
| `send -X ...` | 向复制模式发送一个动作命令 |
| `copy-pipe-and-cancel` | 复制选中文本 → 管道发给引号里的命令 → 退出复制模式（黄色选区因此消失） |
| `"xclip -selection clipboard -i"` | 管道接收方：把文本写入 X11 的 clipboard 选择区，MobaXterm 的 X Server 同步进 Windows 剪贴板 |

完整链路：

```text
拖选 → 松开 → tmux 复制选中文本 → xclip → MobaXterm X Server → Windows 剪贴板 → Ctrl+V
```

### 第三步：加载配置（tmux 是 C/S 架构，别被"重启"骗了）

改完配置后**必须让服务器重新加载**，否则旧配置一直驻留——detach 再 attach、关掉终端窗口都不算重启，`tmux` 服务器进程是独立存活在后台的：

```bash
# 方式 A：热加载（不丢会话，推荐）
tmux source-file ~/.tmux.conf

# 方式 B：真·重启（杀掉后台服务器，所有会话关闭）
tmux kill-server
```

### 第四步：验证与排错

```bash
# 1. 绑定是否注册到正确的表（实际使用的那张表应带 xclip 参数）
tmux list-keys | grep MouseDragEnd

# 2. 模式确认（emacs = 走 copy-mode 表；vi = 走 copy-mode-vi 表）
tmux show -gw mode-keys

# 3. X11 转发是否有值（空 = 链路第一环就断了）
echo $DISPLAY

# 4. xclip 是否安装
which xclip

# 5. 绕过 tmux 单测 X11 链路：执行后 Windows Ctrl+V 应出现 test123
echo "test123" | xclip -selection clipboard -i
```

判断逻辑：第 1 条看"配置进没进去"，第 2 条看"表名对不对"，第 3、4、5 条看"X11 链路通不通"。绑定注册成功但 Windows 粘不出来时，管道命令失败是**静默的**，不会报错，只能靠第 5 条逐环排查。

**补充说明**：

- 松开鼠标黄色选区消失是 `copy-pipe-and-cancel` 的正常行为（复制后自动退出 copy mode），不是失败；判断标准是 Windows 里能不能粘出来。
- 想选完保持高亮可改用 `copy-pipe`（去掉 `-and-cancel`），但一般没必要。
- MobaXterm 里「Terminal → Features → Disable xterm-style mouse reporting」这个开关不推荐用——关掉 tmux 鼠标机制会失去 pane-aware 选择，得不偿失。

## 踩坑记录

- `set -g mouse on` 临时用 `Ctrl+b : set -g mouse on` 只对当前会话生效，重连就失效——固定启用必须写进 `~/.tmux.conf` 并 reload。
- SSH 远程使用时，本地终端窗口大小变化后面板错乱：`Ctrl+b : resize-window -A` 让窗口适应最小客户端。
- 鼠标模式开启后，终端原生的文本选择被拦截：按住 `Shift` 再拖选即可走终端原生复制。
- **改配置后"重启 tmux"没效果**：tmux 是 C/S 架构，detach/关窗口/重新 attach 都不会杀掉后台服务器进程，旧配置一直驻留。必须 `tmux source-file ~/.tmux.conf` 热加载，或 `tmux kill-server` 真重启。
- **`bind -T` 的按键表名必须和 `mode-keys` 匹配**：tmux 默认 `mode-keys emacs`，鼠标拖选走 `copy-mode` 表；写成 `-T copy-mode-vi` 而没配 `mode-keys vi` 时，绑定注册成功但永远不会被触发（死代码）。用 `tmux show -gw mode-keys` + `tmux list-keys | grep MouseDragEnd` 对照检查。
- 管道复制命令（如 xclip）失败是静默的：绑定注册成功、拖选高亮正常，但 Windows 粘不出新内容时，先 `echo test | xclip -selection clipboard -i` 绕过 tmux 单测 X11 链路。
