---
layout: post
title: 前端性能优化
date: 2015.10.01 15:32.000000000 +09:00
category: 前端相关
description: 性能对于程序来说至关重要。本文是对雅虎军规的翻译。前段性能优化从Content、Server、Cookie、CSS、Javascript、Images、Mobile几方面总结。
tags: 前端;性能优化
---

## 导语:

> 性能对于程序来说至关重要。本文主要内容是对[文章](https://developer.yahoo.com/performance/rules.html)的翻译，再加上平常碰到的优化Tip，
> 从Content、Server、Cookie、CSS、Javascript、Images、Mobile几方面总结。
>
> 文中翻译的是需要解释的tip，所以有些看小标题就知道如何优化的我就不翻译了，如果这些未翻译的tip有需要的朋友可以给我留言。
>
> 本文章后续会把每个优化方案的翻译呈现给大家。

这里有篇文章，介绍的点很多：[点击进入](http://www.jianshu.com/p/be5aea4a222f)

## Content

### 1、减少Http请求

终端用户80%的响应时间花费在前端。这些时间大多数是与下载所有组件息息相关的，比如图片，样式表，脚本等。减少组件数量也就是减少渲染页面时需要的Http请求数。这个是加快页面速度的关键。

减少组件数量的一种方式是简化页面设计。但是又没有一种方式可以创建更丰富的内容的同时也能获得更快的响应呢？这里有几个技术点。

- 合并文件：将所有脚本文件合并成一个脚本文件，同样把所有样式表合并成一个；
- CSS Sprites：这是一种比较好的方法。合并你的背景图片，并且使用css的background-image和background-position属性展示需要的图片部分。
- 图片映射：不推荐，想了解[猛戳这里](https://www.w3.org/TR/html401/struct/objects.html#h-13.6)；
- 行内图片：使用[data:URL scheme](http://tools.ietf.org/html/rfc2397)把图片数据嵌入到实际页面。这种方式还没被所有主流浏览器支持。

### 2、减少DNS查询

DNS(Domain Name System)用来映射主机名和ip地址。当你输入网址到你的浏览器，浏览器会与DNS解析器通讯返回服务器的ip地址。DNS有时间消耗，通常查询一个主机名的ip地址需要20-120毫秒。浏览器只有在DNS找到对应主机名的ip地址完成后，才能下载任何东西。

缓存DNS查询可以得到更好的性能。缓存可以出现在一个由用户的ISP（互联网服务提供商）或者局域网维持的特殊缓存服务器，但是也会出现在个人用户电脑。DNS信息保存在操作系统的DNS缓存中（在window里叫做“DNS Client Service”）。大多数浏览器有自己的缓存，与操作系统的缓存是分开的。只要浏览器在自己的缓存中保存着DNS记录，当请求一个记录时就不会理会操作系统。

IE默认缓存DNS查询是30分钟，是由注册表的DnsCacheTimeout指定的。FireFox缓存DNS查询是1分钟，由设置里的network.dnsCacheExpiration来控制。

当客户端的DNS缓存是空的（包括浏览器和操作系统），DNS查询的次数与唯一的主机名的个数相等。这些包括页面的URL、脚本文件、样式表、Flash对象等的主机名。减少唯一的主机名就能减少DNS查询。

减少唯一主机名的数量潜在的会减少发生在页面中并行下载的数量。避免DNS查询减少响应次数，但是减少并行下载的数量可能会增加响应次数。我的指导意见是分离这些组件到2-4个主机名下。这个结果是在减少DNS查询和允许高度并行下载之间的折中。

### 3、避免重定向

重定向的[http状态码][1]是301和302。这里有个301的例子：

```
      HTTP/1.1 301 Moved Permanently
      Location: http://example.com/newuri
      Content-Type: text/html
```

浏览器自动跳转到**location**指定的地址。所有关于重定向的必要信息都在这个header里。
响应体通常是空的。除非指定请求头(比如**Expires**或者**Cache-Control**)，否则301和302响应都不会缓存。

meta的refresh标签和javascript是其他重定向的方式，但是如果你必须重定向，最好的技术是使用标准的3xx状态码，
主要是确保返回按钮可以正确使用。

主要是需要记住重定向会降低用户体验。在用户和html文档之间插入重定向会延迟页面中的所有东西，因为在html文档到达前，没有东西可以渲染，
没有控件可以下载。

一种很损耗的重定向经常发生而且web开发者通常不知道。当URL的末尾的斜杠丢失时就会发生。比如，目标地址是[http://astrology.yahoo.com/astrology ](http://astrology.yahoo.com/astrology)
会收到一个301响应（包含了一个重定向到[http://astrology.yahoo.com/astrology/ ](http://astrology.yahoo.com/astrology/)。这种情况在Apache里通过使用
Alias 或者 mod_rewrite，或者DirectorySlash指令修复。

连接一个老站点和新站点是另一个常见的使用重定向的情况。使用重定向链接两个站点很简单，并且只需要很少的代码，但是这降低了用户体验。

### 4、使用Ajax缓存

### 5、延迟加载组件

### 6、预加载组件

### 7、减少DOM元素数量

### 8、跨域分离组件

### 9、最小化iframe个数

iframe允许html文档嵌入到父文档。理解iframe如何工作才能高效使用它。

iframe pros(优点，网络用语):
*(1) 对加载比较慢的第三方内容（比如广告）有帮助
(2) 安全的沙箱
(3) 并行下载脚本文件*

iframe cons(缺点):
*(1) 即使是空页面也是有消耗的
(2) 阻塞页面加载
(3) 非语义的*

### 10、避免404错误

注意这里是找不到文件，而不是自定义404页面。

HTTP请求是很耗时的，因此发送一个HTTP请求并且收到不可用的响应（i.e. 404 Not Found）是完全没用的，会降低用户体验。

有些站点有有用的404页面"Did you mean X?"，这个有很好的用户体验，但是也同样消耗了服务器资源（比如数据库）。更糟糕的是当页面链接到外部Javascript报404错误。首先，这个下载会阻塞并行下载，然后浏览器会把404响应当成js代码解析，试图在里面找到可用的内容。

## Server

### 1、使用CND

### 2、设置头文件过期或者静态缓存

### 3、Gzip压缩组件

### 4、配置ETags

### 5、尽早释放缓存

### 6、使用GET方法发送Ajax请求

Yahoo!Mail团队在使用XMLHttpRequest时发现：POST请求在浏览器中的实现包含两步处理：首先发送headers，然后发送数据。因此最好用GET，
这种方法只会使用一个TCP包去发送（除非你有很多cookie）。最大的URL长度在IE里是2k，因此如果你发送的多于2k最好就不要用GET。

一个有趣的副作用是没有发送任何数据的POST方法表现的就像GET。基于[HTTP说名](www.w3.org/Protocols/rfc2616/rfc2616-sec9.html),GET
的意思是获取信息，因此当你只想获取数据使用GET是有道理的（从语义上），而不是发送数据保存到服务端。

### 7、Image避免空src

## Cookie

### 1、减小Cookie大小

### 2、为组件使用无Cookie的域名

当浏览器请求一张静态图片时也会带着cookie，但是服务器并不会使用这些cookie。因此他们无合理原因的创建了网络流量。你应该确保静态组件应该是无cookie的请求。创建一个自域名并把你的静态资源放那里。

如果你的域名是www.example.org,你可以安排你的静态资源在static.example.org。但是，如果你已经设置cookie在顶级域名example.org而不是www.example.org，那么所有的请求到static.example.org的请求也会包括这些cookie。这种情况下，你就应该购买一个完整的新域名了，
把你的静态组件放那里，并且保持这个域名是域名无关（cookie－free）的。

静态资源放到cookie无关的域名还有另一个好处：一些代理拒绝缓存带着cookie请求的组件。与此相关的,如果你在考虑主页使用example.org域名还是使用www.example.org,应该考虑cookie的影响。删除www会让你只能把cookie写在*.example.org下，因此为了性能的原因最好使用www
的子域名，并把cookie写在子域名下。

## Javascript

关于语法的优化，直接上张图：

![image]({{ site.imageurl }}/assets/images/2016/javascript-syntax-optimize.png)

## CSS

### 1、把样式表放到上边

### 2、避免Css表达式

### 3、选择<link>而不是@import

### 4、不要使用过滤器

### 5、[如何提升 CSS 选择器性能](http://www.jianshu.com/p/268c7f3dd7a6)

## JS

### 1、把Scripts放到底部

### 2、使用外部的js和css

### 3、压缩js和css

### 4、移除重复的Scripts

### 5、最小化DOM访问

### 6、预加载组件

### 7、开发灵活的事件句柄

有时因为有很多经常执行的事件附加到了DOM树里不同的元素上，导致页面响应慢。这就是使用时间代理是好方法的原因。如果你的div里有10个按钮，应该附加一个事件到div的wrapper上，而不是每个按钮都绑定一个事件。事件向上冒泡，你就能捕获事件，并且知道是哪个button产生的。

你也不必为了操作DOM树而等待onload事件。经常你需要的就是想访问的在DOM树里的元素是可用的。你不必等待所有图片下载完。DOMContentLoaded你可以用来取代onload，但是需要所有浏览器都支持才可用，你可以是YUI事件工具，它有onAvailable方法。

更多的信息请查看Julien Lecomte写的YUI theatre's "High Performance Ajax Applications"

## Images

### 1、优化图片

### 2、优化CSS Sprites

### 3、不要在html中缩放图片

### 4、使favicon.ico变小且可缓存

favicon.ico 是保存在服务器网站根目录的图片。它是一个必然的危害，因为即使你不关心它，浏览器仍然会请求它，因此最好不要产生404响应。而且因为在同一个服务器上，每次请求它cookie都会被发送。这个图片还会阻碍下载队列，比如在ie中，当你在onload中请求额外的组件时，favicon将会在额外组件之前下载。

因此减缓有favicon这个缺点应确保：
* 文件小，最好低于1K；
* 设置你因为合适的Expires请求头（因为当你决定更改它的时候，你不能重命名）。你可以安稳的设置过期时间是未来的几个月。你可以查看最后一次favicon的修改时间做出有依据的决定。

[imagemagick](http://www.imagemagick.org/script/index.php)可以帮你创建小favicons.

## Mobile

### 1、使组件小于25KB

### 2、将组件打包到多部分的文档中

## Browser

### 1、避免reflow：[Repaint 、Reflow 的基本认识和优化][2]


## 参考：

[Best Practices for Speeding Up Your Web Site](https://developer.yahoo.com/performance/rules.html) <br/>
[Repaint 、Reflow 的基本认识和优化](https://segmentfault.com/a/1190000002629708)<br/>
[《Javascript高级教程(第3版)》]()


[1]:http://baike.baidu.com/link?url=6SjPcEJePTLKWT4ERuBR6NFpRPbuUyPip7srH1tSC791q93iERuyO96TbP3im8jBqBfbkSvn8x3Dwznyxexld_ "http code"
[2]:https://segmentfault.com/a/1190000002629708 "Repaint 、Reflow 的基本认识和优化"
