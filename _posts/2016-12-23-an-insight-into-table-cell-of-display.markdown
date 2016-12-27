---
layout: post
title: 深入理解table-cell
date: 2016.12.23 22:10.000000000 +09:00
category: CSS
description: 
tags: table-cell;display;css;前端;
---

## 大妈式唠叨 

还有一周2016就要过去了，时间过得太快，压抑的一年，好像什么都没做，闭目冥思，反思自我得失...我勒个去，文采不好，再唠叨真成大妈了。
这一年听到最多的声音就是房价涨了，房价涨了，房价上蹿上蹿，一路飙升，疯了，全国都疯了，我也疯了，房租涨了，工资没涨，不好意思，我又禁不住
啰嗦了两句，还是聊技术吧，只有技术最可靠。

最近看了几篇关于table-cell的用法，觉得[旭神](http://www.zhangxinxu.com/)总结的还不错，于是乎我就抄袭，不对，是模仿，也不合适，
其实是微微扩展了以下，增加几点需要注意的地方。

## 支持度

从下图可以看出，所有浏览器都支持的很好嘛。

![caniuse]({{site.imageurl}}/assets/images/2016/table-cell-caniuse.png)

## 特性(Peculiarities)

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
    <span style="display:inline-block; line-height:1; vertical-align:middle">
      vertical align vertical align vertical align vertical align vertical align vertical align vertical align 
    </span>
  </div>
```

### 两列布局

```
<div style="clear">
    <div style="float: left; width: 100px; height: 100px; background: red;">
      cell one
    </div>
    <div style="display: table-cell; width: 100000px; background: blue;">
      cell two
    </div>
  </div>
```

![image]({{site.imageurl}}/assets/images/2016/table-cell-two-cols.png)

如果`左边的cell子元素只有img`，有两点需要提醒一下：

**第一点、** 如果未设置cell的max-width：

```
 <div style="margin-top: 30px;">
    <div style="display: table-cell;height:150px;width:150px;background:red;">
      <img src="{{site.imageurl}}/assets/images/samples/cat1.jpg" width="200px">
    </div>
    <div style="display: table-cell;height:150px;width:150px;background:blue;">
      blue box
    </div>
  </div>
```

![image]({{site.imageurl}}/assets/images/2016/table-cell-two-cols-1.1.png)

先来看看对于inline-block容器也存在类似情况：

```
<div>x
   <div style="display:inline-block; height:150px; width:150px; background:red;"></div>
   <div style="display:inline-block; height:150px; width:150px; background:blue;">
     blue box
   </div>
</div>
```

![image]({{site.imageurl}}/assets/images/2016/table-cell-two-cols-1.2.png)

这两种情况涉及到inline-block元素的vertical-align的默认值，其默认baseline，
那么baseline是什么？有三种情况：

- 如果inline-block元素有流内内容，那么它的baseline就是最后一行内容的baseline；
- 如果inline-block元素有流内内容，但是它的**overflow**是非**visible**的，那么它的baseline就是magin-box的底部；
- 如果inline-block元素没有流内内容，那它的baseline是margin-box的底边界；

看到这里应该就明白了为什么有这种垂直落差了吧，如果不明白，留言吧。

为了解决这种问题，可以设置table-cell（或inline-box的vertical-align为top）。

**第二点、**如果cell的max-width为100%，对比看看：

```
// img的max-width不为100%，并且第二个table-cell设置宽度很大比如100%或者9999999px
<div style="margin-top: 30px;">
    <div style="display: table-cell;height:150px;width:150px;background:red;">
      <img src="{{site.imageurl}}/assets/images/samples/cat1.jpg" width="200px">
    </div>
    <div style="display:table-cell; height:150px; width:150px; background:blue;">
      blue box
    </div>
  </div>
```

![image]({{site.imageurl}}/assets/images/2016/table-cell-two-cols-1.1.png)

```
// img的max-width为100%，并且第二个table-cell设置宽度很大比如100%或者9999999px
<div>
  <div style="display: table-cell;background:red;">
    <img style="max-width:100%" src="{{site.imageurl}}/assets/images/samples/cat1.jpg" width="100px">
  </div>
  <div style="display: table-cell;height:150px;width:100%;background:blue;">
    blue box
  </div>
</div>
```

![image]({{site.imageurl}}/assets/images/2016/table-cell-two-cols-2.2.png)

聪明的你一眼就看到第二种情况第一个table-cell没了宽度，其主要原因是**max-width**的优先级**高于**width，所以此时的width无效了，所以img就被右侧的cell
挤压没了，顺便说一句，**min-width**的优先级比**max-width**高。


### 两列等高布局

其实就是利用了特性2：

```
<div>
    <div style="display:table-cell;background:green;">
      hello 
    </div>
    <div style="display:table-cell;background:red;width: 100%">
      <div>
        abc def jkkl kada eix lllll ew a ee www qq  ccccc zzzdfa fdadfjaklew ieiie
      </div>
    </div>
  </div>
```

## 参考

1.[我所知道的几种display:table-cell的应用](http://www.zhangxinxu.com/wordpress/2010/10/%E6%88%91%E6%89%80%E7%9F%A5%E9%81%93%E7%9A%84%E5%87%A0%E7%A7%8Ddisplaytable-cell%E7%9A%84%E5%BA%94%E7%94%A8/)<br/>
2.[max-width](https://developer.mozilla.org/en-US/docs/Web/CSS/max-width)：来自MDN的传说<br/>
3.[Block formatting contexts](https://www.w3.org/TR/CSS21/visuren.html#block-formatting)<br/>
4.[the 'line-height' and 'vertical-align' properties](https://www.w3.org/TR/2011/REC-CSS2-20110607/visudet.html#line-height)
5.[CSS深入理解vertical-align和line-height的基友关系](http://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/)
6.[Vertical-Align: All You Need To Know](http://christopheraue.net/2014/03/05/vertical-align/)
