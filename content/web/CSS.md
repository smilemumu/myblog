---
title: "HTML"
date: 2019-12-10
tags: [H5]
categories: [WEB]
draft: true
---

list-style-type: 默认点
none 无
square 方格

后代组合器
li em
相邻兄弟选择器
h1 + p 
根据状态确定样式
a:link 链接颜色
a:visited 访问后的颜色
a:hover{
	text-decoration: none; 覆盖后，链接去除下划线
}

body h1 + p .special {
  color: yellow;
  background-color: black;
  padding: 5px;
}
上面的代码为以下元素建立样式：在<body>之内，紧接在<h1>后面的<p>元素的内部，类名为special。

refer to : https://developer.mozilla.org/zh-CN/docs/Web/HTML