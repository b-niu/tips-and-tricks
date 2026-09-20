# Docker

## 常用命令

```bash
# 进入运行中的容器
docker exec -it <container> bash

# 清理无用镜像/容器/网络（不会动有 tag 且被使用的）
docker system prune -f

# 查看容器日志（跟踪最近 100 行）
docker logs -f --tail 100 <container>
```

## 进阶命令

```bash
# 挂载目录并映射端口运行
docker run -d --name myapp -p 8080:80 -v D:/data:/data myimage

# 把容器内文件拷出来
docker cp <container>:/path/in/container D:/local/path
```

## 踩坑记录

- （示例）Windows 下挂载路径要用 `/d/data` 或 `D:/data`（正斜杠），反斜杠会报错。
-
