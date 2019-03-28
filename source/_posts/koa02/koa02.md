---
title: koa源码解析(二)一中间件机制
date: 2019-03-30
tags:
  - IT技术
  - 前端
  - Nodejs
  - koa
---

## koa源码
- 多个函数执行

```
function add(a, b){
    return a+b
}

function double(x){
    return x*2
}

const res1 = add(1,2);
const res2 = double(res1);
console.log(res2); // 6

const res3 = double(add(1,2))
console.log(res3) // 6

```

- 函数实现compose

```
const middles = [add, double];
const len = middles.length;
function compose(midds){
    // 返回一个函数
    return (...args) => {
        let res = midds[0](...args);
        for( let i = 1; i < len; i++ ) {
            res = midds[i](res)
        }
        return res
    }
}

const fn = compose(middles);
const fn1 = fn(1,2);
console.log(fn1)

```

- 异步的compose函数实现

```

async function fn1(next){
    console.log('fn1')
    await next();
    console.log('end fn1')
}

async function fn2(next){
    console.log('fn2');
    await delay();
    await next();
    console.log('end fn2');
}

function fn3(next){
    console.log('fn3')
}

function delay(){
    return new Promise( (resolve, reject) => {
        setTimeout( () => {
            resolve()      
        }, 2000)         
    })
}

function compose(middlewares){
    // 由于不能直接循环，调用next执行下一个中间件
    return function(){
        return dispatch(0);
        // 用dispatch来控制所有逻辑
        function dispatch(i){
            let fn = middlewares[i];
            if ( !fn ) {// fn不存在，则i可能大于middlewares的长度
                return Promise.resolve()
            }

            return Promise.resolve(fn(function next(){
                return dispatch(i+1)
            }))
        }
    }
}

const middlewares = [fn1, fn2, fn3];
const finalFn = compose(middlewares);

finalFn(); // fn1 fn2 fn3 end fn2 end fn1

```
- 中间件机制（洋葱圈原理）

```

class Application {
    constructor(){
        this.context = context
        this.response = response
        this.request = request
        this.middlewares = []
    }

    use(callback) {
        this.middlewares.push(callback);
        // this.callback = callback
    }

    compose(middlewares){
        // 由于不能直接循环，调用next执行下一个中间件
        return function(context){
            return dispatch(0);
            // 用dispatch来控制所有逻辑
            function dispatch(i){
                let fn = middlewares[i];
                if ( !fn ) {// fn不存在，则i可能大于middlewares的长度
                    return Promise.resolve()
                }

                return Promise.resolve(fn(context, function next(){
                    // 手动执行next，才可以执行下一个中间价
                    return dispatch(i+1)
                }))
            }
        }
    }  

    listen(...args){
        const server = http.createServer( (req,res) => {
            let ctx = this.createContext(req,res);
            const fn = this.compose(this.middlewares);
            await this.fn(ctx)
            // await this.callback(ctx);
            ctx.res.end(ctx.body)
        })
        server.listen(...args)
    }  

    createContext(req,res) {
        const ctx = Object.create(this.context);
        ctx.response = Object.cretae(this.response);
        ctx.request = Object.create(this.request);
        ctx.res = ctx.response.res = res;
        ctx.req = ctx.request.req = req
        return ctx;
    }
}

```

