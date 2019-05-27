---
title: 初识Webpack（一）
date: 2019-04-27
tags:
  - IT技术
  - 前端
  - Webpack
---

#### 一、下载
```
// 新建目录webpack ，在目录下运行

npm install webpack webpack-cli --save-dev
```

#### 二、简单用法
```
// src目录下新建a.js

module.exports = () => {
    console.log('123')
}

// 新建index.js
const hello = require('./a.js');

hello();

// 执行命令 
npx webpack 

会在webpack目录下生成一个dist/main.js
```


基本配置：
新建==webpack.config.js==

- 配置介绍：
- 1. entry  入口文件
```
// 写法
entry: './index.js'

或

entry: {
    main: './index.js'
}
```

- 2. output  出口文件
```
// 写法
const path = require('path');
output: {
    // 指定打包之后的名称
    filename: 'bundle.js', // 不写默认是main.js
    
    // 制定打包之后的路径
    path: path.resolve(__dirname, 'dist')
}
```

- 3. module  加载模块

> 检测到什么类型的文件，就使用对应的处理模块
```
// 写法
rules: [
    // file-loader处理图片
    {
        test: /\.jpg$/,
        use: {
            loader: 'file-loader',
            // 打包的配置项
            options: {
                [name] 占位符形式，表示原文件名称
                name: "[name]_[hash].[ext]",
                // 指定打包输入路径
                outputPath: 'images/'                
            }
        }
    },
    // url-loader处理图片格式
    {
        test: /\.(jpg|png|gif)$/,
        use: {
            loader: 'url-loader',
            name: [name]_[hash].[ext],
            outputPath: 'images/'
            // 指定不超过配置字节数舒勇url-loader，否则与file-loader结果相同
            limit: 2048
        }
    },
    // 处理样式
    {
        test: /\.css$/,
        use: ['style-loader', 'css-loader', 'postcss-loader']
        
        // 或者
        
        user: [
            { loader: 'style-loader' },
            { 
                loader: 'css-loader',
                options: {
                    // css文件里面有 import 另外的css，此时，这个另外的css只会被css-loader编译，不会被下面的两个loader编译，加上这个配置就可以从下往上再执行一次编译
                    importLoaders: 2,
                    // css 模块化
                    modules: true
                }
            },
            { loader: 'sass-loader' },
            { loader: 'postcss-loader' }
        ]
        
        // css-loader 会把多个css文件打包为一个css文件
        // style-loader 会把打包好的样式加载到style中
        // postcss-loader 根据postcss.config.js为所写的css 3样式自动加上浏览器厂商前缀-webkit-,-moz-等
        
    },
    // 处理sass文件格式
    {
        test: /\.sass$/,
        use: ['style-loader', 'sass-loader']
    },
    {
        test: /\.js$/,
        // 不匹配 node_modules 里面的js
        exclude: /node_modules/,
        use: {
            // babel-loader 是babel与webpack之间的桥梁
            loader: 'babel-loader',
            // @babel/preset-env 才是 es6 转 es5的关键包
            options: {
                // 如果只是写业务代码，则推荐用@babel/preset-env
                presets: [ ["@babel/presets-env", {
                    targets: {
                        chrome: "67"  
                    },
                    // 配置@babel/polyfill 按需加载
                    useBuiltIns: "usage"
                }]]
                
                /* 如果是写类库的代码，推荐@babel/plugin-transform-runtime , 需要下载@babel/runtime-corejs2
                "plugins": [["@babel/plugin-transform-runtime", {
                    "corejs": 2,
                    "helpers": true,
                    "regenerator": true,
                    "useModules": false
                }]
                */
            } 
            /* 或者新建一个.babelrc文件写配置
            {
                "presets": [ "@babel/presets-env" ]
            }
            */
        }
    }    
]
```
- @babel/polyfill 和 @babel/plugin-transform-runtime的使用场景
- @babel/polyfill 适用于自己所创建的新建项目的打包编译es 6语法
- @babel/plugin-transform-runtime 适用于在别人已有项目的基础上打包编译的，不会像polyfill一样产生全局变量，造成全局污染
> ==需要特别注意的是，Webpack中处理css的loader加载顺序是自下往上，自右向左==

- 4. Plugins
> 插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务
```
// 写法
const HtmlWebpackPlugin = require('html-webpack-plugin');

const CleanWebpackPlugin = require('clean-webpack-plugin');

// 处理css打包成一个通过link引入的css文件
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

const webpack = require('webpack');

plugins: [ 
    new CleanWebpackPlugin(), // 自动删除dist文件夹的插件 或  new CleanWebpackPlugin(['dist'])
    // 热更新插件，webpack自带的
    new webpack.HotModuleRepalcementPlugin(),
    new HtmlWebpackPlugin({
      title: 'html 模板',
      filename: 'test.html', // 打包之后的html文件名
      template: './index.html'  // 模板html文件
    })]
```

