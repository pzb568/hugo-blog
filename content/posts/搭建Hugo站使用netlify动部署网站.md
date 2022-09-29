---
title: "搭建Hugo站使用netlify动部署网站"
date: 2022-09-29T14:17:53Z
draft: false
---

## 新见一个网站目录
'''
hugo new site blog

'''
## 安装主题

git clone https://github.com/dillonzq/LoveIt.git themes/LoveIt

## 初始化子模sm
git init
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt


## 忽略public


