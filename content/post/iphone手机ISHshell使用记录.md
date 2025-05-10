---
title: "iphone手机ISHshell使用记录"
date: 2022-11-02T07:53:41Z
lastmod: 2022-11-02T07:53:41Z
draft: false
author: ""
authorLink: ""
description: ""
license: ""
images: []

tags: ["ISH shel"]
categories: ["documentation"]

featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
code:
  copy: true
  maxShownLines: 50
math:
  enable: false
  # ...
mapbox:
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
library:
  css:
    # someCSS = "some.css"
    # located in "assets/"
    # Or
    # someCSS = "https://cdn.example.com/some.css"
  js:
    # someJS = "some.js"
    # located in "assets/"
    # Or
    # someJS = "https://cdn.example.com/some.js"
seo:
  images: []
  # ...
---



![](https://ish.app/assets/github-readme.png)


&emsp;&emsp;iSH 是一个运行在 iOS 上的 Linux shell。使用了 x86 用户模式仿真和系统调用翻译转换。iSH使用的Linux系统镜像是[Alpine Linux](https://alpinelinux.org/),想了解详细信息，请访问链接地址。

<!--more-->

## 安装ISH shell

>软件直接在链接下载[app store](https://apps.apple.com/us/app/ish-shell/id1436902243)。


## 使用

>安装必要软件(ish包管理命令是[^apk][^apk]: 输入'apk'了解详细命令)


```
apk add vim git openssh python zsh 
```

### 更换源

>以下命令直接更换清华源


```
sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories
```

### 修改git限制和拉取失败问题
>因为git默认的 http.postBuffer大小为1M。所以clone较大的文件时偶尔会失败，上面的命令是将git的http.postBuffer大小设置为500M。

```
git config --global http.postBuffer 524288000
```
>git拉取失败问题

![](https://user-images.githubusercontent.com/44852729/110248063-1ef73600-7f24-11eb-9e2c-1f090bbe8f02.jpeg)

```
git config —global pack.threads “1”
```



