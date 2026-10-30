---
date: 2026-10-30
authors:
  - lee
categories:
  - AI
tages:
  - Agents
  - Pi
---
# 个人使用的PI Agent Extensions整理

## 安装&管理方式

### 安装插件

```bash
pi install 在PI官网上找到的名称
```

### 查看已经安装了什么插件

```bash
pi list
```

## 本地跑模型相关

- npm:pi-llama-cpp，设置`LLAMA_SERVER_URL`即可食用，如使用了api-key，输入/login登陆即可
- npm:pi-llama-cpp-stats，用进度条的方式显示llama.cpp返回的进度，比较好看

## Tools相关

- npm:pi-json-tools，**必须安装**的一项，否则模型返回的JSON不认，会导致Tools导用失败

## Vibe Coding

- npm:pi-lens，**重要**，LSP诊断，自动修复文件，注入相关消息至上下文
- npm:pi-web-access，如字面意思，接入MCP服务器，搜索网络上的信息等，部分服务需配置key

## 杂项

- npm:@misterzhou/pi-status-line，状态栏中统计显示Token使用情况
