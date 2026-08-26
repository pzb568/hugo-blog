---
title: "Termux 安装 Hermes Agent 完整指南"
date: 2026-08-26T10:40:00+08:00
draft: false
tags: ["Termux", "Hermes Agent", "AI Agent", "Python", "MCP"]
categories: ["AI 工具", "Android"]
description: "按照 Hermes Agent 官方 Termux 路径，在 Android 手机上安装、配置、诊断 Hermes Agent，并处理 Python、ANDROID_API_LEVEL 和 Android 依赖问题。"
---

Hermes Agent 是 Nous Research 开源的 AI Agent。与普通聊天 CLI 不同，它强调工具调用、长期记忆、技能学习、MCP、后台任务和多种模型提供商。

Hermes 官方目前把 Android/Termux 列为 Tier 2 平台，也就是持续维护但属于 best-effort 支持。官方已经提供专门的 Termux 安装路径，因此在 Android 上应优先采用官方 Termux 安装方式，而不是直接套用桌面 Linux 的依赖方案。

## 一、Termux 基础环境

先更新软件包：

```bash
pkg update && pkg upgrade -y
```

官方手动路径使用以下依赖：

```bash
pkg install -y git python clang rust make pkg-config libffi openssl nodejs ripgrep ffmpeg
```

检查：

```bash
python --version
node --version
git --version
rg --version
ffmpeg -version
```

## 二、推荐：官方一键安装

Hermes 官方目前提供 Termux 感知的安装器：

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

安装器会检测 Termux，并使用 Android 对应的安装流程，包括 `pkg`、Python venv 和 Termux 专用依赖。

安装结束后重新加载 shell：

```bash
source ~/.bashrc
```

然后检查：

```bash
hermes version
hermes doctor
```

最后启动：

```bash
hermes
```

## 三、需要完全控制时：手动安装

如果一键安装失败，官方文档给出了完全显式的 Termux 流程。

首先获取源码：

```bash
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
```

创建虚拟环境：

```bash
python -m venv venv
source venv/bin/activate
```

设置 Android API Level：

```bash
export ANDROID_API_LEVEL="$(getprop ro.build.version.sdk)"
```

升级 Python 构建工具：

```bash
python -m pip install --upgrade pip setuptools wheel
```

然后安装官方测试的 Termux extra：

```bash
python -m pip install -e '.[termux]' -c constraints-termux.txt
```

如果只需要最小核心：

```bash
python -m pip install -e '.' -c constraints-termux.txt
```

## 四、让 `hermes` 永久可用

手动安装完成后：

```bash
ln -sf "$PWD/venv/bin/hermes" "$PREFIX/bin/hermes"
```

这样 `$PREFIX/bin` 中就会存在 Hermes 命令，不需要每次新开 Termux 都重新激活 venv。

验证：

```bash
hermes version
hermes doctor
```

## 五、为什么必须设置 `ANDROID_API_LEVEL`

Hermes 的部分 Python 依赖会使用 Rust/maturin 构建。在 Android 上，构建系统需要知道目标 Android API Level。

官方 Termux 文档特别指出，像 `jiter` 这样的依赖可能需要：

```bash
export ANDROID_API_LEVEL="$(getprop ro.build.version.sdk)"
```

如果出现 `maturin`、Rust native extension 或 `ANDROID_API_LEVEL` 相关错误，首先确认：

```bash
echo "$ANDROID_API_LEVEL"
getprop ro.build.version.sdk
```

然后重新执行 pip 安装。

## 六、首次配置模型

Hermes 安装完成后，可以直接运行：

```bash
hermes model
```

也可以运行完整设置向导：

```bash
hermes setup
```

如果使用官方 Nous Portal，也可以按当前版本的向导完成 OAuth 配置。

如果使用自己的模型 API，可以按照 Hermes 当前支持的 provider 配置 API Key 和模型。

## 七、检查 Hermes 当前状态

推荐安装完成后固定执行：

```bash
hermes doctor
```

它能够帮助检查 Python 环境、依赖、配置和工具状态。

日常更新：

```bash
hermes update
```

查看帮助：

```bash
hermes --help
hermes model --help
hermes tools --help
```

## 八、Termux 下最重要的限制

Android 版本并不是桌面/服务器完整功能的等价物。官方当前文档明确指出：

