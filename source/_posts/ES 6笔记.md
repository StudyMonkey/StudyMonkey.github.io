---
title: ES 6 笔记内容
date: 2019-04-02
tags:
  - IT技术
  - 前端
  - Javascript
---

### 1.变量

```
// let 和 const 不可重复申明
let a = 12 
let a = 5
alert(a) // 报错，const 同理

// 限制修改值

let a = 12
a = 5
alert(a) // 5

const b = 12
b = 5
alert(b) // 报错， const定义的值不能修改

// 块级作用域
<input type="button" value="按钮1"> 
<input type="button" value="按钮1"> 
<input type="button" value="按钮1">

const aBtn = document.getElementsByTagName('input');
window.onload = function (){
    for( var i = 0; i<aBtn.length; i++) {
        aBtn[i].onclick = function(){
            alert(i); // 每次都是3
        }
    }
    
    for( let i = 0; i<aBtn.length; i++) {
        aBtn[i].onclick = function(){
            alert(i); // 0 1 2
        }
    }
}

```

### 2. 箭头函数

```
let show = function () {
    
}

let show = () => {
    
}

// 只有一个参数的时候，()可省略

let show = a => {
    return a*2
}

// 只有return的时候，{}可省略

let show = a => a*2
 
```

### 3. 函数的参数
- 收集参数
==这样用的位置必须在最后一个==

```
function show (a, b, ...args) {
    alert(a);
    alert(b);
    alert(args);
}

show(5, 12, 20, 35, 38, 42)
```

- 扩展参数

```
// 示例1
function show(...arg) {
    alert(a);
}
show(1,2,3);

// 示例2
const arr1 = [1,2,3]
const arr2 = [5,6,7]

let arr = [...arr1,...arr2]

alert(arr) // [1,2,3,5,6,7]

// 示例3
function show (...arg){
    fn(arg)
}

function (a, b) {
    return a+b
}

show(12,5) // 17
```

- 默认参数

```
function show(a,b=5,c=12) {
    console.log(a,b,c)
}

show(18) // 18,5,12

show(18,20) // 18,20

show(18,20,30) // 18,20,30
```




### 4. 数组方法
map reduce filter forEach

```
let arr = [5, 8 , 19, 34]

const result = arr.map( item => item*2 );

console.log(result) // 10, 16, 38, 68

let arr1 = [16, 80, 200, 5042];

// reduce 进去一堆，出来一个总数
arr1.reduce( (temp,item,index) => { 
    // temp是计算加法之后的中间值
    // item 从数组第二个数开始的每个值
    // index 从1开始的下标
    return temp + item   
})

// filter 过滤，根据条件产生一个新数组
let arr2 = [400, 600, 1000, 2000, 3500]

console.log(arr2.filter( v => v >= 2000 )) // [2000, 3500]

// forEach 循环（迭代）


```


### 5. 字符串
==多了两个方法==  startsWith 和 endsWith

```
 const str = 'http://www.baidu.com'
 
 if ( str.startsWith('http') ) {
     alert('普通网址')
 } else if ( str.startsWith('https') ){
     alert('加密网址')
 } else {
    alert('其他') 
 }
 
 const st = '1.txt';
 
 if (st.endsWith('txt')) {
     alert('文本文件')
 }else if ( st.endsWith('jpg') ) {
     alert('jpg图片')
 }else {
     alert('其他')
 }
```

==字符串模板==  反单引号  可换行

```
const str = '测试';
`<div>这是一个${str}标签</div> `
```

### 6.面向对象

```
// 老的写法

function User (name, pass) {
    this.name = name;
    this.pass = pass
}

User.prototype.showName = function() {
    alert(this.name)
}

User.prototype.showPass = function(){
    alert(this.pass)
}

// 老的继承写法

function VipUser(name, pass, level) {
    User.call(this, name, pass);
    
    this.level = level
}

VipUser.prototype = new User();
VipUser.prototype.constructor = VipUser;
VipUser.prototype.showLevel = function(){
    alert(this.level)
}

var ul2 = new VipUser('xiaoMao','123456', '99');
ul.showName();
ul.showPass();
ul2.showLevel();



// 新的写法
class User {
    constructor(name, pass) {
        this.name = name;
        this.pass = pass
    }
    
    showName(){
        alert(this.name)
    }
    
    showPass(){
        alert(this.pass)
    }
}

var ul = new User();
ul.showName();
ul.showPass();

// 新的继承写法
class VIpUser extends User{
    constructor(name, pass, level){
        super(name,pass)
        this.level = level
    }
    
    showLevel(){
        this.level = level
    }
}

var ul = new VIpUser('xiaoMao','123456','99');
ul.showLevel()
```

### 7. json

