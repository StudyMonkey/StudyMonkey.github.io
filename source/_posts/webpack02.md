---
title: 初识Webpack（二）—— webpack 的高级概念
date: 2019-04-29
tags:
  - IT技术
  - 前端
  - Webpack
---

#### 一、Tree shaking
> ==tree shaking 译为树摇晃，即在部分代码中，如果我们导出了多个函数方法或变量，但我们只引用了其中的部分，为了不使打包之后的文件过大，我们会使用tree shaking把没有引用到的方法和变量摇晃掉，从而达到减少打包之后的文件大小的目的。==
---
> 只支持ES module，不支持commonjs的 require形式

```
// math.js

export const add = (a, b) => {
    console.log(a + b)
}

export const minus = (a, b) => { 
    console.log(a - b)
}

// index.js
import { add } from './math.js'

add(2, 3);

// 执行npx webpack 打包命令会将math.js中的所有方法都打包

// webpack.config.js 需增加配置
optimization: {
    usedExports: true
}

// package.json 增加配置
"sideEffects": false

// 或你希望Tree shaking不起作用的文件。
// Tree shaking 会对所引入文件没有导出内容的进行忽略，像@babel/polyfill, style.css这样的我们又必须引入对代码起作用

"sideEffects": ["@babel/polyfill", "*.css"]
```

#### 二、development 和 production 的打包区别及在package.json 中的配置
- development 中可配置devServer
- source-map 的配置区别
- Tree Shaking 的配置区别
- HRM(HotModuleReplacementPlugin)的配置区别
```
// package.json 

"scripts": {
    "dev": "webpack-dev-server --config ./build/webpack.dev.js",
    "build": "webpack --config ./build/webpack/prod.js"
}

// 这样分开写 webpack.dev.js 和 webpack.prod.js 会有大量重复代码

npm i webpack-merge -D

// webpack.dev.js 
const merge = require('webpack-merge');
const commonConfig = require('./webpack.common.js');
const devConfig = {
    
}

export default = merge(commonConfig, devConfig)

// 同理 webpack.prod.js
const merge = require('webpack-merge');
const commonConfig = require('./webpack.common.js');
const prodConfig = {
    
}

export default = merge(commonConfig, prodConfig)
```


#### 三、 code slpliting 代码分割
```
// 写法

// index.js 
npm i loadsh -D

import _ from 'loadsh' // loadsh 大小 1mb

// 业务逻辑代码1 mb
console.log(_.join(['a', 'b', 'c'], '***');
// 省略1万行代码
console.log(_.join(['a', 'b', 'c'], '***');

// 打包之后不压缩大小 2 mb


// 手动代码分割

// 新建 loadsh.js 
import _ from 'loadsh'
window._ = _

// index.js
console.log(_.join(['a', 'b', 'c'], '***');
// 省略1万行代码
console.log(_.join(['a', 'b', 'c'], '***');

// 修改 webpack.config.js 的配置
entry: {
    loadsh: './src/loadsh.js',
    main: './src/main.js'
}


// webpack 自动 code spliting 增加配置

optimization: {
    splitChunks: {
        chunks: 'all',
        /*
          chunks: 'async' 只对异步代码做分割
          chunks: 'initial' 只对同步代码做分割
        */
        // 大于30000字节才做代码分割，即30kb
        minSize: 30000,
        maxSize: 0,
        minChunks: 1, // 至少被用了多少次才做代码分割
        automaticNameDelimiter: '~', // vendors~main.中间的波浪线连接
        cacheGroups: {
            /*  
                vendors: false 若不配置filename，则名称为vendors组名再拼上entry的名字，即vendors~main.js
            */
            vendors: {
                test: /[\\/]node_modules[\\/]/,
                priority: -10, // 优先级，值越大，优先级越高
                filename: 'vendors.js'
            },
            /*
                default: false
           同理，若不配置filename，即default~main.js
            */
            default: {
                priority: -20,
                reuseExistingChunk: true, // 复用已经打包好的模块
                filename: 'common.js'
            }
        }
    }
}
```

#### 四、Lazy loading 懒加载
> 异步加载模块，根据代码来决定什么时候加载，而不是页面一刷新就加载。如，vue 路由的异步加载