### `.[all]` 不属于 Android 推荐路径

Android 当前应使用：

```bash
python -m pip install -e '.[termux]' -c constraints-termux.txt
```

而不是强行安装：

```bash
python -m pip install -e '.[all]'
```

### Voice 依赖存在 Android 限制

`voice` extra 会涉及 `faster-whisper` 和 `ctranslate2`，而 `ctranslate2` 当前没有适用于 Android 的标准 wheel，因此不能把桌面语音依赖方案原样搬到 Termux。

### Docker 不可用

Termux 内不能把 Docker 作为 Hermes 的标准终端隔离后端。

### 浏览器自动安装被跳过

官方 Termux 安装流程会跳过未经验证的浏览器/Playwright 引导。因此浏览器相关能力应视为实验功能，而不是 Android 上的默认稳定功能。

### 后台任务可能被 Android 挂起

即使 Hermes 本身配置正确，Android 的后台进程管理也可能暂停 Termux。Telegram gateway 等长期后台服务因此属于 best-effort，而不是服务器上的标准托管服务。

## 九、Node.js 与浏览器工具

官方 Termux 核心路径会准备 Node.js，但不会强制执行完整浏览器 bootstrap。

如果之后需要实验 Node/browser 工具：

```bash
pkg install nodejs-lts
npm install
```

在 Termux 中，相关工具会搜索 Termux 的 bin 目录，因此通常不需要为了 `npx` 再额外修改 PATH。

## 十、Hermes 配置文件

根据当前安装方式，Hermes 的用户配置位于 `~/.hermes` 体系中。API Key 可以通过 Hermes 的配置命令或 `.env` 配置。

检查目录：

```bash
ls -la ~/.hermes
```

不要公开：

```text
~/.hermes/.env
API Key
OAuth Token
个人聊天数据
```

## 十一、Termux 安装失败时的标准排查顺序

### 1. Python 环境

```bash
python --version
which python
```

### 2. Android API Level

```bash
echo "$ANDROID_API_LEVEL"
getprop ro.build.version.sdk
```

### 3. 编译工具链

```bash
pkg install -y clang rust make pkg-config libffi openssl
```

### 4. Node 和 ripgrep

```bash
pkg install -y nodejs ripgrep
```

### 5. pip 安装

```bash
source venv/bin/activate
export ANDROID_API_LEVEL="$(getprop ro.build.version.sdk)"
python -m pip install --upgrade pip setuptools wheel
python -m pip install -e '.[termux]' -c constraints-termux.txt
```

### 6. Hermes 自检

```bash
hermes version
hermes doctor
```

如果仍然失败，保留完整错误输出，不要只截取最后一行。Android native package 的失败原因通常位于前面的编译日志中。

## 十二、与 Claude Code、Codex 共存

Hermes 可以和其他终端 AI 编程代理同时存在：

```bash
claude --version
codex --version
hermes version
```

它们的定位并不完全相同：

| 工具 | 主要定位 |
| --- | --- |
| Claude Code | Claude 生态的终端编码 Agent |
| Codex CLI | OpenAI 的终端编码 Agent |
| Hermes Agent | 更强调 Agent 工具、记忆、技能、MCP 和多 Provider 的通用 Agent |

在 Termux 手机环境中，可以根据任务选择不同工具，而不需要删除其他 CLI。

## 十三、推荐的最终安装流程

如果是全新 Termux，建议按以下顺序：

```bash
pkg update && pkg upgrade -y
pkg install -y git python clang rust make pkg-config libffi openssl nodejs ripgrep ffmpeg
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
source ~/.bashrc
hermes version
hermes doctor
hermes model
hermes
```

如果一键安装出现 Android 依赖问题，再切换到官方手动路径，而不是盲目安装桌面版 `.[all]`。

## 十四、结语

Hermes 是目前三款工具中对 Termux/Android 文档支持最明确的一款：官方已经提供专门的 Termux 安装器和手动安装说明，但 Android 仍然属于 Tier 2。

因此最稳妥的原则是：**使用官方 Termux installer；失败时使用 `.[termux]`；设置 `ANDROID_API_LEVEL`；最后用 `hermes doctor` 验证。**

这样可以避免把桌面 Linux 的依赖方案直接搬到 Android，尤其可以绕开目前 Android 上不兼容的 voice、Docker 和部分浏览器依赖。
