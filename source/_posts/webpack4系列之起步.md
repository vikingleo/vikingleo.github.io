---
title: webpack4系列 —— 起步
date: 2020-01-11 00:00:15
tags: 
    - webpack4
    - node
    - javascript
category: webpack4入门
---

# 参考案例

[➹使用webpack配置多页面应用(MPA)](https://segmentfault.com/a/1190000017565103)

[webpack4 的开发环境配置说明](https://segmentfault.com/a/1190000015835755)

# 说明

为什么要搞这个？

当然是有多页面后台的需求了！

我们的需求很明确：

1. html页面拥有公共的部分
2. 每个页面按照其所在目录生成合并后的html
3. 生成公用js、第三方js库
4. 生成每个页面自用的js
5. 打包图片资源
6. 使用scss/sass，生成一个style.css


# 安装必要的包

```shell
npm i webpack webpack-cli cross-env webpack-dev-server html-webpack-plugin clean-webpack-plugin extract-text-webpack-plugin css-loader postcss-loader style-loader sass-loader babel-loader babel-core babel-preset-env file-loader node-sass autoprefixer http-server -S-d
```
|插件|说明|
|-|-|
|webpack |  自动化工具|
|webpack-cli |  webpack命令行工具，全局或目录内安装都可以，不安装运行报错。|
|cross-env |  跨平台设置和使用环境变量的脚本。windows平台和linux平台设置NODE_ENV的方式不一样，此插件可以在不同平台设置相同的`NODE_ENV`。|
|webpack-dev-server |  启动使用`Express`的HTTP服务器，用于处理资源文件，当源文件有改动时，插件自动实时编译，编译的文件放在内存，目录中不可看到。|
|html-webpack-plugin| 该插件将为你生成一个 HTML5 文件， 其中包括使用 script 标签的 body 中的所有 webpack 包 |
|clean-webpack-plugin | 打包前清理之前打包的文件 |
|extract-text-webpack-plugin | 用于分离项目中的css文件 |
|css-loader | css加载器，处理css中`@import`,`url`之类的内容 |
|postcss-loader | 这货老朋友了， |
|style-loader | 常用做法是配合`css-loader`使用，将`css-loader`打包好的css以`<style>`形式插入到html文件中 |
|node-sass | 编译`sass`/`scss`用到 |
|sass-loader | 玩`sass`的会用到，必须先安装`node-sass`，编译`sass`/`scss`文件 |
|babel-loader | 这个更明白了，往下数3个都是将ES6（ES2015）编译成大多数浏览器都能识别的ES5 |
|babel-core | 如果某些代码需要调用Babel的API进行转码，就要使用babel-core模块。 |
|babel-preset-env | 根据配置的目标浏览器或者运行环境来自动将ES2015+的代码转换为es5。 |
|babel-polyfill |  Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign）都不会转码。（来自阮一峰博客）|
|file-loader | 当js代码中出现`import`/`require`调用一个文件时，将文件打包到输出目录，并替换打包后js代码里到调用路径 |
|autoprefixer | 自动添加css浏览器前缀 |
|http-server | 快速打开一个本地http服务器，用来直接跑打包后的文件,区别于`development`模式 |

# 目录

```
rootDir
├──config
│  ├── config.js
│  ├── webpack.config.base.js
│  ├── webpack.config.dev.js
│  └── webpack.config.production.js
└── webpack.config.js
```