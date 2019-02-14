---
title: react-native随笔
top: 100
copyright: true
date: 2019-02-14 11:17:42
tags:
- react-native
categories:
- react-native
---

# 前言
做了react-native也有两年了，平时遇到的注意点和坑，只是解决了，但是并没有记录，现在回想我都不记得我遇到哪些坑了，一脸懵逼当中23333.。。，
还是动动手记录下，免得明年也懵逼😆

## 注意点、坑点、不常用不易记的

+ 1、flex和flexGrow的区别：正常情况下，flex:1，和flexGrow: 1,都会在父元素下占满各自的空间，flex: 1时，一个view想设置height，同时高于
父元素的空间，这样高度仍然是占满父元素并不会撑开。flexGrow: 1时，设置了height，不会受父元素空间限制。
解释：flex只有一个值，且值为整数时，等价于 flex：X, 1, 0  ！！这才是最终答案，是根本原因。
flex这个属性是flexGrow、flexShrink和flexBasis三个属性的缩写。
详细布局片可以看这篇文章[http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html]
