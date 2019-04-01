---
title: JS 设计模式
date: 2019-04-03
tags:
  - IT技术
  - 前端
  - Javascript
---

- 工厂模式

```

class Product{
    constructor(name) {
        this.name = name
    }

    init(){
        console.log(`This is ${this.name} init`)
    }

    fn1(){
        console.log('fn1')
    }

    fn2(){
        console.log('fn2')
    }
}

class Creator{
    create(name){
        return new Product(name)
    }
}

const creator = new Creator();
const p1 = creator.create('p1');
p1.init();  // This is p1 init
p1.fn1();
const p2 = creator.create('p2');
p2.init();  // This is p2 init
p2.fn1();


// 应用场景

// JQ中
class Jquery{
    ...许多代码
}

window.$ = function(selector){
    return new Jquery(selector)
}

// React的createElement方法

class Vnode(tar, attrs, children){
    ...许多代码
}

React.createElement = function(tag, attrs, children) {
    return Vnode(tag, attrs, children)
}
```

- 单例模式

- 适配器模式
#### 想象中国的插头和外国插头的区别，需要另外买一个转换头

```

class oldApi{
    change(){
        return '香港接口'
    }
}

class adaptation{
    constructor(){
        this.adaptate = new oldApi();
    }
    request(){
        let info = this.adaptate.change()
        console.log(`${info} -> 转换器 -> 中国接口`)
    }
}

const adapt = new adaptation();
adapt.request();


// 实例运用
// Vue computed

<div id="app"></div>

<script src="/vue.2.5.6.js"></script>
<script>
    const vue = new Vue({
        el: '#app',
        data: {
            message: 'Hello'
        },
        computed: {
            reverseMes: function(){
                return this.message.split('').reverse().join('');
            }
        }
    })
</script>

```

- 装饰器模式

```

@testDec
class Demo{
    // ...代码
}

function testDec(target){
    target.isDec = true
}

console.log(Demo.isDec)


```