---
title: "Git版本撤销学习记录"
date: 2019-11-05T10:19:54Z
categories:
- 学习记录
tags:
- git
- github
keywords: #关键词
- git
#thumbnailImage: //example.com/image.jpg
---
&emsp;&emsp; 

# 撤销修改
```
checkout config.tom
```

# 版本跳转
```
checkout 578a9ca
```
# git回到master分支
```
checkout master
```
# 已经把错误修改推送到远程
## 撤销远程修改
>返回到最后一个直接的推送
```
reset --hard 391852b
```
## 修改然后强制push
```
push -f origin master
```







<!--more-->
