+++
date = '2026-06-15T20:20:00+08:00'
draft = false
title = '从零搭建 AI 助手：在 Windows 上使用 Agnes AI + Hermes Agent 接入 QQ Bot 全记录'
+++

## 1. 介绍：什么是 Hermes Agent？

**Hermes Agent** 是由 Nous Research 开源的 AI Agent 框架，定位与 Claude Code、OpenAI Codex 类似——一个拥有自主工具调用能力的 AI 助手。但 Hermes 有几个独特的优势：

- **完全免费**：Hermes Agent 本身是开源免费的，配合 Agnes AI 的免费 API 额度，整个搭建过程零成本。
- **任意模型接入**：不绑定特定厂商，支持 OpenRouter、Anthropic、OpenAI、DeepSeek、本地模型以及 15+ 其他 provider，甚至可以通过 `custom` 模式接入任意兼容 OpenAI 格式的 API。
- **持久记忆**：跨会话保存用户偏好、环境信息和经验教训，助手越用越懂你。
- **多平台网关**：同一个 Agent 可以运行在 Telegram、Discord、Slack、WhatsApp、微信、QQ 等 10+ 平台上，工具能力全平台一致。
- **技能系统（Skills）**：Agent 可以将复杂任务的解决经验保存为可复用的技能文档，在后续会话中自动加载，实现自我进化。
- **多配置（Profiles）**：可以同时运行多个独立的 Hermes 实例，各自拥有独立的配置、会话和技能库。
- **丰富的工具生态**：终端执行、文件操作、网页搜索、浏览器自动化、定时任务（Cron）、子任务委派（Delegation）、MCP 服务器等开箱即用。

简单来说，Hermes Agent 不是一个聊天机器人，而是一个**有手脚、有记忆、会学习的 AI 助手**——它能执行命令、读写文件、搜索网页、生成图片，甚至定时自动运行任务。

---

## 2. 安装与配置全过程

### 2.1 注册 Agnes AI

