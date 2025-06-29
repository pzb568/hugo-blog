---
title: "{{ replace .Name "-" " " | title }}" # 文章标题
description: "" # 文章描述
date: {{ .Date }} # 创建日期
image: "" # 文章题图
math: false # 是否启用 MathJax
license: "" # 版权协议
hidden: false # 是否隐藏
comments: true # 是否开启评论
draft: true # 是否为草稿
---

<!-- 
这是一个非常全面的 Markdown 语法和 Hugo Shortcodes 示例。
您可以直接在此基础上修改，开始您的创作。
-->

# 一级标题 (H1) - 通常由文章标题自动生成

## 二级标题 (H2)

### 三级标题 (H3)

#### 四级标题 (H4)

##### 五级标题 (H5)

###### 六级标题 (H6)

---

## 文本格式

这是一个普通的段落，用于介绍基本的文本格式。

**这是加粗的文字**

*这是斜体的文字*

***这是加粗且斜体的文字***

~~这是带删除线的文字~~

`这是行内代码`

您也可以在文本中加入脚注[^1]。

[^1]: 这是脚注的具体内容。

---

## 引用块

> 这是一个引用块。当您想引用他人的话语或突出显示某段文字时，可以使用它。

> 嵌套引用块
>> 第二层嵌套
>>> 第三层嵌套

---

## 列表

### 无序列表

- 列表项 A
- 列表项 B
  - 嵌套列表项 B1
  - 嵌套列表项 B2
- 列表项 C

* 使用星号也可以创建列表
+ 或者使用加号

### 有序列表

1. 列表项 1
2. 列表项 2
3. 列表项 3
   1. 嵌套列表项 3.1
   2. 嵌套列表项 3.2

### 任务列表 (Checklist)

- [x] 已完成的任务
- [ ] 未完成的任务
- [ ] 待办事项

---

## 代码块

使用三个反引号 ` ``` ` 包围代码，并可以指定语言以获得语法高亮。

```bash
#!/bin/bash
# 这是一个 shell 脚本示例
echo "Hello, Hugo!"
```

```javascript
// 这是一个 JavaScript 示例
function greet(name) {
  console.log(`Hello, ${name}!`);
}

greet('World');
```

```plaintext
这是一个没有语法高亮的纯文本块。
```

---

## 链接与图片

### 链接

[这是一个指向外部网站的链接](https://gohugo.io/ "Hugo 官网")

[这是一个指向站内页面的相对链接](../about/)

### 图片

![图片替代文本](/images/avatar.png "图片标题")

---

## 表格

| 表头 1 | 表头 2 (居中) | 表头 3 (居右) |
| :--- | :---: | ---: |
| 单元格 1 | 单元格 2 | 单元格 3 |
| 单元格 4 | 单元格 5 | 单元格 6 |
| 短文本 | 这是一个内容较长的单元格 | ... |

---

## Hugo Shortcodes

Hugo ��用 Shortcodes 来扩展 Markdown 的功能。

### 引用 (quote)

{{</* quote */>}}
在这里写下您想引用的名言。
{{</* /quote */>}}

{{</* quote author="作者" link="https://example.com" */>}}
在这里写下您想引用的名言，并附上作者和来源链接。
{{</* /quote */>}}

### Bilibili 视频

{{</* bilibili BV1xx411c7mD */>}}

### YouTube 视频

{{</* youtube w7Ft2ymGmfc */>}}

### 图片处理 (figure)

{{</* figure src="/images/avatar.png" title="这是一个带标题的图片" caption="这是图片的说明文字，可以使用 *Markdown* 语法。" alt="替代文本" class="center" */>}}

---

## HTML 标签

您也可以在 Markdown 中直接使用 HTML 标签。

<details>
  <summary>点击展开查看更多内容</summary>
  <p>这里是隐藏的详细信息，对于折叠长代码块或补充说明非常有用。</p>
</details>