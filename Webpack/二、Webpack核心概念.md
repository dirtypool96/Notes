---
title: 二、Webpack核心概念
date: 2020-11-30
sidebar: "auto"
categories:
  - Webpack
tags:
  - Webpack
---

::: tip

1、Plugin

2、sourceMap

3、热更新

:::

<!-- more -->
## 一、`Plugin`

### 1）使用`HtmlWebpackPlugin`

该插件会在打包结束后，自动生成一个`html`文件在`dist`目录中，并把打包生成的`js`自动引入到这个`html`中

`webpack.config.js`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    mode: "development",
    entry: {
        main: './src/index.js'
    },
    module: {},
    plugins:[new HtmlWebpackPlugin(
    )],
    output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, 'dist')
    }
}   
```

> 以上的应用可以得出，`Plugin`的作用就是在打包的某一时刻，帮你做一些处理，类似于生命周期函数

### 2）使用`clean-webpack-plugin`

打包之前，自动删除`dist`目录

```
yarn add clean-webpack-plugin -D
```

```
const CleanWebpackPlugin = require('clean-webpack-plugin');

plugins:[
    new HtmlWebpackPlugin({
        template: './src/index.html'
    }),
    new CleanWebpackPlugin()
],
```

## 二、`SourceMap`的配置

`SourceMap`是一个储存映射关系的信息文件，它知道转换后的文件的位置对应源文件的位置

配置：

```
module.exports = {
    mode: "development",
    devtool: "cheap-module-eval-source-map",//development生产环境
    // devtool: "cheap-module-source-map",//production线上环境
```

> 在`webpack`[官网有对应参数](https://www.webpackjs.com/configuration/devtool/)，还需注意**原理**

看阮一峰老师的理解：整个文件就是一个JavaScript对象，可以被解释器读取。它主要有以下几个属性：

- `version`：Source-map的版本。

- `file`：转换后的文件名。

- `sourceRoot`：转换前的文件所在的目录。如果与转换前的文件在同一目录，该项为空。

- `sources`：转换前的文件。该项是一个数组，表示可能存在多个文件合并。

- `names`：转换前的所有变量名和属性名。

- **mappings**：记录位置信息的字符串（`mappings:"AAAAA,BBBBB;CCCCC"`）

  1. 分号对应行，逗号对应位置。（转换后的）

  2. 位置对应：每个位置使用五位，表示五个字段

     第一位，表示这个位置在（转换后的代码的）的第几列

     第二位，表示这个位置属于`sources`属性中的哪一个文件

     第三位，表示这个位置属于转换前代码的第几行

     第四位，表示这个位置属于转换前代码的第几列

     第五位，表示这个位置属于`names`属性中的哪一个变量

## 三、热更新

### 1）`package.json`

```
"scripts": {
    "watch": "webpack --watch" 
  },
```

增加`--watch`，不能实现自动打包

### 2）`web-dev-sever`

`webpack.config.js`

```
devServer: {
    contentBase: path.join(__dirname, "dist")
},
```

`package.json`

```
"scripts": {
    "build": "webpack",
    "start": "webpack-dev-server"
},
```

当前用的版本，能解决**Error: Cannot find module 'webpack-cli/bin/config-yargs**的问题

```
"webpack-cli": "3.3.12",//暂时更改为3.3.12版本
"webpack-dev-server": "^3.11.0"
```

#### 3.2.1 proxy 属性

代理用于跨域