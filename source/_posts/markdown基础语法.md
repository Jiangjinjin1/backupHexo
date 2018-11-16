---
title: markdown基础语法
date: 2018-11-16 13:59:03
tags:
top: 100
copyright : true
---

# 1. markdown是什么？
>Markdown是一种轻量级标记语言，它以纯文本形式(易读、易写、易更改)编写文档，并最终以HTML格式发布。
>Markdown也可以理解为将以MARKDOWN语言编写的语言转换成HTML内容的工具，最初是一个perl脚本Markdown.pl。

<!--more-->

# 2. markdown语法
## 2.1 标题
标题有两种形式：
(1) 第一种是用=或者-表示一级或者二级标题

> 一级标题
> `======`
> 二级标题
> `------`

效果如下：

> # 一级标题down
> ## 二级标题

(2) 第二种就是#来表示1-6级标题，与HTML的h1-h6相似。

```
> #一级标题
> ##二级标题
> ###三级标题
> ####四级标题
> #####五级标题
> ######六级标题
```

效果如下：

# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题

## 2.2 引用
在段落的每行或者只在第一行使用符号>,还可使用多个嵌套引用，如：

> \>引用
  \>\>嵌套引用

效果如下：

> 引用

>> 嵌套引用

## 2.3 代码块
代码块可以在每行加上四个空格来表示代码区域，效果如：

>    function foo(){
>          return;
>    }

或者在开始与结尾用表示一样可以，效果同上：

```javascript
> function foo(){
> 		return;
> }
```

## 2.4 \`\`符号
\`\`可以用来表示相对较小区域的代码内容，或者起到标记作用，如

> \`标记\`

效果如下：

> `标记`

## 2.5 强调与斜体
在强调内容两侧分别加上*或者_，如：

> \*斜体，\_斜体\_
> \*\*粗体\*\*，\_\_粗体\_\_

效果如下：

> *斜体*，_斜体_
> **粗体**，__粗体__

## 2.6 有序列表与无序列表
使用\*、\+、或\-标记无序列表，如：

> (+-) 第一项 (+-) 第二项
*(+-) 第三项

效果如下：

> * 第一项
* 第二项
* 第三项

使用数字123加上.即是有序列表，如：

> 1. 第一项
2. 第二项
3. 第三项

效果如下：

> 1.第一项
2.第二项
3.第三项

## 2.7 分割线

分割线最常使用就是三个或以上\*，还可以使用\-和\_，如：

> 我是分割线1
\***
我是分割线2
———
我是分割线3
\___

效果如下：

我是分割线1
***
我是分割线2
-----
我是分割线3
___

## 2.8 链接
链接由[]与()组成，[]中的指描述，()中跟链接地址，如：

> \[百度\](https://www.baidu.com/)

效果如下：

> [百度](https://www.baidu.com/)

## 2.9 图片引入

> \!\[图片\](图片地址)

效果如下

> ![图片](http://thirdwx.qlogo.cn/mmopen/vi_32/6h85RMsib6oGMKz0eibALsD1ricmicj6gUbUWAOs0C6Ynhf7Na6IBLyoWpHxTnHAZoAdznwicTf1Xaa61xdnEjV1QTg/132)


2.9 表格
写法

```
First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell
```

在 Markdown 中，可以制作表格，例如：

First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell


