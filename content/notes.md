# 技术笔记

日常开发中遇到的问题和解决方案。

## Python

### pip 换源

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 虚拟环境

```bash
python -m venv .venv
source .venv/bin/activate  # Linux/Mac
.venv\Scripts\activate     # Windows
```

## Git

### 修改分支名

```bash
git branch -m old-name new-name
```

### 取消已暂存的文件

```bash
git restore --staged <file>
```

## Linux

### 查看端口占用

```bash
lsof -i :8090
netstat -tlnp | grep 8090
```

## Docker

### 清理未使用的资源

```bash
docker system prune -a
```
