---
title: koa一些好用的中间件
date: 2019-04-01
tags:
  - IT技术
  - 前端
  - 全栈
  - Nodejs
  - koa
---

## koa一些好用的中间件
![koa](https://upload-images.jianshu.io/upload_images/4418297-0002e9f53db8b5eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

- Koa-router
路由是Web框架必不可少的基础功能，koa.js为了保持自身的精简，并没有像Express.js自带了路由功能，因此koa-router做了很好的补充，作为koa星数最多的中间件，koa-router提供了全面的路由功能，比如类似Express的app.get/post/put的写法，URL命名参数、路由命名、支持加载多个中间件、嵌套路由等。其他可选路由中间件：koa-route, koa-joi-router, koa-trie-router
```

const app = require('koa')();
const Router = require('koa-router');
const router = new Router();
app.use(router, routes());
router.get('/', async (ctx,next) => {
    ctx.body = 'Hello World'
})

```

- Koa-bodyparser
koa.js并没有内置Request Body的解析器，当我们需要解析请求体时需要加载额外的中间件，官方提供的koa-bodyparser是个很不错的选择，支持x-www-form-urlencoded, application/json等格式的请求体，但不支持form-data的请求体，需要借助 formidable 这个库，也可以直接使用 koa-body 或 koa-better-body
```

const koa = require('koa');
const app = new koa();
const bodyParser = require('koa-bodyparser');
app.use(bodyParser())

```

- Koa-static
Node.js除了处理动态请求，也可以用作类似Nginx的静态文件服务，在本地开发时特别方便，可用于加载前端文件或后端Fake数据，可结合 koa-compress 和 koa-mount 使用

- Koa2-cors
可用来处理跨域请求
```

// 用法：
const koa = require('koa');
const app = new koa();
const cors = require('koa2-cors');
app.use(cors())

```

- koa-views
koa-views对需要进行视图模板渲染的应用是个不可缺少的中间件，支持ejs, nunjucks等众多模板引擎。

- Koa-session
HTTP是无状态协议，为了保持用户状态，我们一般使用Session会话，koa-session提供了这样的功能，既支持将会话信息存储在本地Cookie，也支持存储在如Redis, MongoDB这样的外部存储设备

- koa-jwt
随着网站前后端分离方案的流行，越来越多的网站从Session Base转为使用Token Base，JWT(Json Web Tokens)作为一个开放的标准被很多网站采用，koa-jwt这个中间件使用JWT认证HTTP请求

- koa-helmet
网络安全得到越来越多的重视，helmet 通过增加如Strict-Transport-Security, X-Frame-Options, X-Frame-Options等HTTP头提高Express应用程序的安全性，koa-helmet为koa程序提供了类似的功能，参考[Node.js安全清单](https://segmentfault.com/a/1190000003860400)。

- koa-compress
当响应体比较大时，我们一般会启用类似Gzip的压缩技术减少传输内容，koa-compress提供了这样的功能，可根据需要进行灵活的配置

- koa-logger
koa-logger提供了输出请求日志的功能，包括请求的url、状态码、响应时间、响应体大小等信息，对于调试和跟踪应用程序特别有帮助，koa-bunyan-logger 提供了更丰富的功能

- koa-convert
对于比较老的使用Generate函数的koa中间件(< koa2)，官方提供了一个灵活的工具可以将他们转为基于Promise的中间件供Koa2使用，同样也可以将新的基于Promise的中间件转为旧式的Generate中间件

参考资料：[Koa中文网](https://www.itying.com/koa/)




