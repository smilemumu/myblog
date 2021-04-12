---
title: "HTML"
date: 2019-12-10
tags: [H5]
categories: [WEB]
draft: true
---
disabled 布尔熟悉，可以使输入框变得不可选（变灰色）
例如：
```html
<!-- 使用disabled属性来防止终端用户输入文本到输入框中 -->
<input type="text" disabled>
```
粗体
<strong></strong> 有语义
<b></b>
斜体
<em></em> 有语义
<i></i>
下划线
<u></u> 无语义

当链接到同一网站的其他位置时，你应该使用相对链接（当链接到另一个网站时，你需要使用绝对链接）

<sub></sub> 下标
<sup></sup> 上标

有大量的HTML元素可以来标记计算机代码：

<code>: 用于标记计算机通用代码。
<pre>: 对保留的空格（通常是代码块）——如果您在文本中使用缩进或多余的空白，浏览器将忽略它，您将不会在呈现的页面上看到它。但是，如果您将文本包含在<pre></pre>标签中，那么空白将会以与你在文本编辑器中看到的相同的方式渲染出来。
<var>: 用于标记具体变量名。
<kbd>: 用于标记输入电脑的键盘（或其他类型）输入。
<samp>: 用于标记计算机程序的输出。


HTML 还支持将时间和日期标记为可供机器识别的格式的 <time> 元素。例如：
<time datetime="2016-01-20">2016年1月20日</time>

html5包括<header>，<nav>，<article>，<section>，<aside>，和<footer>元素。


div中的img和p可替换成 figure中的img和figcaption
html5 中的<figure> 和 <figcaption> 元素

总而言之，如果图像对您的内容里有意义，则应使用HTML图像。 如果图像纯粹是装饰，则应使用CSS背景图片

可点击图片创建
```html
<a href="https://developer.mozilla.org/en-US/">
  <img src="https://mdn.mozillademos.org/files/6851/mdn_logo.png" 
       alt="MDN logo" />
</a>
```

refer to : https://developer.mozilla.org/zh-CN/docs/Web/HTML