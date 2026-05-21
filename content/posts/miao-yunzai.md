+++
date = '2026-03-29T19:54:25+08:00'
draft = false
title = 'Miao-Yunzai 安装与使用指南'
+++

## 新安装步骤

1. **安装依赖**
   - 安装 Node.js
   - 安装 Redis for Windows

2. **安装 pnpm**

   ```bash
   npm install -g pnpm
   ```

3. **安装项目依赖**
   ```bash
   pnpm install -P
   ```

## 使用说明

- **登录方式**：一定用手表登录

## 常见问题解决

### 登录token过期

删除 `data/icqq/<QQ ID>` 文件夹下的文件

### Git合并冲突

当出现以下错误时：

```
error: The following untracked working tree files would be overwritten by merge:
```

在插件目录下执行：

```bash
git clean -xfd
```

## 更新后操作

更新完成后执行：

```bash
pnpm install
```