首先前往 **Agnes AI** 的 [API 平台](https://apihub.agnes-ai.com) 注册账号，获取 API Key。Agnes AI 提供 OpenAI 兼容格式的接口（Base URL 形如 `https://apihub.agnes-ai.com/v1`），这意味着它可以直接替代 OpenAI 被 Hermes 调用，无需额外适配。

> **💰 Agnes AI 完全免费！** 注册即可获得免费 API 额度，可用于对话、图片生成、视频生成，无需付费即可体验完整的 Hermes Agent 功能。

> **提示：** 后续所有模型调用（对话、图片生成、视频生成）都共用同一个 API Key。

### 2.2 安装 Hermes Agent

Hermes Agent 支持 Linux、macOS 和 Windows。安装非常简单：

```powershell
irm https://res1.hermesagent.org.cn/install.ps1 | iex
```

安装完成后，运行以下命令进行基本配置：

```bash
hermes setup
```

### 2.3 配置自定义 API（接入 Agnes AI）

这是最关键的一步。在 Hermes Agent 的配置中，将模型 provider 设置为 `custom`，并指定 Agnes AI 的接口信息：

```yaml
model:
  default: agnes-2.0-flash          # 使用的模型名称
  provider: custom                   # 自定义 provider 模式
  base_url: https://apihub.agnes-ai.com/v1
  api_key: sk-your_api_key      # 你的 Agnes AI API Key
```

> **如何编辑配置：** 可以直接修改 `~/.hermes/config.yaml` 文件，或者运行 `hermes model`

这样配置后，Hermes Agent 的所有对话请求都会转发到 Agnes AI 的接口，使用 `agnes-2.0-flash` 模型。

### 2.4 配置图片生成

Agnes AI 同时提供图片生成接口，Hermes Agent 原生支持通过自定义后端插件接入。

这一步可以直接用以下指令交给 Hermes Agent 运行：
```
这是Agnes AI图片生成的文档：https://agnes-ai.com/doc/agnes-image-21-flash。根据其内容，配置一个图片生成后端
```

### 2.5 配置联网搜索（SearXNG for Windows）

这一步不是必须的，但强烈建议配置——Hermes Agent 的 `web_search` 和 `web_extract` 工具需要搜索后端才能工作。由于国内网络环境限制，默认的海外搜索 API 通常不可用，因此推荐使用 **SearXNG for Windows** 项目搭建本地搜索引擎。

SearXNG for Windows 是一个开箱即用的 Windows 本地搜索引擎打包项目，集成了 SearXNG 及其依赖，无需 Docker 或 Linux 环境。

**第一步：下载并启动 SearXNG for Windows**

1. 前往 [SearXNG for Windows 项目页面](https://github.com/mbaozi/SearXNGforWindows) 下载最新版本（解压即用）。
2. 运行目录中的 `SearXNG for Windows.bat` 启动服务。
3. 服务默认监听 `http://localhost:8888`，在浏览器中访问即可看到搜索引擎界面。

**第二步：配置 Hermes Agent 使用本地 SearXNG**

在 `config.yaml` 中添加或修改：

```yaml
web:
  backend: http://localhost:8888
  search_backend: searxng
```

这样配置后，Hermes Agent 的所有搜索请求都会转发到你本地的 SearXNG 实例，而 SearXNG 可以聚合百度、搜狗、Bing 等多个中文搜索引擎的结果。

> **提示：** SearXNG for Windows 是一个独立的批处理项目，适合 Windows 用户快速上手。每次启动 Hermes Agent 前确保 SearXNG 服务已在运行。

### 2.6 配置 QQ Bot 网关

Hermes Agent 支持 **QQ Bot（qqbot）** 平台接入。配置步骤如下：

**第一步：获取 QQ Bot 凭证**

在 [QQ 开放平台](https://q.qq.com/) 创建应用，获取 AppID 和 AppSecret。

**第二步：配置网关**

```bash
hermes gateway setup
# 或直接在 config.yaml 中配置 qqbot 平台相关参数
```

**第三步：启动网关**

```bash
hermes gateway run     # 前台运行
hermes gateway install # 安装为后台服务
hermes gateway start   # 启动服务
```

**第四步：启用工具集**

QQ Bot 平台的工具集可以在 `config.yaml` 中配置（`platform_toolsets.qqbot`），根据你的需求启用或禁用。默认情况下会加载浏览器、终端、文件、搜索、图片生成等核心工具。

启动后，在 QQ 中 @你的 Bot 即可开始对话。Bot 能做的事包括：回答问题、执行代码、搜索网页、生成图片、管理定时任务……


## 3. 注意事项

### 🔑 API Key 安全

- API Key 存储在 `~/.hermes/.env` 或 `config.yaml` 中，**切勿提交到 Git 仓库**。
- `config.yaml` 中的 `api_key` 字段建议通过 `.env` 文件引用环境变量，避免明文存储。
- 使用 `hermes auth` 命令可以交互式管理凭证。

### 🌐 网络环境

- Hermes Agent 的默认 `web_search` 后端可能依赖海外搜索 API（OpenRouter 等），在国内环境下通常不可用。
- 推荐使用**本地 SearXNG 实例**作为搜索后端（配置 `web.backend` 和 `web.search_backend: searxng`），可以接入百度、搜狗等国内搜索引擎。
- 如果 SearXNG 服务需要开机自启，建议配置为系统服务或启动脚本。

### 🖥️ Windows 平台特有

- **命令行环境：** Windows 上使用 git-bash（MSYS），注意 `terminal` 工具执行的是 bash 而非 PowerShell。PowerShell 内置命令（如 `Get-ChildItem`、`Select-String`）不会生效，需使用 POSIX 等价命令（`ls`、`grep`）。
- **配置文件编码：** `config.yaml` 不要用 Notepad 以 UTF-8 BOM 格式保存，否则 Hermes 会报 "No models provided" 错误。
- **路径分隔符：** 推荐使用正斜杠 `/`（如 `C:/Users/asus/...`），避免反斜杠的转义问题。
- **快捷键：** Windows 上 `Alt+Enter` 切换全屏，不会输入换行。使用 `Ctrl+Enter` 输入换行。

### 🔄 配置生效

- **工具/技能变更：** 在对话中发送 `/reset` 开始新会话即可生效。
- **配置/网关变更：** 发送 `/restart` 或重新运行网关。
- **插件变更：** 重启网关 (`hermes gateway restart`) 或 CLI 会话。
- **注意事项：** 工具集和技能的变更不会在对话中途生效，必须新建会话。

### 🛠️ 故障排查

- **模型调用失败：** 运行 `hermes doctor` 检查配置和依赖。
- **搜索不可用：** 检查 SearXNG 是否正常运行（`curl http://localhost:8888`）。
- **图片生成失败：** 确认插件已启用且 API Key 正确。
- **网关异常：** 查看日志 `~/.hermes/logs/gateway.log`，搜索 `failed to send` 或 `error`。
- **配置格式错误：** 运行 `hermes config check` 检查配置，`hermes config migrate` 迁移过时配置。

### 💡 经验之谈

1. **善用 Skills 系统**：每次完成复杂任务后，建议将流程保存为 Skill，后续会话会自动加载，让 Agent 越来越擅长你的使用场景。
2. **持久记忆是有用的**：记住保存"能减少未来重复沟通"的事实，而非任务进度和临时状态。
3. **多 Profile 隔离**：如果你同时使用多个模型或平台，可以利用 Profile 功能隔离配置，互不干扰。

## 结语

通过 Hermes Agent + Agnes AI + QQ Bot 的组合，你可以拥有一个**24 小时在线、多平台触达、拥有完整系统访问权限**的 AI 助手。从对话问答到代码执行，从图片生成到定时任务，能力远超传统聊天机器人。

关键就在于：选对模型（Agnes AI 兼容 OpenAI 格式且**完全免费**，接入成本极低）、装好框架（Hermes 安装一行命令搞定）、配好网关（QQ Bot 让你随时随地对话）。

---

> 本文档由 Hermes Agent 自动生成。