---
layout: post
title: 深入理解table-cell
date: 2016.12.23 22:10.000000000 +09:00
category: CSS
description: 
tags: table-cell;display;css;前端;
---

## 发言

## 特性

1. 对marigin无感；
2. 并列cell等高；
3. 天然垂直居中(但是要设置cell的vertical-align为middle，这点很奇怪，因为cell是容器)；
4. 宽高不会超过父容器；
5. 会生成BFC；

## 用途

### 垂直居中

inline-height

### 两列布局


### 两列等高布局

## 参考

1.[我所知道的几种display:table-cell的应用](http://www.zhangxinxu.com/wordpress/2010/10/%E6%88%91%E6%89%80%E7%9F%A5%E9%81%93%E7%9A%84%E5%87%A0%E7%A7%8Ddisplaytable-cell%E7%9A%84%E5%BA%94%E7%94%A8/)<br/>
2.[max-width](https://developer.mozilla.org/en-US/docs/Web/CSS/max-width)：来自MDN的传说<br/>
