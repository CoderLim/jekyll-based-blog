---
layout: post
title: 常见兼容性问题
date: 2015.03.02 13:10.000000000 +09:00
category: 前端相关
description: 总结常见的兼容性问题
tags: 前端;兼容;
---

> 总结常见的兼容性问题
> 内容不够丰富，等碰到之后还会添加

## CSS

- [[CSS hack大全&详解（什么是CSS hack）](http://www.kwstu.com/Admin/ViewArticle/201409011604277330)](http://www.kwstu.com/Admin/ViewArticle/201409011604277330)

```
// 区分所有主流浏览器的hack：
.element{
     color:#000;             /*w3c标准*/
     [;color:#f00;];         /*Webkit(chrome和safari)*/
     color:#666\9;           /*IE8*/
     *color:#999;            /*IE7*/
     _color:#333;            /*IE6*/
}
:root .element{color:#0f0\9;}  /*IE9*/
@media all and (-webkit-min-device-pixel-ratio:10000), not all and (
-webkit-min-device-pixel-ratio:0) { .element{color:#336699;}}  /*opera*/
@-moz-document url-prefix(){ .element{color:#f1f1f1;}} /*Firefox*/
```

- 怪异模式会出现不可预料的错误，所以在文档最上面使用<!DOCTYPE html>

- 浏览器默认的margin和padding不同，使用reset解决，但是不要这样设置：* {margin:0; padding:0 }，因为效率低；

- chrome 中文界面下默认会将小于12px的文本强制按12px显示，使用-webkit-text-size-adjust:none;已经不能解决问题了，应该这样设置

```
       .fontsize2 {
	    /*-webkit-text-size-adjust: none;*/
	    font-size: 9px;
	    -webkit-transform: scale(0.75);// 12 * 0.75 = 9
	}
```  

- 超链接访问后hover样式就不现实了，被点击访问后的超链接不再具有hover和active；按照L-V-H-A：a:link{} a:visited{} a:hover{} a:active{} 顺序设置可解决；

- 同一BFC相邻box的margin会折叠：详细信息请[猛戳这里](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)

- png24位的图片在iE6浏览器上出现背景，解决方案是做成PNG8.也可以引用一段脚本处理;

- IE6双边距：block+float+margin就会出现此bug，增加样式_display:inline(或block); ie6的常见问题还有[猛戳着里](http://www.frontopen.com/1130.html)

- [IE6 select穿透问题(div 定位无法遮盖select)! ](http://blog.csdn.net/yangzhihello/article/details/41207175)
- [IE6 1px的div](http://www.cnblogs.com/lancee/archive/2011/08/19/2145448.html)

- [ IE6 IE7 IE8(Q) 负边距导致元素溢出 hasLayout 容器时显示异常](http://w3help.org/zh-cn/causes/RB1001)

- text-align:center的子元素在非IE下不居中

```
// 在IE下div.child是居中现实的，但是在firefox和chrome下是left
       .container {
	    text-align: center;
	}
```

```
        <div class="container">
		<div class="child">
			child's content
		</div>
	</div>
```    

解决方案如下：

```
// 方法一：
       .container {
		text-align: center;
		text-align: -webkit-center;
		text-align: -moz-center;
	}
```

```
方法二：
       .container {
	    text-align: center;
	}
	.container .child {
	    margin: auto;
	}
```

- 不能自动展开包含框：触发Layout来解决
height:1%触法haslayout有一定风险，所以应使用height:0;或height:1px,但是
overflow:hidden与height不能同时设置（同时设置height的值就回对元素的真实高度产生影响）,此时应使用zoom:1或者display:inline-block
来触法haslayout

- 列表布局问题
- 
```
	<style>
	    ul {
	        width: 60%;
	    }
	</style>
	<ul>
	   <li>li1</li>
	   <li>li2</li>
	   <li>li3</li>
	</ul>
 ```
 
定义列表宽度为60%，那么ul元素就拥有了layout特性，如果在ie浏览，会发现列表的项目符号
消失了。
解决如下：

```
ul {
     width: 60%;
     padding-left: 1em;
}
```

- 在IE6及以下版本浏览器中，当列表项元素li中包含有块状现实的超链接元素时，列表元素之间的空格将不会被忽略，而且会额外增加一行；

```
	.a2,.a3 {
	     display: block;
	}
	<ol>
	    <li class="l1"><a href="#" class="a1">a1</a></li>
	    <li class="l2"><a href="#" class="a2">a2</a></li>
	    <li class="l3"><a href="#" class="a3">a3</a></li>
	    <li class="l4"><a href="#" class="a4">a4</a></li>
	</ol>
```

在l3和l4下面都会多出一个空行；
解决如下：

```
	.a2,.a3 {
	     display: block;
	     zoom: 1;
	}
```

- 定位布局问题
由于相对定位不能触法Layout特性，所以在使用定位时会出现莫名其妙的错误，如定位对象丢失、定位元素错位等，所以建议相对定位元素声名zoom:1

- IE6当在浮动元素之间增加HTML注释时下方会出现多余字符；

- [IE6 躲猫猫BUG的解决方案](http://blog.csdn.net/hedong37518585/article/details/6639222)

- IE浏览器出现背景与内容分离时，考虑触发Layout特性来解决

## JS

更多关于dom操作的兼容性请参考《js高级程序设计(第三版)》第10章

- 以前有篇文章：[跨浏览器的EventUtil&&StyleUtil](http://www.jianshu.com/p/f1d5e1e5fa5f)

- ie可以使用常规属性方法获取自定义属性（elment.customProp），也可以使用getAttribute("customProp")获取，FireFox只能使用getAttribute();

- ie的event对象有[x,y(看这里)](http://www.w3school.com.cn/jsref/dom_obj_event.asp)属性，没有pageX,pageY;Fireforx下有pageX，pageY，没有x,y；

- IE8前event没有pageX，pageY，解决如下

      event = event || window.event;
      var pageX = event.pageX;
          pageY = event.pageY;

      if (pageX === undefined) {
        // document.body（混杂模式），document.documentElement（标准模式）
        pageX = event.clientX + (document.body.scrollLeft || document.documentElement.scrollLeft);
      }
      if (pageY === undefined) {
        pageY = event.clientY + (document.body.scrollTop || document.documentElement.scrollTop);
      }

- cloneNode()方法在IE下会复制事件处理程序（非IE下不会），所以在复制之前最好先移除事件处理程序；

- 在IE中可以给createElement()传入完整的元素标签，也可以包含属性：
- 
```
var div = document.createElement("<div id=\"myNewDiv\" class=\"box\"></div>");
/*
*这种方法有助于避开IE7及更早版本中动态创建元素的某些问题：
*1.不能设置动态创建的iframe元素的name特性；
*2.不能通过表单的reset方法重设动态创建的input元素；
*3.动态创建的type特性为reset的button元素重设不了表单
*4.动态创建的一批name相同的单选按钮彼此毫无关系。
*/
```

- attributes属性，在IE7及更早版本会返回HTML元素中所有可能的特性，包括没有指定的特性。可以通过attribute的specified属性过滤，所有未设置过的特性该属性都是false，而其它浏览器不会为这类特性生成特性节点。

- 下面代码在IE解析下ul会有3个字节点（3个li），而在其他浏览器会有7个元素（3个li和4个li元素之间的文本节点）：

```
	<ul id="myList">
	     <li>Item 1</li>
	     <li>Item 2</li>
	     <li>Item 3</li>
	 </ul>
```

- IE不允许访问script和style的子节点，所以动态创建script和style应这样处理：

```
	// Script
	function loadScriptString(code) {
	     var script = document.createElement("script");
	     script.type = "text/javascript";
	     try {
	         script.appendChild(document.createTextNode(code));
	     } catch(e) {
	        script.text = code;
	     }
	     document.body.appendChild(script);
	}
```

```
	// Style
	function loadStyleString(code) {
	      var style = document.createElement('style');
	      style.type = "text/css";
	      try {
	          style.appendChild(document.createTextNode(code));
	      } catch(e) {
	          style.styleSheet.cssText = code;
	      }
	      var head = document.getElementsByTagName("head")[0];
	      head.appendChild(style);
	}
```

- style,getComputedStyle,currentStyle,defaultView的区别
[获取元素CSS值之getComputedStyle方法熟悉](http://www.zhangxinxu.com/wordpress/2012/05/getcomputedstyle-js-getpropertyvalue-currentstyle/)
有几点需要注意：
1、虽然currentStyle与getComputedStyle都是获取计算后的属性，但是键名还是有不少差异，比如“浮动”对应的键名可能是
cssFloat与styleFloat，获取值的时候应该这样

```
	window.getComputedStyle(element, null).getPropertyValue("float");// 属性名不需要驼峰式
```
2、但是ie9之前又不支持getPropertyValue，在老的IE浏览器（包括最新的），getAttribute方法提供了与getPropertyValue方法类似的功能

```
	style.getAttribute("backgroundColor"); //属性名需要是驼峰式
```
- IE下是支持firstChild,lastChild,nextSibling,previousSibling,
但是在FF下，由于它会把标签之间的**空格当成文本节点**，所以为了准确地找到相应的元素，应该使用
:firstElementChild,lastElementChild,nextElementSibling,
previousElementSibling

## 参考

> 1.[Block formatting context](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Block_formatting_context)：介绍块级格式化上下文；<br/>
> 2.[前端精选文摘：BFC 神奇背后的原理](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)：介绍了什么是BFC，及BFC的应用；<br/>
> 3.[前端知识点](http://blog.csdn.net/kongjiea/article/details/46341575)：介绍了各种需要了解的点；<br/>
> 4.[Definition of "containingblock"](https://www.w3.org/TR/CSS21/visudet.html#containing-block-details)：包含快的定义<br/>
> 5.[前端精选文摘：那些年我们一起清除过的浮动](http://www.cnblogs.com/lhb25/p/story-of-clear-float.html):内容包括如何触发BFC和hasLayout<br/>
> 6.[IE6中的常见BUG与相应的解决办法](http://www.frontopen.com/1130.html)<br/>
> 7.[获取元素CSS值之getComputedStyle方法熟悉](http://www.zhangxinxu.com/wordpress/2012/05/getcomputedstyle-js-getpropertyvalue-currentstyle/)<br/>
> 8.[兼容的firstChild,lastChild,nextSibling,previousSibling写法(转)](http://blog.sina.com.cn/s/blog_4586764e0101s6b7.html)<br/>
