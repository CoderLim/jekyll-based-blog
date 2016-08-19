---
layout: post
title: 基于jekyll＋github-pages的个人博客
date: 2016-07-1 21:00:00.000000000 +09:00
category: other
description: 博客对于我来说还是生活必备的，主要有两方面的作用：1、记录技术点；2、技术探讨.当然对于其他人博客还可以记录生活的点点滴滴，写日记等，其实博客就是电子笔记本，纸上可以写的都可以写到博客里；但是博客是公开的，是让博主有分享精神，第三方的博客很方便，但是有自己的博客不是更酷。个人博客与第三方博客的最大不同就是个人博客有更大的灵活性，随意设置样式，更换主题；可以把自己的简历写到博客里，省去了打印的烦恼，方便更新，更方便其他人（比如面试官）查看。
tags: personal;blog;jekyll;个人博客;github-pages;
---


![image]({{site.imageurl}}/assets/images/2016/personal-blog-joke1.png)<br/>

## 导语

> 从大学到现在我有多个平台的博客：[新浪][3], [CSDN][4], [简书][5]。博客对于我来说还是生活必备的，主要有两方面的作用：1、记录技术点；2、技术探讨。
> 当然对于其他人博客还可以记录生活的点点滴滴，写日记等，其实博客就是电子笔记本，纸上可以写的都可以写到博客里；但是博客是公开的，是让博主有分享精神，
> 分享你的知识，分享你的快乐与忧伤。

> 第三方的博客很方便，但是有自己的博客不是更酷。个人博客与第三方博客的最大不同就是个人博客有更大的灵活性，随意设置样式，更换主题；
> 可以把自己的简历写到博客里，省去了打印的烦恼，方便更新，更方便其他人（比如面试官）查看。

## 选择

### host gator

刚开始是看到[The Ultimate Guide To Building A Personal Website][1]，很高兴，终于可以有自己的博客了<br/>
![image]({{site.imageurl}}/assets/images/2016/personal-blog-happy.png)<br/>
于是开始按照教程购买配置，什么？购买？没错，是需要花钱的，购买的网站就是口碑一向很好的hostgator，庆幸的是有优惠券，可以去百科找hostgator有可用的
优惠券，购买域名和主机大概花了我400+软妹币。跟着教程走没问题，很简单。

一切都搞定了，安装wordpress，漂亮，[我的网站](http://gengliming.com)可以访问了。但是还要选择主题样式，很麻烦，选了主题还要找图片，唉～对于我这个
app程序员来说还真费劲，如果可以直接修改源码可能对于我来说还容易点。

就在我试了多个theme后，发现了jekyll主题，有多个主题都是我想要的，于是我就搜**如何把jekyll主题设置到wordpress**，发现了jekyll+github-pages打造自己
的博客，居然写着免费不花钱，什么？不花钱？！<br/>
![image]({{site.imageurl}}/assets/images/2016/personal-blog-freak.png)<br/>

其实还是需要花钱买域名的，主机就不需要了，这样就剩了300+软妹币，还好hostgator支持40天退款，但是最终退款是退到你的hostgator账户的，如果想转出来好想
还需要手续费，算了，放着吧，以后域名续费吧。

### jekyll

国外有很多人的技术博客都是基于jekyll。直接使用github主机，而且有很多主题可用，我当时创建时，是直接down的onevat的博客源码，然后自定义的。所以如果你
喜欢我的博客，就直接down我的代码，[这里][7]，但是不要忘记点击[这页面][7]右上角的star。其他都是次要的，主要的还是写博客，你需要懂得[markdown][8]语法。

## jekyll-based blog

我的故事讲完了，该轮到你动手了，跟着[jekyll中国的官方教程][6]进行，很简单，如果有不懂的地方欢迎留言。如果你没有github账号，就落伍了，我认为看到
这篇文章的人应该是搞技术的，真的该注册并日常使用github，没有的来[这里注册][9]。先让网站跑起来，更换域名下面再说。

### 添加google analytic统计

[来官网][10]，创建账户，添加必要的信息，系统就会生成一小段代码，按照要求粘贴到你的博客源码中就行了。

如果你是down的[我的源码][7]，就不用粘贴代码了，但是还是要创建账户，添加必要信息，将系统生成的代码中的几个值对应设置到_config.yml中的ga字段就行了。


### 添加评论分享模块

起初试了一下disqus，但是国内访问速度也是够了，有时压根就出不来，于是选择了[jiaThis][11]。进入jiaThis官网首页获取代码是关于**分享**的，如果需要就按教程来。
**评论**是[友言][12]提供的，好像友言被jiaThis收购了，所以在[jiaThis][11]有[友言][12]的入口。

关于评论和分享没什么可注意的，都是“获取代码”粘贴到你的源码相应位置就行了。

### SEO

什么是seo？seo叫做搜索引擎优化，可以让其他网友更容搜到你的文章，增加访问量。

如何做搜索引擎优化呢？如果你是down的[我的代码][7]就不用设置了，否则给你推荐[jekyll-seo-tag][13]，主要是优化meta数据。

### 如何使用我的域名

* 先购买域名，我的是hostgator的花了60左右rmb；
* [配置DNS的A record](https://help.github.com/articles/setting-up-an-apex-domain/#configuring-a-records-with-your-dns-provider), 配置好后下面有个dig的测试语句，
  测试没问题就没问题了；
* [设置你的github项目的域名](https://help.github.com/articles/adding-or-removing-a-custom-domain-for-your-github-pages-site/)<br/>
修改Name Server或者Manage DNS后要等最多48小时后才能访问你的域名
 
## 结语

絮絮叨叨终于说完了，这篇文章不是教程，而是引导，因为你看不出我具体是如何做的。上面提到的官方教程足够简单，所以就不细化浪费时间了。
既然有了自己的博客，不妨把个人简历放上去，让面试官可以对你有个好印象。


## 参考

1.[Jekyll使用篇 1 - 发布文章、加入评论功能、加入Google Analytics](http://www.jianshu.com/p/ffbbed22f984) : 内容如标题<br/>
2.[jekyll 官网](http://jekyll.bootcss.com/)：按步骤来 <br/>
3.[google 统计](https://www.google.com/analytics/): 加入浏览量，新用户等统计 <br/>


[1]: http://collegeinfogeek.com/personal-website/ "hostgator tutorial"
[2]: http://cn.hostgator.com/?utm_source=baidu&utm_medium=cpc&utm_campaign=%C6%B7%C5%C6-Hostgator&utm_term=hostgator "hostgator中国"
[3]: http://blog.sina.com.cn/gengliming110 "我的新浪博客"
[4]: http://write.blog.csdn.net/postlist "我的CSDN博客"
[5]: http://www.jianshu.com/users/6e62f1bcb77a/latest_articles "我的简书"
[6]: http://jekyll.bootcss.com/ "jekyll cn"
[7]: https://github.com/CoderGLM/jekyll-based-blog "jekyll-based-blog"
[8]: http://sspai.com/25137 "markdown"
[9]: https://github.com/ "github"
[10]: https://www.google.com/analytics/ "google 统计"
[11]: http://www.jiathis.com/ "jiaThis"
[12]: http://www.uyan.cc/ "友言"
[13]: https://github.com/jekyll/jekyll-seo-tag "seo"

