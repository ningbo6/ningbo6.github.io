---
title: RAGFlow接入本地Ollama Embedding模型踩坑记录
categories: tools
tags: 
    - RAGFlow
    - Ollama
    - bge-m3
    - Docker
    - embedding
comments: true
keywords: RAGFlow, Ollama, bge-m3, embedding, Docker, 本地模型
description: 在16GB内存服务器上通过Ollama部署bge-m3 embedding模型，解决Docker容器内RAGFlow无法连接Ollama的三个关键问题：监听地址、模型路径、用户权限。
date: 2026-07-11 19:00:00
---

（转载请注明作者和出处：https://ningbo6.github.io 未经允许请勿用于商业用途）

## 背景

在本地部署 RAGFlow 后，希望使用本地 embedding 模型替代云端 API，既节省成本又保护数据隐私。服务器配置为 16GB 内存，经过对比选择了 **Ollama + bge-m3** 方案。

bge-m3 是 BAAI 推出的多语言 embedding 模型，中文效果优秀，内存占用约 2-3GB，完全适合 16GB 内存的服务器。

## 安装 Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

拉取 bge-m3 模型：

```bash
ollama pull bge-m3
```

验证模型：

```bash
curl http://localhost:11434/api/tags
```

返回结果确认模型已就绪：

```json
{
  "models": [{
    "name": "bge-m3:latest",
    "details": {
      "family": "bert",
      "parameter_size": "566.70M",
      "context_length": 8192,
      "embedding_length": 1024
    },
    "capabilities": ["embedding"]
  }]
}
```

## 问题：RAGFlow 无法连接 Ollama

在 RAGFlow 中配置 Ollama embedding 模型后，报错：

```
Fail to bind embedding model: Failed to connect to Ollama.
Please check that Ollama is downloaded, running and accessible.
```

## 排查过程

### 第一步：确认 Ollama 运行状态

```bash
ps aux | grep ollama
```

Ollama 进程正常运行。

### 第二步：从容器内测试连通性

```bash
docker exec docker-ragflow-cpu-1 curl -s http://172.18.0.1:11434/api/tags
```

连接失败，exit code 7（无法连接）。

### 第三步：检查监听地址

```bash
ss -tlnp | grep 11434
```

发现问题：

```
LISTEN  127.0.0.1:11434  0.0.0.0:*
```

**Ollama 默认只监听 `127.0.0.1`，Docker 容器无法访问。**

### 第四步：修改监听地址

```bash
pkill -f "ollama serve"
OLLAMA_HOST=0.0.0.0:11434 /usr/local/bin/ollama serve &
```

再次检查：

```bash
ss -tlnp | grep 11434
# 输出：LISTEN  *:11434  *:*
```

### 第五步：再次测试容器连通性

```bash
docker exec docker-ragflow-cpu-1 curl -s http://172.18.0.1:11434/api/tags
```

连接成功，但返回空模型列表：

```json
{"models": []}
```

### 第六步：检查模型路径

查看 Ollama 日志：

```
OLLAMA_MODELS:/home/a/.ollama/models
total blobs: 0
```

**Ollama 以 root 用户运行，默认查找 `/home/a/.ollama/models`，但模型实际存储在 `/usr/share/ollama/.ollama/models/`。**

### 第七步：权限问题

尝试指定正确的模型路径：

```bash
OLLAMA_MODELS=/usr/share/ollama/.ollama/models /usr/local/bin/ollama serve
```

报错：

```
Error: mkdir /usr/share/ollama/.ollama: permission denied
```

**目录权限为 `750`，仅 `ollama` 用户可访问。**

## 最终解决方案

以 `ollama` 用户身份启动，并设置正确的环境变量：

```bash
sudo -u ollama \
  OLLAMA_HOST=0.0.0.0:11434 \
  OLLAMA_MODELS=/usr/share/ollama/.ollama/models \
  /usr/local/bin/ollama serve &
```

验证：

```bash
curl http://localhost:11434/api/tags
# 返回 bge-m3 模型

docker exec docker-ragflow-cpu-1 curl -s http://172.18.0.1:11434/api/tags
# 容器也能正常访问
```

## 固化配置

创建 systemd 服务，确保重启后自动生效：

```bash
sudo tee /etc/systemd/system/ollama.service << 'EOF'
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_MODELS=/usr/share/ollama/.ollama/models"

[Install]
WantedBy=default.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable ollama
sudo systemctl start ollama
```

## RAGFlow 配置

在 RAGFlow Web 界面中：

1. 进入 **模型管理**（Models）
2. 添加 Embedding 模型，配置如下：

| 字段 | 值 |
|---|---|
| Factory | `Ollama` |
| Base URL | `http://172.18.0.1:11434` |
| 模型名称 | `bge-m3` |
| API Key | 留空 |

> **注意**：`172.18.0.1` 是 Docker 网络 `docker_ragflow` 的网关地址，容器通过这个 IP 访问宿主机服务。可通过以下命令查看：

```bash
docker network inspect docker_ragflow --format '{{range .IPAM.Config}}{{.Gateway}}{{end}}'
```

## 踩坑总结

| 问题 | 原因 | 解决 |
|---|---|---|
| 容器无法连接 Ollama | Ollama 默认监听 `127.0.0.1` | 设置 `OLLAMA_HOST=0.0.0.0:11434` |
| 模型列表为空 | 模型路径不对 | 设置 `OLLAMA_MODELS=/usr/share/ollama/.ollama/models` |
| permission denied | 目录权限限制 | 以 `ollama` 用户启动服务 |

三个问题缺一不可，必须同时解决才能让 Docker 容器中的 RAGFlow 正常访问 Ollama。

## 内存占用参考

| 方案 | 模型 | 内存占用 | 中文效果 |
|---|---|---|---|
| Ollama | bge-m3 | ~2-3GB | ⭐⭐⭐⭐⭐ |
| Ollama | nomic-embed-text | ~1GB | ⭐⭐⭐⭐ |
| Ollama | all-minilm | ~200MB | ⭐⭐ |

16GB 内存服务器推荐使用 bge-m3，中文效果最好且内存充足。
