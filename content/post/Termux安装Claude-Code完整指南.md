---
title: "Termux 安装 Claude Code 完整指南"
date: 2026-08-26T10:30:00+08:00
draft: false
tags: ["Termux", "Claude Code", "AI", "Node.js"]
categories: ["AI 工具", "Android"]
description: "在 Android Termux 中安装、认证和使用 Claude Code，并处理 Node.js、npm、PATH 与 API 环境变量问题。"
---

Claude Code 是 Anthropic 的终端 AI 编程代理。官方当前文档要求 Node.js 18+，标准安装方式是通过 npm 全局安装 `@anthropic-ai/claude-code`。需要注意的是，Anthropic 官方支持列表主要面向 macOS、Linux 和 Windows/WSL，并没有把 Android/Termux 列为正式支持平台。因此，本文的 Termux 部分属于实际可用的移动端运行方案，遇到 Android 特有问题时应以实际版本为准。

## 一、准备 Termux

建议使用来自 F-Droid 或 GitHub Releases 的新版 Termux，而不是长期停留在旧版 Google Play 构建。

先更新软件包：

```bash
pkg update && pkg upgrade -y
```

安装 Claude Code 常用依赖：

```bash
pkg install -y git nodejs npm curl wget ripgrep
```

检查版本：

```bash
node --version
npm --version
git --version
```

Node.js 应至少满足 Claude Code 官方要求的 18+。

## 二、安装 Claude Code

官方标准 npm 安装命令：

```bash
npm install -g @anthropic-ai/claude-code
```

不要使用 `sudo npm install -g`。Termux 本身不需要 sudo，直接使用 Termux 的 `$PREFIX` 环境即可。

安装完成后检查：

```bash
which claude
claude --version
claude doctor
```

如果 `claude` 找不到，先检查 npm 全局 bin：

```bash
npm prefix -g
npm bin -g 2>/dev/null || true
printf '%s\n' "$PATH"
```

Termux 的 npm 全局命令通常应该落在 `$PREFIX/bin` 可访问的位置。如果 PATH 被自行修改过，优先修正 PATH，而不是重复安装。

## 三、首次启动与认证

进入代码项目：

```bash
cd ~/your-project
claude
```

首次启动时按照 Claude Code 的认证流程完成登录。官方支持 Anthropic Console，以及符合条件的 Claude Pro/Max 账户；企业环境还可以使用 Bedrock 或 Vertex AI。

完成认证后，可以先执行：

```text
/init
```

让 Claude Code 分析当前项目并生成项目级 `CLAUDE.md`。项目级指令适合记录架构、构建命令、测试命令和代码规范。

## 四、Termux 中的推荐工作方式

建议把代码放在 Termux 的 Linux 文件系统中，例如：

```bash
mkdir -p ~/src
cd ~/src
git clone <你的仓库>
cd <项目目录>
claude
```

大量项目文件不建议直接放在 `/sdcard` 或 `/storage/emulated/0` 后进行高频编译，因为 Android 共享存储的权限和文件系统语义可能影响 Git、Node、构建工具和文件监听。

如果需要访问手机共享存储，可执行：

```bash
termux-setup-storage
```

但源码、虚拟环境和 `node_modules` 更推荐放在 `~` 下。

## 五、API 环境变量：不要把配置写乱

Claude Code 支持通过环境变量配置 API/Gateway。最容易出现的问题，是在 `.bashrc` 中重复定义 `ANTHROPIC_API_KEY`、`ANTHROPIC_AUTH_TOKEN` 或 `ANTHROPIC_BASE_URL`，导致后面的值覆盖前面的值。

检查当前环境：

```bash
env | grep -E '^ANTHROPIC_|^CLAUDE_'
```

如果只使用官方 Anthropic 服务，应尽量保持环境简单，不要同时设置多个互相冲突的认证变量。

如果使用兼容 Anthropic API 的网关，应明确设置对应的 Base URL 和认证变量。例如：

```bash
export ANTHROPIC_BASE_URL="https://example.com"
export ANTHROPIC_AUTH_TOKEN="你的令牌"
```

真实密钥不要写进 Git 仓库、博客文章或截图。

## 六、常用命令

```bash
claude
claude "检查这个项目的构建错误"
claude -p "总结当前项目结构"
claude --continue
claude --resume <session-id>
claude update
claude doctor
```

自动化脚本可以使用 `-p` 非交互模式，并结合 `--output-format json` 获取结构化输出。

## 七、Termux 常见问题

### 1. `node: command not found`

```bash
pkg install -y nodejs
which node
node --version
```

### 2. `claude: command not found`

检查：

```bash
which claude
npm prefix -g
printf '%s\n' "$PATH"
```

确认 npm 全局 bin 位于 PATH 中。

### 3. npm 权限错误

不要在 Termux 中使用 sudo。检查 npm prefix 是否指向 Termux 环境：

```bash
npm config get prefix
```

正常情况下应使用 Termux 的 `$PREFIX` 路径。

### 4. API 连接异常

先确认环境变量：

```bash
env | grep '^ANTHROPIC_'
```

再检查网络和 Base URL。尤其要排查 `.bashrc`、`.profile`、`.zshrc` 中是否存在重复配置。

## 八、安全建议

Claude Code 能够读取项目、执行命令和修改文件，因此不要在不可信目录中随意授予高权限操作。特别是 `--dangerously-skip-permissions` 应谨慎使用。

同时建议：

- API Key 只放在本机环境变量或安全凭据存储中。
- 不要把 `.env`、Token 和 Cookie 提交到 Git。
- 对陌生仓库先阅读 `README`、构建脚本和权限相关配置。
- 在手机上尤其注意 Termux 进程和文件权限边界。

## 九、结语

在 Termux 中运行 Claude Code 的核心链路很简单：`Termux → Node.js 18+ → npm → Claude Code → 登录/API`。真正容易出问题的并不是安装命令，而是 Android 平台兼容性、PATH、npm 全局目录以及重复的 API 环境变量。

本文按 2026 年 8 月的官方 Claude Code 安装方式整理；由于 Termux 并非 Anthropic 官方重点支持平台，后续版本升级时应优先检查官方系统要求和 `claude doctor`。
