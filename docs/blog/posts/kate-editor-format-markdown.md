---
date: 2026-10-30
authors:
  - lee
categories:
  - Linux
tages:
  - KDE
  - Kate
---
# Kate编辑器如何格式化markdown文件

## 背景

由于Kate自带的format插件不支持markdown，而format插件又不能通过配置的方式加入markdown支持，因此，需通过“外部工具”的方式实现需求

## 所需工具

### Prettier，需用npm安装

```bash
npm install -g prettier
```

## 配置方法

### "工具"->"外部工具"->"配置"，新增一个外部工具，分类可自行定义，个人是将它放在“格式化”分类中

- 名称：格式化Markdown
- 可执行文件：prettier或其完整路径
- **使用文件路径方式避免中文乱码**参数：--write %{Document:FilePath}
- 输入：留空即可
- 工作目录：留空即可
- MIME类型：text/markdown
- 保存：无
- **保存时自动格式化**触发器：保存后
- **重要**钩选“执行后重新加载当前文档”
- 输出：忽略
- 编辑器命令：留空即可
