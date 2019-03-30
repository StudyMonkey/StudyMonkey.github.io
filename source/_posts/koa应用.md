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

// 页面效果 Hello World1
```

- koa的接口实现

```
// 所需依赖包都需要npm install 下载
// 示例请求豆瓣API

const koa = require('koa');
// 处理路由
const Router = require('koa-router');
const router = new Router();
// 处理跨域
const cors = require('koa2-cors');
// query值的获取
const bodyParser = require('koa-bodyparser');
// http请求
const axios = require('axios');

const app = new koa();
app.use(cors());
app.use(bodyParser());
const apiKey = '某变量';

router.get('/demo', async ctx => {
    ctx.body = '<div>Hello World</div>'
})

/* 
 * 电影Top250接口
 * param 电影ID
 */
router.get('/movie/top250', async (ctx, next) => {
    const { param } = ctx.query;
    const res = await axios.get('https://api.douban.com/v2/movie/top250?apikey='+ apikey +'&start='+ param +'&count=20')
})

/* 
 * 口碑周榜接口
 */
router.get('/movie/weekly', async (ctx,next) => {
	const { param } = ctx.query;
    const res = await axios.get('https://api.douban.com/v2/movie/weekly?count=20&apikey='+apikey+'&start='+ param);
    ctx.body = res.data;
	await next();
})

/* 
 * 电影搜索接口
 */
router.get('/movie/search', async (ctx,next) => {
	let { param } = ctx.query;
	param = encodeURIComponent(param);
    const res = await axios.get('https://api.douban.com/v2/movie/search?q='+param+'&apikey='+apikey);
    ctx.body = res.data;
	await next();
})

app.use(router.routes());

app.listen(3002, () => {
    console.log('3002端口')
})

```



