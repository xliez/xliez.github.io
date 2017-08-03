---
title: 学习hexo写博客
date: 2017-03-12 15:11:18
tags: 
    - 学习笔记
categories:
    - 学习笔记
---

## 前言

前段时间写了个博客程序，前端用vue全家桶，后端用express+mongoose，数据库自然是mongodb。

花了两周时间写了大概70%，实现了博客文章的增删改查、主页面的展示和后端的管理页面，页面风格模仿了hexo中的NexT主题，但是对于标签和目录结构的那部分很难下手，因为我感觉我的数据库设计部分就设计的不好，一改几乎后端部分就得重写了。

想了想反正这烂代码只是学习中的产物，后端部分毫无抽象可言，干脆回炉重炼，遂一怒之下删掉了所有代码。。。。（现在有点后悔，毕竟vue模块部分是可以拿来重用的。。。。

重写之前想了想，本身个人博客还用后台CMS这一点本身就很麻瓜，因为现在都是写markdown，不如像hexo那样直接从md文件中读取文章信息到数据库。

于是，此系列就是分析hexo源码来学习写一个“伪”静态博客。

<!-- more -->

## hexo是如何分析md文件的？

[hexo-front-matter](https://github.com/hexojs/hexo-front-matter)这个模块被用来分析md文件的头信息,即如下格式：

``` js
---
title: 学习hexo写博客
date: 2017-03-12 15:11:18
tags: 
    - 学习笔记
catalogies: 
    - 学习笔记

```

而这个模块又依赖于[JS-YAML](https://github.com/nodeca/js-yaml)这个模块，这个模块算是核心模块。hexo-front-matter 原理是利用正则表达式提取出头信息和文章正文，然后用JS-YAML提取头信息的object。

先看下代码中最关键的的正则表达式：

``` js
var rPrefixSep = /^(-{3,}|;{3,})/;
var rFrontMatter = /^(-{3,}|;{3,})\n([\s\S]+?)\n\1(?:$|\n([\s\S]*)$)/;
var rFrontMatterNew = /^([\s\S]+?)\n(-{3,}|;{3,})(?:$|\n([\s\S]*)$)/;
```
用nodejs跑了下发现第三个reg是合适的reg。

代码如下：

```js
const fs = require('fs');
var rFrontMatterNew = /^([\s\S]+?)\n(-{3,}|;{3,})(?:$|\n([\s\S]*)$)/;
fs.readFile('x.md', function(err, data) {
    if (err) {
        console.log(err);
        return;
    }
    // console.log(data.toString());

    let str = data.toString();

    let o = str.match(rFrontMatterNew);
    
    console.log(o);
});
```
输出：
```
[ '---\ntitle: 学习hexo写博客\ndate: 2017-03-12 15:11:18\ntags: \n    - 学习笔记\ncatal
ogies: \n    - 学习笔记\n---\n\n## 前言\n\n前段时间写了个博客程序，前端用vue全家桶，后\n',
  '---\ntitle: 学习hexo写博客\ndate: 2017-03-12 15:11:18\ntags: \n    - 学习笔记\ncatal
ogies: \n    - 学习笔记',
  '---',
  '\n## 前言\n\n前段时间写了个博客程序，前端用vue全家桶，后端用express+mongoose，数据库
自然是mongodb。\n\n花了两周时间写了大概70%，实现了博客文章的增删改查、主页面的展示和后\n',
  index: 0,
  input: '---\ntitle: 学习hexo写博客\ndate: 2017-03-12 15:11:18\ntags: \n    - 学习笔记
\ncatalogies: \n    - 学习笔记\n---\n\n## 前言\n\n前段时间写了个博客程序，前端用vue全家
桶，后\n' ]
```

lib/md-parse.js 实现了这个功能

根据这个设计数据库。
具体代码见我的github/exp/server/model

## hexo如何处理文件

hexo有一个box模块，能够加载、处理并且观察文件的改动，试着看了下代码，看不明白。不过提供了一个思路，那就是观察存放md文件的文件夹，然后对发生改动的文件与数据库进行对比，更新数据库。

nodejs的fs.watch不能满足需求，因为其似乎无法区分新建与删除，这里我用了[chokidar](https://github.com/paulmillr/chokidar)这个模块，很方便强大！

将数据库操作封装好，当文件变动时调用，用前面的模块解析文件，存入或修改数据库，其中将文件的时间作为查询关键词，因为一般情况下文章不会修改发表时间，而时间具有唯一性。

## 一些处理库

为了实现封装，写了一些简单的函数库，比如

- lib/watcher.js实现文件监控
- parase2db.js实现md文件信息转化为数据库文档的转化
- db2front.js实现数据库文档输出到前台时的处理，因为mongodb的ObjectId类型是前端不能处理的，并删除不能暴露的path的信息

## 设计路由

因为没有后台CMS，所以只有GET方法，很简单，不谈了

## 编写前端

对vue还不熟，只能看着文档写。以后单独写吧。

## 源码

[github](https://github.com/xliez/exp)