- 5. mode  
> 模式
```
// 写法
mode: 'development' // 开发模式

// 或者

mode: 'production'  // 生产模式
```

- 6. devtool
> 此选项控制是否生成，以及如何生成 source map
```
// 写法

// 当mode为development时，默认产生sourceMap，此时想关掉的话，可以设置
devtool: 'none'

// 或

devtool: 'eval'  // none 和 eval这两种是打包速度最快的

// 或

devtool: 'source-map'  // source-map是打包速度最慢的，单独打包成了一个.map文件

// 或

devtool: 'cheap-source-map'  // 以base64的形式打包到js文件
devtool: 'cheap-inline-source-map'

// 或

devtool: 'cheap-module-eval-source-map' // 开发模式下推荐使用这种

// 线上环境可以不开启，如果要看到错误信息，推荐
devtool: 'cheap-module-source-map'
```

- 7. devServer
```
// 写法

// 修改 package.json 文件

"scripts": {
    "server" : "webpack-dev-server"
}

// webpack.config.js 文件

devServer: {
    // 启动server所依赖的文件夹
    contentBath: './dist' // 或  contentBase: path.join(__dirname, 'dist');
    // 自动打开浏览器
    open: true,
    // 开发时的代理
    proxy: {
        '/api': {
            target: 'http://localhost:9092'
        }
    },
    hot: true, // 开启热更新
    hotOnly: true // 即使没有热更新，也不自动刷新浏览器
    
    /*
        监听 JS 文件的热更新 例 number 文件
        if ( module.hot ) {
            module.hot.accept('./number', () => {
                // do something
            })
            
        }
    */
}
```
- path.join和path.resolve有什么区别

- 8. optimization
```
// 写法
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

#### 三、打包配置react代码
```
npm i react react-dom -D

npm i @babel/preset-react -D
```

#### 四、webpack.config.js 配置文件示例
```
const path = require('path');
// 打包之后自动生成html的插件
const HtmlWebpackPlugin = require('html-webpack-plugin');

// 打包之前自动删除dist的插件
const CleanWebpackPlugin = require('clean-webpack-plugin');

const webpack = require('webpack');

module.exports = {
    entry: './index.js', // 指定打包入口
    /* entry的对象写法
    entry: {
        main: './index.js'
    }
    */
    output: {
        filename: '', // 打包出的文件名
        // 绝对路径
        path: path.resolve(__dirname, 'dist'),
        publicPath: 'https://cdn.com.cn'
    },
    module: {
        // [name] 占位符写法，原文件名称
        // [ext] 原文件后缀
        
        // 模块加载规则
        rules: [
            // 检测到什么类型的文件，加载什么样的loader
            {
                test: /\.jpg$/,
                use: {
                    loader: 'file-loader',
                    options: {
                        // 图片打包之后的名称
                        name: "[name]_[hash].[ext]",
                        outputPath: "images/"
                    }
                }
            },
            {
                // test: /\.png$/,
                test: /\.(jpg|png|gif)$/
                use: {
                    loader: 'url-loader',
                    options: {
                        // 图片打包之后的名称
                        name: "[name]_[hash].[ext]",
                        outputPath: "images/",
                        // 当图片小于2048字节时，会使用url-loader打包成base64，超过2048则跟file-loader效果相同
                        limit: 2048
                    }
                }
            },
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.js$/,
                // 不匹配 node_modules 里面的js
                exclude: /node_modules/,
                use: {
                    // babel-loader 是babel与webpack之间的桥梁
                    loader: 'babel-loader',
                    // @babel/preset-env 才是 es6 转 es5的关键包
                    options: {
                        "presets": [ "@babel/presets-env" ]
                    }
                }
            }
        ]
    },
    Plugins: [ 
        new HtmlWebpackPlugin({
          title: 'html 模板',
          filename: 'test.html',
          template: './index.html'
        }),
        new CleanWebpackPlugin(),
        // 热更新插件
        new webpack.HotModuleReplacementPlugin()
    ],
    mode: 'cheap-inline-source-map',
    devServer: {
        // 启动server所依赖的文件夹
        contentBath: './dist' // 或  contentBase: path.join(__dirname, 'dist');
        // 自动打开浏览器
        open: true,
        // 开发时的代理
        proxy: {
            '/api': {
                target: 'http://localhost:9092'
            }
        },
        hot: true, // 开启热更新
        hotOnly: true  // 即使没有热更新，也不刷新浏览器
        
    },
    optimization: {
        // webpack 自带的代码分割 code spliting
        splitChunks: {
            chunks: 'all'
        }
    }
}
```