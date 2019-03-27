---
title: koa01
date: 2019-03-29
tags:
  - IT技术
  - 前端
  - Nodejs
  - koa
---

## koa源码
- 基础的http服务

```
const http = require('http');

const server = http.createServer( (req,res) => {
    res.writeHead(200);
    res.write('Hello World');
    res.end();
})
```
- Application的类

- Object.create
Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__

```
const person = {
  isHuman: false,
  printIntroduction: function () {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};

const me = Object.create(person);

me.name = "Matthew"; // "name" is a property set on "me", but not on "person"
me.isHuman = true; // inherited properties can be overwritten

me.printIntroduction();
```

- 重点：get和set函数

```
const demo = {
    _name: 'xm',
    get name(){
        return this._name
    },
    set name(val){
        console.log(`new name is ${val}`);
        this._name = val
    }
}

console.log(demo.name); // xm
demo.name = 'test'
console.log(demo.name); // test 
```

- response内容，request内容，context内容

```
let request = {
    get url(){
        return this.req.url
    }
}

let response = {
    get body(){
        return this._body
    }
    set body(val){
        this._body = val
    }
}

let context = {
    get url(){
        return this.request.url
    }
    get body(){
        return this.response.body
    }
    set body(val){
        this.response.body = val
    }
}
```

- request、response、context相互之间是怎样产生联系的

```
clas Application {
    constructor(){
        this.context = context;
        this.response = response;
        this.request = request;
    }
    use(callback){
        this.callback = callback
    }
    listen(...args) {
        const server = http.createServer(async (req,res) => {
            let ctx = this.createCtx(req, res);
            await this.callback(ctx)
            ctx.res.end(ctx.body)
        })
    }
    //response,request,context产生关联的处理
    createCtx(req,res){
        ctx = Object.create(this.context) // 拷贝
        ctx.response = Object.create(this.response)
        ctx.request = Object.create(this.request);
        // 将req同时挂载到ctx.req和crx.request.req上
        ctx.req = ctx.request.req = req
        // 同上
        ctx.res = ctx.response = res
        return ctx
    }
}

module.exports = Application

// app.js内容
const Xmoa = require('./application');
const app = new Xmoa();

app.use(async ctx => {
    ctx.body = 'Hello World'
})

app.listen(3002, () => {
    console.log('3002端口')
})
```
