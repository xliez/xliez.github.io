---
title: webpack与postcss与less
date: 2017-04-17 21:21:19
tags:
- 前端
- webpack
catalogies:
- 学习笔记
---

# 前言

以前学过stylus，今天去github看了看，发现作者一年没更新了，再去看了看sass和less，sass依旧还需要ruby，果断放弃，于是决定以后就用less了。

于是花一下午学习了下[webpack](https://webpack.js.org/)与[postcss](https://github.com/postcss/postcss)与[less](http://lesscss.org/#)这三个东西，其实less语法倒没怎么看，多是折腾webpack和其他两个如何组合使用了，写个笔记梳理一下。

*webpack版本为2.4.1*

<!--more-->
# 安装webpack

首先安装webpack这个最主要的东西。

`npm i webpack --save-dev`

安装完成后在package.json中的`script`中加入`"start": "node_modules/.bin/webpcak"`这样就可以直接`npm webpack`构建(如果你是全局安装的话可以直接webpack指令，-*官方不推介全局安装*-)

# 配置webpack

接下来就是配置webpack。

官方文档里将webpack的配置主要概括为4点：
- *entry* = 入口
- *output* = 输出
- *loaders* = 处理器
- *plugins* = 插件

在这里逐个讲解。

## 配置entry

entry表示着你要webpack处理的文件的入口点。

通俗点讲就是，你总有一个文件是不被任何其他文件require的（这里还有一个nodejs中的`parent moduel`的概念），那么这个文件就是你的入口。

当然你不一定只有一个入口，也可以有多个入口。

这里就复制官方文档的代码了，单文件：

```js
module.exports = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
}
```
多文件入口：

```js
module.exports = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
}
```

## 配置output

output就是webpack后生成的文件。

主要就两个的东西：`filename`与`path`

-*`filename`不一定是自己命名的`bundle.js`*，有`[id].js`或`[name].js`等系统生成的命名-

*注意path必须为绝对路径，因此可以使用path.resolve解决*

*详细请看官方文档*

```js
const config = {
  output: {
    filename: 'bundle.js',
    path: '/home/proj/public/assets'
  }
};

module.exports = config;
```

## 配置loaders

> Loaders are transformations that are applied on the source code of a module. They allow you to preprocess files as you require() or “load” them. Thus, loaders are kind of like “tasks” in other build tools, and provide a powerful way to handle front-end build steps. Loaders can transform files from a different language (like TypeScript) to JavaScript, or inline images as data URLs. Loaders even allow you to do things like require() CSS files right in your JavaScript!

以上是官方对loader的解释。

我的理解就是对源文件进行处理或构建的规则与工具的配置点。

*loader都是要自己npm安装的*

```js
const config = {
//省略
 module: {   //Object
    rules: [  //Array
      {
        test: /\.css$/, //正则表达式，匹配你要处理的文件
        use: [     // Array     //使用的loader插件，注意它是逆序排列，即先用最后一个loader处理，再由倒数第二个处理，最后才交给第一个loader处理
          { loader: 'style-loader'},
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          }
        ]
      }
    ]
  }
}

module.exports = config
```

## 配置plugins

> They also serve the purpose of doing anything else that a loader cannot do.

这个主要是webpack自带的一下功能插件，如处理html文件

```js
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins
const path = require('path');

const config = {
  entry: './path/to/my/entry/file.js',
  output: {
    filename: 'my-first-webpack.bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        loader: 'babel-loader'
      }
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

# postCss与less

postCss也是第一次用，查了下文档，大致就是一个既能自己定义css语法（如less的功能），又能对css文件进行后处理的前后处理工具，不过我主要是看中了autoprefixer功能。

与webpack结合的话就是用postcss-loader。

less具体语法就不说了，要在webpack使用的话就是用less-loader。

*记得所有loader要npm安装，less也是*

以下就是我最终的配置：

```js
//postcss.config.js
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```


```js
//webpack.config.js
module.exports =  {
  entry: {
    css: path.resolve(__dirname, './index.js')
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, './dist')
  },
  module: {
    rules: [
      {
        test: /\.less$/,
        exclude: /node_modules/,
        use: ['style-loader',
          {
            loader: 'css-loader',
            options: {
              importLoaders: 1
            }
          },
          'postcss-loader',
          'less-loader'
        ]
      }
    ]
  }
}
```

# 结语

在我将所有都配置好后，在vsCode里发现一个插件：[Easy Less](https://marketplace.visualstudio.com/items?itemName=mrcrowl.easy-less)，此插件兼 编译less文件为css文件 与 自动autoprefixer 一体，用这个其实就不要postcss-loader和less-loader了。