```
JSON.stringify()   // 字符串化
JSON.parse() // json 化

// key 和 value值相同，可以只写一个
示例
this.setState({
    selectedKeys: selectedKeys
    // 可写成
    selectedKeys
})

// JSON里面的函数可以省略 : function

class User {
    constructor () {
        
    }
    
    showName() {
        
    }
}
```

### 8. Promise

```
异步： 操作之间没啥关系，同时进行多个操作
    $.ajax({
        url: '/api/getUser',
        datatype: 'json',
        success: function(){
            $.ajax({
                url: '/api/getUser',
                datatype: 'json',
                success: function(){ 
                    $.ajax({
                        url: '/api/getUser',
                        datatype: 'json',
                        success: function(){ 
                            
                        }                
                    })                    
                }                
            })
        },
        error: function(err){
            console.log(err)
        }
    })
    
同步： 操作之间有关系，同时只能进行一个操作
    let user_data = ajax_async('/api/getUser');
    let banner_data = ajax_async('/api/banners');
    let menu_data = ajax_async('/api/menus');
    let item_data = ajax_async('/api/item');
    
Promise -------消除异步操作
    *用同步一样的方式，来书写异步代码

    var p1 = new Promise( function(resolve,reject){
        $.ajax('data/1.txt', function(arr){
            resolve(arr)
        }, function(err){
            reject(err)
        })   
    }) 

    var p1 = new Promise( function(resolve,reject){
        $.ajax('data/json.txt', function(arr){
            resolve(arr)
        }, function(err){
            reject(err)
        })   
    })     
    
/*    p1.then(function(arr){
        alert('成功'+arr)
    },function(err){
        alert(err)
    }) */
    
    Promise.all([
        p1,p2
    ]).then(function(arr){
        let [a1, a2] = arr
        alert('全都成功了');
    },function(){
        alert('至少有一个失败了');
    })
    
所以写成通用的：
    function commonPromise(url){
        return new Promise(function(resolve,reject){ // 这里不能写成箭头函数
            $.ajax({
                url,
                success: function(arr){
                    resolve(arr);
                },
                error: function(err){
                    reject(err)
                }
            })
        })
    }
    
    Promise.all([
        commonPromise('data/1.txt'),
        commonPromise('data/json.txt')
    ]).then( (arr) => {
        let [p1, p2] = arr;
        alert('全都成功了')
    }, () => {
        alert('至少有一个失败了')
    })
```

==高版本jQuery已经自己封装了Promise==
直接：
```
    Promise.all([
        $.ajax({url:'data/1.txt',dataType: 'json'}),
        $.ajax({url:'data/json.txt',dataType: 'json'})
    ]).then( () => {
        alert('成功')
    }, () => {
        alert('失败')
    })
```
Promise.race 看谁先返回

```
Promise.race([
    $.ajax({url:'data/1.txt'}),
    $.ajax({url:'data/2.txt'}),
    $.ajax({url:'data/3.txt'}),
    $.ajax({url:'data/4.txt'}),
    $.ajax({url:'data/5.txt'}),
]).then( ()=>{
    // 由上面加载最快的，得到返回值
}, ()=> {
    
} )
```

### 9. generator( * 和 yield )

- 普通函数   想象成飞机高铁，一路执行到底
- generator  想象成出租车，想停就能停，停的位置看yield

```
function *show(num){
    alert('a');
    let a = yield num;
    alert('b')
    alert(a);
}

var p = show();
// p.next() // a
// p.next() // b

p.next(12); // a
p.next(5);  // b 5

// Promose 里面如果带逻辑判断执行的话，代码更复杂
Promise.all([
    $.ajax({url: '/getUser', dataType: 'json'})
]).then( result => {
    let res = result[0];
    if ( res.VIP ) {
        Promise.all([
            $.ajax({url: '/VIPdata', dataType: 'json'})
        ]).then( () => {

        }, () =>{
            
        })
    }else {
        Promise.all([
            $.ajax({url: '/itemdata', dataType: 'json'})
        ]).then( () => {

        }, () =>{
            
        })        
    }
}, err => {
    console.log(err)
})

// yield 带逻辑

runner(function *show(){
    let user_data = yield $.ajax({url: '/getUser', dataType: 'json'});
    if ( user_data.VIP ) {
        let data = yield $.ajax({url: '/VIPdata', dataType: 'json'});
    }else {
        let data = yield $.ajax({url: '/itemdata', dataType: 'json'});
    }
})
```

总结：
    Promise        一次读一堆
    generator      一次读一堆，可以带逻辑  

### 10. for of (ES 7 语法)

### 11. async 和 await

```
 let renderData = async () => {
    const data =  await $.ajax({url:'data/1.txt', dataType: 'json'})
    
    console.log(data)
 }
 
 renderData()
```


