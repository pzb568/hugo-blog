---
title: "搭建Hugo站使用netlify动部署网站"
date: 2022-09-29T14:17:53Z
draft: false
tags: ["软件应用"]
categories: ["documentation"]
lightgallery: true

math:
  enable: true
---

![](https://raw.githubusercontent.com/gohugoio/gohugoioTheme/master/static/images/hugo-logo-wide.svg?sanitize=true)

<!--more-->


## 新见一个网站目录
```
hugo new site blog
```



## 安装主题


```
git clone https://github.com/dillonzq/LoveIt.git themes/LoveIt
```


## 初始化子模块


```

git init
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt

```



## 忽略public

```
/public
```
## 复制编辑主题
```
cp themes/LoveIt/exampleSite/config.toml
```
## 创建你的第一篇文章
```
hugo new posts/first_post.md

```
## 本地预览网站
```
hugo serve
```
## 新建GitHub库


## 上传GitHub并
```
git add -A
git commit -m "网站搭建"
git push -u origin master
```




