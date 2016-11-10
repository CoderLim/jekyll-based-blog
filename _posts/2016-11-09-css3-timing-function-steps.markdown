---
layout: post
title: animation-timing-function 之 steps()
date: 2016.11.09 20:30.000000000 +09:00
category: CSS
description: 这篇博客是面向于对css动画有一定了解的童鞋，主要是区分step-start和step-end.
tags: 前端;CSS;step-start;step-end;
---

## 随便聊聊

不知道最近三星怎么这么喜欢炸弹，不仅手机能爆炸，洗衣机也会爆炸，尼玛，产品设计师难不成是研究炸弹的；NBA开赛了，可惜湖人没了科比的身影，
在我还没成为球迷之前他已经不在了，很湿伤心，还是听听音乐吧。说到听歌，给大家推荐一首神曲《我的滑板鞋》，看到这歌名，我想你们内心是崩溃
的，听到`原唱`你们肯定骂了不止一百遍逗比碧池，心细的同学可能注意到我提到了原唱，没错原唱是不堪入耳，但是经过华晨宇的改编已经脱胎换骨了，
“一步两步，一步两步”，steps, step-start, step-end。

## 示例

CSS:

```
    .cube {
      height: 150px;
      width: 200px;
    }
    .start {
      border: 20px solid red;
      animation: anima 4s infinite step-start;
    }
    .end {
      border: 20px solid green;
      animation: anima 4s infinite step-end;
    }

    @keyframes anima {
      25% {
        border-top-color: transparent;
      }
      50% {
        border-right-color: transparent;
      }
      75% {
        border-bottom-color: transparent;
      }

```

本来想把剂夫图发上来，但是录制的时间点总是不对，防止误导就不贴图了，移步[这里](https://jsfiddle.net/CoderGLM/7gz452ad/)。

## steps

steps(number_of_step, direction)定义了一个step函数,将输出分成等距的步。还是用我擅长的方言说吧，比如steps(2,start)(先忽略*start*)，
意思是把时间分成2等分，每等个时间段持续一种不变的状态，没有中间的过渡，可以从下面两个图看出来。

- *number_of_step*：正数，代表组成stepping函数的等距步的多少，即步数；
- *direction*：表示stepping函数是[左持续还是右持续][1]；
  - *start*：表示左持续函数，因此当动画开始时，第一步就会发生，什么意思呢？也就是动画刚开始的一刹那，第一步就发生了，第一步刚发生就结束了，开始了吗？已经结束了，昙花一现；
  - *end*：表示右持续，因此当动画结束时，最后一步就会发生，这又TTMM什么意思呢？意思是动画结束的一刹那是最后一步的开始，也是最后一步的结束，

什么是左持续和右持续呢？请看下面坐标里的大头针，针尖方向的左就是左持续，相反就是右持续，^~^!

steps\(2,start\):
![steps(2,start)]({{ site.imageurl }}/assets/images/2016/steps(2,start).png)

steps\(4,end\):
![steps(4,end)]({{ site.imageurl }}/assets/images/2016/steps(4,end).png)



step-start和step-end是两种不同状态，从大头针图来看，step-start是刚开始就病入膏肓了，没针只有头，接下来病好了，又能伸出来了^_^;
step-end是刚开始身体杠杠的，到最后不行了，为什么不行了，什么不行了？我也不知道。

## step-start

![steps-start]({{ site.imageurl }}/assets/images/2016/step-start.png)

这个关键字与steps(1, start)是一样的。使用这个时间函数，动画立即跳到最终的状态，呆在那个位置直到动画结束。阿西吧，什么鬼，没关系
等我把另一个关键词拿出来后对比一下就清楚了。

## step-end

![step-end]({{ site.imageurl }}/assets/images/2016/step-end.png)

这个与steps(1,end)等同。在用这个函数时，动画呆在初始状态直到结束。阿西吧吧，什么意思？似懂非懂，
大头针图来看，step-start和step-end都是只有一根针，凸^_^凸，卖个萌＝_＝!


接下来用我的姿势来告诉你这两个是怎么表现的吧，还是拿最上面的示例来说话。

来看看这段4s动画的实现：

```
    @keyframes anima {
      25% {
        border-top-color: transparent;
      }
      50% {
        border-right-color: transparent;
      }
      75% {
        border-bottom-color: transparent;
      }
    }

```

虽然step-XXX都是一步到位（numbers_of_step＝1），也就是只有一个大头针，但是有四帧动画，
实际上主要还是用到了方向这个参数。

从数学归纳法的角度来看看规律：

step-start：

1. 1s，动画的最终状态是25%，50%，75%样式叠加，所以只显示左边框；
2. 2s是50%，75%样式的叠加，所以只隐藏了右和下边框；
3. 3s是只有75%的样式，所以只隐藏了下边框；
4. 4s是这几种样式都不存在的状态，所以四边都显示了。

step-end：也许这种更符合我们的直觉

1. 1s，动画的最初状态是四边都显示；
2. 2s，只有25%的样式，也就是只隐藏顶部；
3. 3s是25%和50%样式的叠加，所以隐藏了顶部和右边；
4. 4s是25%，50%，75%样式的叠加，所以只有左边显示。

请从方向的角度来理解上面的规律。

## 结语

balabala最重要的就是最后的对比啊，对比才能更好的理解，不然根据文档实在是有点头疼，如果哪位大虾有更好的理解角度，欢迎留言。

## 参考

1.[single-transition-timing-function](https://developer.mozilla.org/en-US/docs/Web/CSS/single-transition-timing-function)<br/>




[1]: http://en.wikipedia.org/wiki/Left-continuous#Directional_and_semi-continuity "left- or right-continuous"
