---
layout: post
title: 如何正确实现addEvent
date: 2015.07.06 15:32.000000000 +09:00
category: 前端相关
description: 如何正确实现addEvent？
tags: 前端;addEvent;
---

> 如何正确实现addEvent？

## 分析一下传统addEvent的实现：

```
// 传统写法大致上是这样
// 作者：Scott Andrew
function addEvent(obj, type, fn, useCapture) {
  if (obj.addEventListener) {
    obj.addEventListener(type, fn, useCapture);
    return true
  } else if (obj.attachEvent) {
    var r = obj.attachEvent("on"+type, fn);
    return r;
  } else {
    alert("事件处理器不能附加");
  }
}
```

### 优势：

1、没有限制的程序处理，可以添加任意多个；
2、避免ie的内存问题：有一个中央函数来来设置事件处理程序，这种方法允许你对所有的事件处理程序保存在数组或对象中，这样在unload方法中就可以移除事件处理器与dom元素的绑定。

### 当然也是有问题的：
1、addEventListener和attachEvent的区别：[猛戳这里-The this keyword](http://www.quirksmode.org/js/this.html)；
2、类似foldout菜单，每个li中有个a标签，当mouseover和mouseout的时候修改li的class。那我在handler中怎么拿到li呢？既然this在[The this keyword](http://www.quirksmode.org/js/this.html)提到是有问题的，那用event.target来获取li从而修改class呢？很可惜，li被a盖住了，所以target获取到的是a。



## Dean.Edwards是这样实现的：

```
// 作者：Dean.Edwards
function addEvent(element, type, handler) {
  // 为每个handler赋值一个唯一的ID
  if (!handler.$$guid) handler.$$guid = addEvent.guid++;
  // 为element创建一个事件类型的hash表
  if (!element.events) element.events = {};
  // 为每个element/event对儿创建一个事件handler的hash表
  var handlers = element.events[type];
  if (!handlers) {
    handlers = element.events[type] = {};
    // 保存已有的事件处理程序（如果有一个的话）
    if (element["on" + type]) {
      handlers[0] = element["on" + type];
    }
  }
  // 把handler保存在hash表
  handlers[handler.$$guid] = handler;
  // 赋值一个全局事件处理程序来做所有的工作
  element["on" + type] = handleEvent;
}; // 这里为何加分号

// 用来创建唯一ID的计数器
addEvent.guid = 1;

function removeEvent(element, type, handler) {
  // 从hash表删除事件处理程序
  if (element.events && element.events[type]) {
    delete element.events[type][handler.$$guid];
  }
}; // 这里为何加分号

function handleEvent(event) {
  // 拿到event对象（ie是全局event对象）
  event = event || window.event;
  // 得到事件处理程序的hash表的引用
  var handlers = this.events[event.type];
  // 执行每个时间处理器
  for (var i in handlers) {
    this.$$handleEvent = handler[i];
    this.$$handleEvent(event);
  }
}
```

这样的好处是什么呢？
- 1、没有对象检测；
- 2、没有使用addeventListener/attachEvent；
- 3、保证了正确的作用域（指this关键字）；
- 4、正确的传递了event对象；
- 5、完全的跨浏览器（ie4或ns4可能也能正常使用）；
- 6、 并且让我说的话，它是没有内存泄漏的。

## 参考
 
> [addEvent() – My Solution](http://dean.edwards.name/weblog/2005/10/add-event/)：js专家的addEvent是这么写的；
> [The this keyword](http://www.quirksmode.org/js/this.html)：this关键字在事件中的不同表现；
> [addEvent() considered harmful](http://www.quirksmode.org/blog/archives/2005/08/addevent_consid.html)：通用的addEvent居然有这么多问题。
