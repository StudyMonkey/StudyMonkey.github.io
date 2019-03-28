---
title: koa应用
date: 2019-03-31
tags:
  - IT技术
  - 前端
  - Nodejs
  - koa
---

## koa应用
- 安装引用
```

$ yarn add koa -S
$ npm i koa -S
$ cnpm i koa -S/cnpm i koa --save

```

- 简单app.js代码
```

const koa = require('koa');
const app = new koa();

app.use(async (ctx,next) => {
    ctx.body = 'Hello World'
    await next()
})

app.use(async ctx => {
    ctx.body += 1
})

app.listen(3002, () => {
    console.log('3002端口')
})
```



