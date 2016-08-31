---
layout: post
title: 几个有趣的javascript函数
date: 2015.06.05 14:02.000000000 +09:00
category: 前端相关
description: 小函数蕴含着大学问：函数节流之水龙头与弹簧、尾递归优化
tags: 前端;js;javascript;throttle;bounce;tco;
---

> 整理几个有趣的js函数，后续还会增加。

## 函数节流：水龙头与弹簧

**什么是节流，请自行百谷**

- Throttle：这种方式是类似水龙头，当水滴足够大时水滴就会掉下来；类似的，当时间超过某个timeout的时候就执行函数

```
var throttle = function(fn, timeout) {
      var last = new Date().getTimes();

      return function() {
        var curr = new Date().getTimes();
        if (curr - last >= timeout) {
          fn.apply(this, arguments);
        }
      };
};
```

忽然想到一个问题，如果需要节流的是scroll事件，如果滚动的时间超短上面函数岂不是不能执行了，那就这样实现：

```
var throttle = function(fn, timeout, delay) {
    var timer = null,
    last = new Date().getTimes();

    return function() {
      var curr = new Date().getTimes(),
          context = this,
          args = arguments;

      if (curr - last >= timeout) {
        clearTimeout(timer);
        timer = null;
        last = curr;
        fn.apply(context, args);
      } else {
        timer = setTimeout(function() {
                  fn.apply(this, arguments);
                }, delay);
      }
    };
};
// 上面这一串代码居然需要我手动缩紧，也是醉了
```

- Debounce：bounce是反弹的意思，debouce就是不让弹，就像弹簧一样，你一直按着它，直到放手它才能弹起来；这种思想拿到节流来说就是：如果你一直滚动，那函数就一直不响应，直到你不滚动我才执行：

```
var debounce = function(fn, delay) {
  var timer = null;
  return function() {
    var context = this,
        args = arguments;

    clearTimeout(timer);
    timer = setTimeout(function() {
      fn.apply(context, args);
    }, delay);
  };
};
```

## 尾递归优化

**不知道尾递归？**参考[中文版](http://es6.ruanyifeng.com/#docs/function)，什么？看不懂中文？那就来这里[英文版](http://www.integralist.co.uk/posts/js-recursion.html)

- Trampoline：蹦床函数，为何叫蹦床？没明白。这个函数的作用是把递归通过循环实现。

```
function trampoline(fn) {
    while (f && f instanceof Function) {
      f = f();
    }
    return f;
  }
  function sum(x, y){
    if (y > 0) {
      return sum.bind(null, x+1, y-1);
    } else {
      return x;
    }
}
trampoline(sum(1,100000));
```

- Tco：不用修改原函数的尾递归优化，我喜欢这个函数，因为它不太好理解，看不懂的朋友请参考上面的链接。

```
function tco(fn) {
      var value,
        active = false,
        accumulated = [];

    return function accumulator() {
      accumulated.push(arguments);
      // console.log(active);
      if (!active) {
        active = true;
        while (accumulated.length) {
          value = fn.apply(this, accumulated.shift());
        }
        console.log(value);
        active = false;
        return value;
      }
    };
}
var sum = tco(function(x, y) {
    if (y > 0) {
      return sum(x+1, y-1);
    } else {
      return x;
    }
});
console.log(sum(1, 10000));
```


## 参考

> [JS魔法堂：函数节流（throttle）与函数去抖（debounce）](http://www.cnblogs.com/fsjohnhuang/p/4147810.html)：这里貌似比我说的更形象
> [Understanding recursion in functional JavaScript programming](http://www.integralist.co.uk/posts/js-recursion.html)：简单看了看代码，主要内容还是看的中文版的（在下面）;
> [函数的扩展](http://es6.ruanyifeng.com/#docs/function)：这是es6教程中的其中一课，往下拉往下拉然后就看到“尾递归”了， 如果嫌麻烦，那就点上面的锚点.
