---
layout: post
title: 正则表达式 ?:，?=，?!
date: 2016.11.01 10:12.000000000 +09:00
category: 正则表达式
description: 本文详细解读`?:，?=，?!`的使用方法
tags: 正则表达式;regex;javascript
---

## 分组 非捕获分组

在正则表达式中遇到小括号就可以认为是一个分组，比如/(a)bcda\1/，(a)就是一个分组，\1代表第一个分组。这种分组可以成为
捕获分组。

```
var reg = /(a)bcd\1/
console.log(reg.test('abcdef')) // false
console.log(reg.test('abcdaef')) // true
```

与其相对的就是非捕获分组，比如/(?:a)bcda/，(?:a)就是非捕获分组，不能通过\1引用。

```
var reg = /(?:a)bcd\1/
console.log(reg.test('abcdaef')) // false
```

## 贪婪 非贪婪

贪婪的意思是尽可能多的匹配，比如:

```
var str = 'aaaaaabc'
console.log(/a+/.exec(str)) // 'aaaaaa'
console.log(/a+?/.exec(str)) // 'a'
```
非贪婪的意思就是尽可能少的匹配，如上，其语法的表示就是在+或*后满加一个?。

## 零宽度断言 前瞻 后顾

零宽度断言就是子表达式只是预测而不匹配，不会影响到[lastIndex][1]，如下的reg2中的(?=xxxx)就是零宽度断言。

```
var str = 'abcdef'
var reg1 = /ab(?:cdef)/g
reg1.exec(str)
console.log(reg1.lastIndex) // 6

var reg2 = /ab(?=cdef)/g
reg2.exec(str)
console.log(reg2.lastIndex) // 2
```

### 前瞻 后顾

前瞻（lookahead）和后顾（lookbehind），总起来叫lookaround，它们就是零宽度断言，就像匹配行首和行尾，匹配单词边界，
不同之处是，lookaround实际上是匹配字符的，但是最终会放弃匹配，只返回是否匹配。这就是为什么它们只叫做断言。
它们并不消耗字符，只断言一个匹配是否可能。

前瞻包括：x(?=y) 和 x(?!y)
后顾包括：(?<x)y 和 (?<!)y

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
2.[Regular Expressions](https://developer.mozilla.org/en/docs/Web/JavaScript/Guide/Regular_Expressions)<br/>
3.[Zero-Length Assertions](http://www.regular-expressions.info/lookaround.html)

[1] https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex "正则表达式之lastIndex"

