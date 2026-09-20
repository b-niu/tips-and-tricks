# uv 命令行与配置标准速查表

## 一、项目管理核心流程

### 1. 项目初始化与开发

```bash
# 初始化新项目（生成 pyproject.toml）
uv init

# 初始化指定 Python 版本的项目
uv init --python 3.11

# 锁存并同步依赖（创建/更新 .venv，安装依赖）
uv sync

# 运行项目中的命令（自动加载 .venv 环境）
uv run python main.py
uv run pytest
```

### 2. 依赖管理

```bash
# 添加普通依赖
uv add pyside6 pillow

# 添加指定版本的依赖
uv add "lancedb>=0.4"

# 添加开发环境依赖
uv add --dev pytest black

# 移除依赖
uv remove pillow

# 升级所有依赖并更新 uv.lock
uv lock --upgrade

# 仅升级指定包
uv lock --upgrade-package lmdb
```

## 二、Python 解释器管理

```bash
# 列出本地已安装及可下载的 Python 版本
uv python list

# 下载并安装指定的 Python 版本
uv python install 3.11 3.12

# 锁定当前项目使用的 Python 版本（生成 .python-version）
uv python pin 3.11
```

## 三、单文件脚本运行（免创建项目）

```bash
# 直接运行脚本并自动安装脚本头部声明的依赖
uv run script.py

# 临时指定依赖运行单文件
uv run --with requests --with "beautifulsoup4>=4.10" script.py
```

## 四、全局工具隔离安装（替代 pipx）

```bash
# 全局安装 CLI 工具
uv tool install ruff
uv tool install black

# 运行全局工具
uvx ruff check .

# 更新所有通过 uv 安装的全局工具
uv tool upgrade --all
```

## 五、镜像源与全局配置（pyproject.toml）

```toml
# 配置 PyPI 镜像源
[[tool.uv.index]]
name = "tuna"
url = "https://pypi.tuna.tsinghua.edu.cn/simple"
default = true

# 配置强行覆盖的全局依赖版本（解决传递依赖兼容问题）
[tool.uv]
override-dependencies = [
    "lmdb>=1.4.1",
]

# 配置源码编译隔离环境的额外依赖
build-isolation-packages = [
    "patch-ng",
]
```

### 1. 双镜像源：tuna 装 PyPI + sjtu 装 PyTorch

命令行临时指定索引的写法：

```bash
uv add torch torchvision torchaudio --index pytorch-cu126=https://mirror.sjtu.edu.cn/pytorch-wheels/cu126/
```

更推荐写成 `pyproject.toml` 配置，一次配置长期生效：

```toml
# 默认源：tuna 装 PyPI 上的普通包
[[tool.uv.index]]
name = "tuna"
url = "https://pypi.tuna.tsinghua.edu.cn/simple"
default = true

# PyTorch CUDA 专用源：只服务 torch 系列包
[[tool.uv.index]]
name = "pytorch-cu126"
url = "https://mirror.sjtu.edu.cn/pytorch-wheels/cu126/"

# 显式绑定：torch 系列固定走 sjtu 源，避免被默认源抢占
[tool.uv.sources]
torch = { index = "pytorch-cu126" }
torchvision = { index = "pytorch-cu126" }
torchaudio = { index = "pytorch-cu126" }
```

配置好后正常添加依赖即可：

```bash
uv add torch torchvision torchaudio
```

> 关键是 `[tool.uv.sources]` 的显式绑定：没有它，uv 会优先从 `default = true` 的 tuna 源解析 torch，装到的就不是 CUDA 12.6 版本。

## 六、常见故障排除（Troubleshooting）

### 1. Windows 平台源码编译缺少依赖（如 `lmdb` 报 `ModuleNotFoundError: No module named 'patch-ng'`）

- **原因**：上游包缺失二进制 Wheel，且未在 `build-system` 中声明构建所需的 Python 模块。
- **方案 A（推荐）**：使用 `override-dependencies` 强行升级该依赖至提供 Windows 预编译 Wheel 的版本。
- **方案 B**：在 `pyproject.toml` 的 `[tool.uv]` 中声明 `build-isolation-packages = ["patch-ng"]`。

### 2. `pyproject.toml` 语法解析错误（`invalid type: map, expected a sequence`）

- **原因**：在 `pyproject.toml` 中将 `override-dependencies` 写成了字典结构。
- **排查与解决**：`override-dependencies` 必须是列表类型。

```toml
# 错误格式
[tool.uv.override-dependencies]
"lmdb" = ">=1.4.1"

# 正确格式
[tool.uv]
override-dependencies = ["lmdb>=1.4.1"]
```

### 3. PyPI 索引连接超时或下载缓慢

- **排查与解决**：清理本地缓存并检查镜像源配置。

```bash
# 清理 uv 缓存
uv cache clean

# 强制刷新依赖解析
uv sync --refresh
```

## 七、不推荐的 uv 使用方式（反模式）

### 1. 在项目目录中直接使用 `uv pip install <package>`

- **不推荐原因**：`uv pip` 系列命令仅用于兼容传统的 `pip` 工作流。在拥有 `pyproject.toml` 的项目中直接使用 `uv pip install` 会导致安装的包未写入 `pyproject.toml` 及 `uv.lock`，破坏环境的可复现性。
- **正确做法**：始终使用 `uv add <package>` 管理项目依赖。

### 2. 手动激活虚拟环境后再运行命令（`.venv\Scripts\activate`）

- **不推荐原因**：`uv` 的设计理念是接管环境调度。手动激活环境容易导致环境变量污染或多项目间虚拟环境串用。
- **正确做法**：直接使用 `uv run <command>` 运行程序，`uv` 会自动识别并激活项目对应的 `.venv`。

### 3. 手动修改 `uv.lock` 文件

- **不推荐原因**：`uv.lock` 是由依赖解析器自动生成的哈希校验文件，手动修改极易损坏数据结构。
- **正确做法**：修改 `pyproject.toml` 后，运行 `uv lock` 或 `uv sync` 让解析器自动更新锁文件。
