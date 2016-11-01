---
layout: post
title: 正则表达式`?:，?=，?!`
date: 2016.11.01 10:12.000000000 +09:00
category: 正则表达式
description:  本文详细解读`?:，?=，?!`的使用方法
tags: 正则表达式;regex;javascript
---

## 捕获 VS 非捕获

## 贪婪 VS 非贪婪

## 零宽度

## 前瞻 VS 后顾

## ?: ?= ?!

### (?:x)

匹配'x'但是不记住这个匹配。这里是括号是不捕获的，并且可以让你定义用于正则表达式操作符使用的子表达式。
比如/(?:foo){1,2}/，如果表达式是/foo{1,2}/，那{1,2}将只会应用于在'foo'中的最后字符'o'。而使用非捕获括号，
{1,2}就会应用于整个'foo'.

### x(?=y)

只有'x'的后面是'y'时，才会匹配'x'，这叫做（前瞻）lookahead。
比如，/Jack(?=Sprat)/，只有'Jack'的后面是'Sprat'时，才会匹配'Jack'。但是'Sprat'并不包含在匹配结果中。

### x(?!y)

只有'x'的后面不是'y'时，才会匹配'x'，这个叫做否定前瞻（negated lookahead）。
比如，/\d+(?!\.)/，匹配一个后面不是点的数字。/\d+(?!\.)/.exec('3.141')匹配'141'，而不是'3.141'。

## 参考

1.[使用正则表达式找出不包含特定字符串的条目](http://www.imkevinyang.com/2009/08/%E4%BD%BF%E7%94%A8%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%E6%89%BE%E5%87%BA%E4%B8%8D%E5%8C%85%E5%90%AB%E7%89%B9%E5%AE%9A%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E6%9D%A1%E7%9B%AE.html) <br/>
2.[Regular Expressions](https://developer.mozilla.org/en/docs/Web/JavaScript/Guide/Regular_Expressions)

