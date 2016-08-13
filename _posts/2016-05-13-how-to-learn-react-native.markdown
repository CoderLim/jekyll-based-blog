---
layout: post
title: react-native入门
date: 2016-05-13 23:32:54.000000000 +09:00
tags: 心情杂记
---

>此博客将记录我（ios程序员）学习RN很有价值的内容，代码学习可以参考[ReactNativeLeaning(ES6版)](https://github.com/CoderGLM/ReactNativeLeaning)，最后附了一个sinaWebBo项目，希望大家不吝赐个star。
> 支持一下新封装的[react-native-image-browser](https://github.com/CoderGLM/react-native-image-browser)，赏个star吧
> 增加“如何运行在android模拟器”

# 1、Hello World！
react-native init AwesomeProject 卡住不动：[解决React Native初始化项目速度很慢的问题](http://www.jianshu.com/p/62242e424bae)；

# 2、选个好用的编辑器：atom+nuclide的[详细使用方法](http://blog.csdn.net/hello_hwc/article/details/51612139)
这个编辑器很好用，有察看dom结构和debugger功能，不过如果你喜欢别的编辑器也可以用；
# 3、学习的两个重点，布局和语言
- 如果布局不会请参考阮一峰的[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)和张鑫旭的[CSS box-flex属性，然后弹性盒子模型简介](http://www.zhangxinxu.com/wordpress/2010/12/css-box-flex%E5%B1%9E%E6%80%A7%EF%BC%8C%E7%84%B6%E5%90%8E%E5%BC%B9%E6%80%A7%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8B%E7%AE%80%E4%BB%8B/)
- 如果es6不会请参考阮一峰的[ECMAScript 6入门](http://es6.ruanyifeng.com/)，如果你以前了解过js，再看看看es5与es6的[区别](https://segmentfault.com/n/1330000004266763)吧，这篇文章对于熟悉es5不熟悉es6的来说很有价值

# 4、控件是页面展示的基本组成
react是基于组件开发的，但是组件是由控件组成的。
什么是常用的，按我的经验来说几个，View(为布局而生)、Text(文本展示)、TextInput(输入框)、TouchableHighlight(按钮)、Navigator(导航控制器，多页面控件)、TabBarIOS(多页面控件)
[Navigator详解](http://bbs.reactnative.cn/topic/20/%E6%96%B0%E6%89%8B%E7%90%86%E8%A7%A3navigator%E7%9A%84%E6%95%99%E7%A8%8B/2)

# 5、看看官方的Demo
github地址[在这里](https://github.com/facebook/react-native)，在examples文件夹里有几个很简单的demo，对于熟练编码还是有价值的，不妨跟着敲一遍，不过都是es5实现的，其中movies的es6实现[在这里](https://github.com/CoderGLM/ReactNativeLeaning/tree/master/3.1FacebookMovies)（不过不完全）。
###5.1 如何运行官方的Demo呢？我用运行UIExplorer举例
- 1.cd 到UIExplorer文件夹；
- 2.npm install；安装完后，直接在UIExplorer文件夹下运行react-native run-ios，会报错：Command `run-ios` unrecognized. Did you mean XXX，为何会报错还不知道；
- 3.运行“开发服务器”，如果你的编辑器是atom，那么cmd＋shift＋p，输入start packager，回车就会打开服务器了(或者在UIExplorer文件夹下npm start即可)；
- 4.使用xcode运行。

### 5.2 可能会遇到的error
- 1.Could not connect to development server：没有启动服务器，如何启动上面提到了使用atom的启动方式和npm start方式；
- 2.cannot find entry file examples/UIExplorer：这是因为当前启动的服务器是别的项目的，需要重启服务器；


# 6、卡顿是用户体验的杀手
[React?Native痛点解析之性能调优](http://mt.sohu.com/20160601/n452304739.shtml)

# 7、看看真机运行效果
这里只说下ios的，android可以参考[ React Native真机调试 ](http://my.oschina.net/imot/blog/512808)
1、将AppDelegate中sourceURL的localhost改为电脑的ip；
2、启动服务器，启动方法在上面的5.1.3有提到；
3、运行程序。
ps：手机和电脑要用数据线连接；手机和电脑要连到相同的wifi下

# 8、如何运行在Android模拟器
安装jdk和android studio（都装最新的就行了），以下问题都是我按顺序遇到的：
1、SDK location not found. Define location with sdk.dir in the locSDK location not found. Define location with sdk.dir in the local.properties file or with an ANDROID_HOME environment variable
* 如何查看sdk location：打开android studio --> preferences -> 查找sdk即可看到路径；
* 设置ANDROID_HOME：参考https://spring.io/guides/gs/android/

`Mac OS X
export ANDROID_HOME= sdk 路径
export PATH=${PATH}:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools`


2、failed to find target with hash string 'android-23' in: 你的sdk的路径：直接在android studio中的preferences里搜sdk，然后apply就行了

3、failed to find Build Tools revision 23.0.1： 还是在android studio的preferences的Android SDK下，选择SDK Tools，勾选右下角的Show Package Details，选择23.0.1的就行了，然后apply

4、com.android.builder.testing.api.DeviceException: No connected devices!：找工具栏的AVD Manager，下载模拟器
