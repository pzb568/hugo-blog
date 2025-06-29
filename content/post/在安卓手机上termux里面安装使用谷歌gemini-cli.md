---
title: "在安卓手机上Termux里面安装使用谷歌Gemini CLI"
date: 2025-06-29T12:00:00+08:00
author: "彭宅波"
tags: ["Termux", "Gemini", "CLI", "AI", "Android"]
categories: ["技术教程"]
description: "一篇详细的图文教程，指导你如何在安卓手机的Termux环境中安装、配置并使用谷歌官方的Gemini CLI工具，随时随地拥有强大的AI命令行助手。"
image: ""
math: false
license: ""
hidden: false
comments: true
draft: false
---

## 前言

谷歌的 Gemini 模型发布以来，其强大的能力令人印象深刻。除了常见的网页版，谷歌还为开发者提供了官方的命令行工具 (CLI)，让我们可以在终端里与 Gemini 进行交互。对于喜欢在手机上折腾的我们来说，如果能在 [Termux](https://termux.dev/en/) 这个强大的终端模拟器里用上 Gemini CLI，无疑是一件非常酷的事情。

本文就将详细记录如何在安卓手机的 Termux 环境中，从零开始安装、配置并使用 Gemini CLI。

## 准备工作

在开始之前，请确保你已经准备好以下几样东西：

1.  **一台安卓手机**：并且已经安装好了 Termux。
2.  **网络环境**：能够正常访问谷歌服务的网络。
3.  **一个谷歌账号**：用于获取 Gemini API 密钥。

## 获取 Gemini API 密钥

Gemini CLI 需要一个 API 密钥来进行身份验证。我们可以从 Google AI Studio 免费获取。

1.  打开浏览器，访问 [Google AI Studio](https://aistudio.google.com/)。
2.  使用你的谷歌账号登录。
3.  点击页面左侧的 “Get API key” 按钮。
4.  在弹出的窗口中，点击 “Create API key in new project”。
5.  系统会为你生成一串字符，这就是你的 API 密钥。**请务必复制并妥善保管好它**，因为这串密钥只会完整显示一次。

![获取API密钥](https://i.imgur.com/your-image-placeholder.png) (提示：你可以将实际截图上传后替换此链接)

## 安装步骤

准备好 API 密钥后，我们就可以开始在 Termux 中安装 Gemini CLI 了。

### 1. 更新 Termux 软件包

首先，打开 Termux，运行以下命令更新已安装的软件包，确保环境最新。

```bash
pkg update && pkg upgrade -y
```

### 2. 安装必要的依赖

Gemini CLI 是用 Go 语言编写的，所以我们需要安装 `golang` 来编译它。同时，我们还需要 `git` 来从 GitHub 克隆项目源码。

```bash
pkg install golang git -y
```

### 3. 克隆并编译 Gemini CLI 源码

��下来，我们从 Gemini 的官方 GitHub 仓库克隆源码。

```bash
git clone https://github.com/google/generative-ai-go
```

克隆完成后，进入 `generative-ai-go` 目录下的 `cmd/gemini` 文件夹，这里存放着 CLI 的源码。

```bash
cd generative-ai-go/cmd/gemini
```

然后，使用 `go build` 命令来编译源码。

```bash
go build .
```

编译过程可能需要几分钟，请耐心等待。编译成功后，当前目录下会生成一个名为 `gemini` 的可执行文件。

### 4. 将可执行文件移动到 `PATH`

为了能在任何路径下都方便地使用 `gemini` 命令，我们需要将这个文件移动到系统的 `PATH` 路径下。Termux 推荐的路径是 `$PREFIX/bin`。

```bash
mv gemini $PREFIX/bin/
```

现在，你可以通过运行以下命令来验证是否安装成功：

```bash
gemini -h
```

如果能看到一长串的帮助信息，说明 Gemini CLI 已经成功安装到你的 Termux 里了！

## 配置与使用

### 1. 配置 API 密钥

第一次使用时，我们需要设置之前获取的 API 密钥。运行以下命令：

```bash
gemini auth
```

程序会提示你输入 API 密钥。将你之前保存好的密钥粘贴进去，然后按回车。

```
Enter your API key: YOUR_API_KEY_HERE
```

设置成功后，密钥会保存在 `~/.gemini/credentials.json` 文件中，下次使用就无需再次配置了。

### 2. 开始与 Gemini 对话

现在，你可以正式开始使用 Gemini 了！最简单的用法就是直接提问。

```bash
gemini "你好，请用中文做个自我介绍"
```

Gemini 会很快返回它的回答。

你也可以不带任何参数运行 `gemini`，进入一个交互式的对话模式，可以连续进行多轮对话。

### 3. 结合管道 (Pipe) 使用

Gemini CLI 最强大的功能之一就是可以结合管道符 `|` 来处理其他命令的输出。例如，你可以让它帮你解释一个脚本文件。

假设你有一个名为 `backup.sh` 的脚本，你可以这样做：

```bash
cat backup.sh | gemini "请解释一下这个shell脚本是做什么的"
```

Gemini 会读取脚本内容并给出它的分析和解释，非常方便。

## 总结

通过以上步骤，我们成功地在安卓手机的 Termux 环境中部署了 Gemini CLI。现在，无论你身在何处，只要掏出手机，就能拥有一个强大的 AI 命令行助手。无论是查询资料、解释代码，还是激发灵感，它都能为你提供极大的便利。

希望这篇教程对你有帮助，快去探索 Gemini CLI 的更多强大功能吧！
