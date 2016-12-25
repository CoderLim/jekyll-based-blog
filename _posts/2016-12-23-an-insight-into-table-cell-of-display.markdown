---
layout: post
title: 深入理解table-cell
date: 2016.12.23 22:10.000000000 +09:00
category: CSS
description: 
tags: table-cell;display;css;前端;
---

## 发言

最近看了几篇关于table-cell的用法，觉得[旭神](http://www.zhangxinxu.com/)总结的还不错，于是乎我就抄袭，不对，是模仿，也不合适，
其实是微微扩展了以下，增加几点需要注意的地方。

## 特性

table-cell的特征就是跟table的td表现是一毛一样的；

1. 对marigin无感：设置margin没有反应，但是可以设置宽高和padding；
2. 并列cell等高：同容器下的并列cell的高度保持一致；
3. 宽不会超过父容器：宽高设置成99999999px也不会超过父元素的宽度；
4. 会生成BFC：以下的几点都会生成BFC
    1. position为absolute或fixed；
    2. display为table-cell, table-caption或者inline-block；
    3. float不为none；
    4. overflow不为visible；
5. 天然垂直居中：注意要设置cell的vertical-align为middle，这点很奇怪，因为cell是父元素，对比下面两个实例，感受一下：

```
// demo1
<table-cell style="vertical-align: middle;">
    <span></span>
</table-cell>

// demo2 
<div>
    <inline style="vertical-align: middle;"></inline>
</div>
```

## 用途

### 垂直居中

```
<div style="display: table-cell; vertical-align: middle; width: 200px; height: 150px; border: 1px solid red;">
    vertical align
  </div>
```

顺便提一下inline-height的垂直居中，当多行文本直居中时，其子元素应为inline-block，比如：

```
<div style="line-height:150px; width:200px; border:1px solid red;">
    <span style="display:inline-block; line-height:1; vertical-align:middle">vertical align vertical align vertical align vertical align vertical align vertical align vertical align </span>
  </div>
```

### 两列布局

```
<div style="clear">
    <div style="float: left; width: 100px; height: 100px; background: red;">cell one</div>
    <div style="display: table-cell; width: 100000px; background: blue;">cell two</div>
  </div>
```

1. 如果左侧为空元素；
2. 如果左侧子元素为img，并且max-width为100%；


### 两列等高布局

其实就是利用了特性2：

```
<div>
    <div style="display:table-cell;background:green;">
      hello 
    </div>
    <div style="display:table-cell;background:red;width: 100%">
      <div>abc def jkkl kada eix lllll ew a ee www qq  ccccc zzzdfa fdadfjaklew ieiie</div>
    </div>
  </div>
```

## 参考

1.[我所知道的几种display:table-cell的应用](http://www.zhangxinxu.com/wordpress/2010/10/%E6%88%91%E6%89%80%E7%9F%A5%E9%81%93%E7%9A%84%E5%87%A0%E7%A7%8Ddisplaytable-cell%E7%9A%84%E5%BA%94%E7%94%A8/)<br/>
2.[max-width](https://developer.mozilla.org/en-US/docs/Web/CSS/max-width)：来自MDN的传说<br/>
3.[Block formatting contexts](https://www.w3.org/TR/CSS21/visuren.html#block-formatting)<br/>
