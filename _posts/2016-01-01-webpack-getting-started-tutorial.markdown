---
layout: post
title: webpack入门教程
date: 2016.01.01 00:00.000000000 +00:00
category: 前端相关
description: webpack的使用方法
tags: 前端;webpack;入门;
---

## WELCOME

通过这个教程你会了解到：<br/>

* 如何安装**webpack**;
* 如何使用**webpack**;
* 如何使用**loaders**;
* 如何使用**开发服务器**;

## 安装webpack

你需要已经安装安装[node.js](https://nodejs.org/).

```
$ npm install webpack -g
```

> 这使得webpack命令可以使用

## 设置编译器(Compilation)

以一个空文件夹作为开始. <br/>

创建这些文件:<br/>

**add entry.js**

```
document.write("It works");
```

**add index.html**

```
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <script type="text/javascript" src="bundle.js" charset="utf-8"></script>
    </body>
</html>
```

然后执行下面的命令:

```
$ webpack ./entry.js bundle.js
```

它将会编译你的文件并且创建一个**bundle**文件.<br/>

如果成功就会显示如下:

```
Version: webpack 1.12.11
Time: 51ms
    Asset     Size  Chunks             Chunk Names
bundle.js  1.42 kB       0  [emitted]  main
chunk    {0} bundle.js (main) 28 bytes [rendered]
    [0] ./tutorials/getting-started/setup-compilation/entry.js 28 bytes {0} [built]
```

在浏览器中打开index.html，将会显示`It works`.

## 第二个文件

接下来添加**content.js**:

```
module.exports = "It works from content.js.";
```

更新**entry.js**

```
document.write(require("./content.js"));
```

然后重新编译:

```
$ webpack ./entry.js bundle.js
```

刷新你的浏览器（index.html）,你将会见到`It works from content.js.`

> webpack将会通过你的**entry**文件分析对于其他文件的依赖。
> 这些文件（叫做模块）也包含在bundle.js，webpack将会给每个模块i 个
> 唯一id，并且在bundle.js通过id将所有模块保存成可访问的.
> 只有entry模块在启动时被执行。一个小的运行时提供了**require**函数
> 并且当需要的时候执行这些依赖。

## 第一个loader

我们想在程序中添加css文件。

webpack只能处理本地的javascript，因此我们需要`css-loader`来处理CSS文件。
我们也需要`style-loader`来实现在CSS文件中的样式。

运行`npm install css-loader style-loader`来安装loaders。（它们需要本地安装，不需要`-g`）
这将会创建一个`node_module`文件夹，loaders存在于这个文件夹里。

让我们来使用它们，添加**style.css**:

```
body {
    background: yellow;
}
```
更新**entry.js**:

```
require("!style!css!./style.css");
document.write(require("./content.js"));
```
重新编译，并且刷新浏览器，你将会看到黄色背景。

> 通过将loader作为模块请求的前缀，模块就能穿过loader的管道。这些loader按指定的方式转化文件的内.
> 当转化被应用后，结果就是javascript模块。

## 绑定loader

我们不像写着么长的require `require("!style!css!./style.css");`.

我们可以把文件扩展名绑定到loader，这样我们只需要写：`require("./style.css")`

更新**entry.js**

```
require("./style.css");
document.write(require("./content.js"));
```

编译：

```
webpack ./entry.js bundle.js --module-bind 'css=style!css'
```
> 一些环境可能需要双引号：–module-bind “css=style!css”

你将会见到刚才相同的页面。

## 配置文件

我们想把相关的配置放倒配置文件里，添加**webpack.config.js**

```
module.exports = {
    entry: "./entry.js",
    output: {
        path: __dirname,
        filename: "bundle.js"
    },
    module: {
        loaders: [
            { test: /\.css$/, loader: "style!css" }
        ]
    }
};
```

现在我们只需要运行`webpack`编译，结果如下：

```
Version: webpack 1.12.11
Time: 379ms
    Asset     Size  Chunks             Chunk Names
bundle.js  10.7 kB       0  [emitted]  main
chunk    {0} bundle.js (main) 8.86 kB [rendered]
    [0] ./tutorials/getting-started/config-file/entry.js 65 bytes {0} [built]
    [1] ./tutorials/getting-started/config-file/style.css 943 bytes {0} [built]
    [2] ../~/css-loader!./tutorials/getting-started/config-file/style.css 201 bytes {0} [built]
    [3] ../~/css-loader/lib/css-base.js 1.51 kB {0} [built]
    [4] ../~/style-loader/addStyles.js 6.09 kB {0} [built]
    [5] ./tutorials/getting-started/config-file/content.js 45 bytes {0} [built]
```
> webpack命令将会在当前目录下加载**webpack.config.js**

## 更好看的输出

如果项目变大，编译就有可能会久。因此我们想展示进度条。并且我们希望是彩色的...

我们可以这么做：

```
webpack --progress --colors
```

## watch模式

我们不想每次修改都要手动重新编译，可以这样做：

```
webpack --progress --colors --watch
```

webpack可以缓存未修改的模块和输出文件。

> 当使用watch模式时，webpack向所有文件安装watcher。如果有变化被探测到，它就会重新编译。当缓存可用时，webpack保存每个模块
> 到内存中，如果模块没有改变就会重用。

## development server

development server更好些。

```
npm install webpack-dev-server -g
```

```
webpack-dev-server --progress --colors
```

这个是在localhost:8080上绑定了一小的express服务，服务于你的静态资源和bundle（自动被编译的）。
它将会在bundle被编译(SockJS)后自动更新浏览器页面。在你的浏览器中打开[http://localhost:8080/webpack-dev-server/bundle]()。

> dev server使用的就是webpack的watch模式。它阻止webpack把结果文件放到磁盘上，而它是从内存中提供和保存结果文件的。

## 参考

[webpack-getting-started](https://webpack.github.io/docs/tutorials/getting-started/)：真正的入门教程
[WebpackTutorial](https://github.com/AriaFallah/WebpackTutorial/tree/master/part1)：更多入门教程
