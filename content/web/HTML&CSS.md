---
title: "HTML&CSS"
date: 2019-12-05
tags: [H5]
categories: [WEB]
draft: true
---

# 第一课
## 常见HTML术语：
- 元素(elements)：
```css
<h1>-<h6>、<p>、<a>，<div>，<span>，<strong>，和<em>元素，等等。
```
```css
自封闭元素
<br><embed><hr><img><input><link><meta><param><source><wbr>
```
- 标签(tags)：  
```css
<a>...</a>
```
- 属性(attribute)：  
```css
<a href="http://shayhowe.com/">Shay Howe</a>
```
![元素标签属性](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/web/html-1.png)


## 典型的HTML文档结构  

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Hello World</title>
  </head>
  <body>
    <h1>Hello World</h1>
    <p>This is a web page.</p>
  </body>
</html>
``` 

## CSS术语

选择器
   
```java
在CSS中，选择器后跟花括号，{}其中包含要应用于所选元素的样式。这里的选择器以所有<p>元素为目标
```

属性 
```css
p {
  color: ...;
  font-size: ...;
}
```
值
```css
p {
  color: orange;
  font-size: 16px;
}

```
![元素标签属性](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/web/html-2.png)

## 选择器分类

- 类型选择器  
```css
div { ... }
```
- 类选择器  
```css
.awesome { ... }
html中的用法：
<div class="awesome">...</div>
```
- ID选择器  
```css
#shayhowe { ... }
html中的用法：
<div id="shayhowe">...</div>
```
- 复杂选择器  
https://learn.shayhowe.com/advanced-html-css/complex-selectors/

## 引用css
```html
<head>
  <link rel="stylesheet" href="main.css">
</head>
```

## 重置css
css重置样式主要是为了让各个浏览器的CSS样式有一个统一的基准，使HTML元素样式在跨浏览器时有一致性的效果。
推荐normalize.css：https://necolas.github.io/normalize.css/


# 第二课
## 块级和内联元素
- 块级元素从新行开始，一个堆叠在另一个之上，并占据任何可用宽度。块级元素可以相互嵌套，并且可以包装内联级元素。我们通常会看到块级元素用于较大的内容，例如段落。
- 内联级元素不以新行开头。它们属于文档的正常流程，一个接一个地排列，仅保持其内容的宽度。内联级别的元素可以相互嵌套。但是，它们不能包装块级元素。通常，我们会看到内联级元素具有较小的内容，例如几句话。

## 注释
HTML注释以开头<!-- and end with -->。CSS注释以/*和开头*/。

## 加粗斜体
加粗
<strong></strong>
<b></b>
斜体
<em></em>
<i></i>

## 结构元素
```html
包括<header>，<nav>，<article>，<section>，<aside>，和<footer>元素。
```
![建筑结构](https://raw.githubusercontent.com/smilemumu/picture/master/myblog/web/html-3.png)
header: 标题
nav: 导航
article: 文章
section: 部分
aside: 旁边
footer ：页脚


## 新窗口中打开链接  
使用target值为的属性_blank。该target属性确定链接显示的确切位置，该_blank值指定一个新窗口。
```html
<a href="http://shayhowe.com/" target="_blank">Shay Howe</a>
```

## 链接到同一页面的各个部分
```html
<body id="top">
  ...
  <a href="#top">Back to top</a>
  ...
</body>

```

# 第三课（了解CSS）