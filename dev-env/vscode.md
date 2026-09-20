# VS Code

## 常用快捷键

| 快捷键 | 作用 |
|--------|------|
| `Ctrl+Shift+P` | 命令面板 |
| `Ctrl+P` | 按文件名快速打开文件 |
| `Ctrl+`` ` | 切换终端 |

## 推荐配置

```jsonc
// settings.json 片段
{
  "editor.formatOnSave": true,
  "files.encoding": "utf8",
  "python.defaultInterpreterPath": ".venv/Scripts/python.exe"
}
```

## 推荐插件

- Python / Pylance
- GitLens — 行内显示 git blame

## 踩坑记录

- （示例）中文乱码：终端编码改为 `chcp 65001` 或设置 `files.encoding: utf8`。
-
