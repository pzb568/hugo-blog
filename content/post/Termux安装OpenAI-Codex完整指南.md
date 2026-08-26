---
title: "Termux 安装 OpenAI Codex CLI 完整指南"
date: 2026-08-26T10:35:00+08:00
draft: false
tags: ["Termux", "OpenAI", "Codex", "AI", "CLI"]
categories: ["AI 工具", "Android"]
description: "在 Android Termux 中安装 OpenAI Codex CLI，完成 npm 安装、登录、项目使用和 Android/架构兼容性排查。"
---

OpenAI Codex CLI 是运行在终端中的编码代理。官方当前仓库提供 npm 安装方式，也提供 Linux/macOS 的独立安装器和预编译版本。对于 Android Termux，最重要的区别是：官方提供的是 Linux、macOS、Windows 等平台的安装路径，Android/Termux 并不是独立的一等官方平台，因此在 Termux 上应优先使用 npm 路径并根据实际 CPU 架构验证二进制是否可运行。

## 一、准备 Termux

更新系统：

```bash
pkg update && pkg upgrade -y
```

安装基础环境：

```bash
pkg install -y git nodejs npm curl ripgrep
```

确认 Node.js 和 npm：

```bash
node --version
npm --version
uname -m
```

如果输出 `aarch64`，说明手机使用 ARM64，这是现代 Android 手机最常见的架构。

## 二、安装 Codex CLI

官方仓库当前提供 npm 安装方式：

```bash
npm install -g @openai/codex
```

然后检查：

```bash
which codex
codex --version
```

如果 npm 安装成功但 `codex` 不在 PATH 中，检查：

```bash
npm config get prefix
printf '%s\n' "$PATH"
```

不要使用 `sudo npm install -g`。Termux 的软件和 npm 全局包应保持在 Termux 自己的用户空间。

## 三、首次运行

进入项目目录：

```bash
cd ~/src/your-project
codex
```

Codex 首次运行时可以选择 **Sign in with ChatGPT**。官方说明支持使用 ChatGPT 账户，也可以通过 API Key 进行额外配置。

如果选择 ChatGPT 登录，按照终端显示的认证流程完成授权即可。

## 四、先验证 Codex 是否真正可用

安装成功不等于 Android 上的运行时一定兼容。建议执行：

```bash
codex --version
codex
```

然后在项目中进行低风险测试，例如：

```text
请只分析当前项目结构，不要修改任何文件。
```

如果能够正常进入 Codex 会话并读取项目，说明当前 Termux 环境基本可用。

## 五、为什么不能直接照搬 Linux ARM64 二进制

官方 Releases 中存在 Linux ARM64 构建，例如 `aarch64-unknown-linux-musl`。但 Android 使用的是 Android/Bionic 用户空间，而普通 Linux 使用 glibc 或 musl 等 Linux 用户空间。

因此：

```text
Android aarch64 ≠ Linux aarch64
```

即使 CPU 都是 ARM64，也不能仅凭 `uname -m` 判断一个 Linux ELF 就能直接在 Android 上运行。

在 Termux 中更推荐先使用：

```bash
npm install -g @openai/codex
```

然后让 npm 包提供的安装逻辑处理当前平台。如果最终下载到的 native binary 与 Android 不兼容，再根据具体报错判断是 ABI、动态链接器、系统调用还是权限问题。

## 六、常见架构与动态链接问题

查看系统：

```bash
uname -a
uname -m
getprop ro.product.cpu.abi
```

查看 Codex 文件：

```bash
which codex
file "$(which codex)"
```

如果命令实际上是一个脚本或 Node wrapper，可以继续定位 npm 全局包：

```bash
npm root -g
```

如果 native executable 报类似 `No such file or directory`、`Exec format error` 或动态库错误，不要反复执行安装命令，应先确定实际下载的 binary 架构和 ABI。

## 七、Termux 中的项目目录建议

推荐：

```bash
mkdir -p ~/src
cd ~/src
git clone <你的仓库>
cd <项目目录>
codex
```

不建议把大型源码树和 `node_modules` 长期放在 Android 共享存储目录。共享存储对 Linux 权限、符号链接、文件监听等能力存在限制。

如需访问共享存储：

```bash
termux-setup-storage
```

但 Codex 的实际工作目录仍建议放在 `~/` 下。

## 八、API Key 与环境变量

如果不使用 ChatGPT 登录，而是使用 API Key，应按照 OpenAI 当前 Codex 文档配置认证。

检查是否存在旧环境变量：

```bash
env | grep -E '^(OPENAI_|CODEX_)'
```

尤其不要把 API Key 直接写入 Git 仓库，也不要把包含密钥的 `.bashrc` 发布到博客。

如果以前使用过其他 OpenAI 兼容网关，建议先检查并清理旧变量，避免 Codex 实际连接到错误的 endpoint。

## 九、常用工作流

进入项目：

```bash
cd ~/src/project
codex
```

典型任务可以直接描述：

```text
分析这个项目的构建错误，先不要修改文件。
```

```text
检查 GitHub Actions 最近一次失败的原因，并给出最小修复方案。
```

```text
修复测试失败的问题，修改后运行相关测试。
```

对于涉及删除、覆盖或批量修改的任务，应先让 Codex 给出计划，再执行修改。

## 十、与 Claude Code 共存

Claude Code 和 Codex 都是终端 AI 编程代理，可以同时安装：

```bash
npm install -g @anthropic-ai/claude-code
npm install -g @openai/codex
```

检查：

```bash
claude --version
codex --version
```

二者使用独立命令，不需要二选一。

建议：

```text
claude   → Claude Code 工作流
codex    → OpenAI Codex 工作流
```

不要把两个工具的认证环境变量混在一起管理。

## 十一、故障排查顺序

### 1. npm 安装失败

```bash
pkg update
pkg upgrade -y
pkg install -y nodejs npm
node --version
npm --version
npm install -g @openai/codex
```

### 2. `codex: command not found`

```bash
which codex
npm config get prefix
printf '%s\n' "$PATH"
```

### 3. `Exec format error`

```bash
uname -m
file "$(which codex)"
```

重点检查 Android 与 Linux binary 的兼容性。

### 4. 登录失败

先确认网络正常，然后直接运行：

```bash
codex
```

按终端提示重新进行 ChatGPT 登录。若使用 API Key，则检查相关认证环境变量是否正确。

## 十二、安全注意事项

Codex 能够执行代码、修改文件并运行命令。不要在包含重要私钥、密码或个人数据的目录中直接运行高权限自动化任务。

尤其避免把下面内容提交到 Git：

```text
.env
API Key
OAuth Token
SSH 私钥
Cloudflare Token
数据库密码
```

## 十三、结语

在 Termux 上安装 Codex 的最简路径是：

```bash
pkg update && pkg upgrade -y
pkg install -y nodejs npm git
npm install -g @openai/codex
codex
```

但 Android 与普通 Linux 的 ABI 不同，因此真正需要关注的是 native binary 的兼容性，而不是单纯的 npm 安装是否完成。对于 Termux，建议以 npm 安装结果和实际运行测试为准，并保留 `uname -m`、`file` 和完整错误信息，便于定位 Android 特有问题。
