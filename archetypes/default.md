---
title: "{{ replace .Name "-" " " | title }}"  # 标题，文件名中的“-”替换为空格，并将首字母大写
description:                              # 页面描述
date: {{ .Date }}                        # 页面创建日期
image:                                   # 页面关联的图片路径，默认为空，需手动指定
math: #true/false
license:                                 # 页面内容的许可协议，默认为空，需手动指定
hidden: false #是否隐藏页面，false 表示页面可见
comments: true                           # 是否启用评论功能，true 表示允许评论
draft: true                              # 是否为草稿，true 表示页面为草稿状态，不会发布
---