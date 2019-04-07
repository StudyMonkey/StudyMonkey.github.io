---
title: 初识Taro
date: 2019-04-05
tags:
  - IT技术
  - 前端
  - Taro
  - 小程序
---

#### 1. 顶部引入稍微有区别
```

// Taro
import Taro, { Component } from '@tarojs/taro'
import { View, Text } from '@tarojs/components'

export default class Index extends Component {
    constructor(props){
        super(props)
    }
    
    render(){
        return (
            <div> Hello World! </div>
        )
    }
}

// React 
import React,{ Component } from 'react'

export default class Index extends Component {
    constructor(props){
        super(props)
    }
    
    render(){
        return (
            <div> Hello World! </div>
        )
    }
}

```

#### 2. 父子组件传值基本相同（props）
```

// 父组件
import Taro, { Component } from '@tarojs/taro'
import { View, Text } from '@tarojs/components'

export default class Index extends Component {
    constructor(){
        super();
        this.state = {
            name: 'testProp'
        }
    }
    
    test = () => {
        console.log('父组件传递方法')
    }
    
    render(){
        return(
            <View>
                <Text> Hello World! </Text>
                // 这里写成ontest是为了兼容微信小程序的事件
                <Child ontest={ this.test }  name={ this.state.name } />
            </View>
        )
    }
}

// 子组件
// 头部引入与上面相同，暂省略
export default class Child extends Component {

    cl = () => {
        this.props.ontest();
    }

    render(){
        const { name } = this.props
        return(
            <View>
                <Text onClick={ this.cl }>This is Child component, { name } </Text>
            </View>
        )
    }
}


```

#### 3. 路由
- 路由的定义、路由传值和获取
```
// React 
// 需要依赖包react-router-dom
import { Router, } from 'react-router-dom'
<>
    <Router path="/" component={ Home } />
    <Router path="/about" component={ About } /> 
</>

// Taro
// app.js里面可直接配置
config = {
    pages: [
        '/pages/index/index', // 写在第一个的为首页
        '/pages/about/about'
    ]
}

// index.js
import Taro,{ Component } from '@tarojs/taro'
import { View, Button } from '@tarojs/components'
export default class Index extends Component{

    router = () => {
        Taro.navigateTo({
            // 跳转about页面
            // url: '/pages/about/about'
            
            // 跳转页面并传值
            url: '/pages/about/about?name="xmtest"'
        })
    }
    
    render(){
        return(
            <View>
                <Button onClick={ this.router }>跳转</Button>
            
            </View>
        )
    }
}

// about.js 
// 接收index.js路由跳转传递过来的参数
import Taro, { Component } from '@tarojs/taro'

export default class About extends Component{

    componentDidMount(){
        console.log(this.$router.params.name) // xmtest
    }
    
}

```
- navigateTo和redirectTo的考虑使用（最多嵌套5层）

#### 4. 资源的引用
- JS的有关导出的变量和方法引用基本相同
```
// utils.js

export function fn1(){}
export function fn2(){}

// about.js 
import { fn1, fn2 } from '../utils'

```
- Image的两种引入方法
```
import Taro,{ Component } from '@tarojs/taro'
import { View, Text, Image } from '@tarojs/components'
// import 方式
import imgSrc from '../../img/1.png'

export default class About extends Component{
    render(){
        
        return(
            // require 方式
            <img src={ require('../../img/1.png') } alt="测试图片" />
            
            // import 方式
            <img src={ imgSrc } alt="测试图片" />
        )
    }
}

```
- Css的引用（注意：像素的大写和小写的区别）
```
import './about.css'

// 值得注意的是，Taro中的小写px会转换成rem单位，大写PX在页面上才会显示成px
```

#### 5. 条件渲染
```
// Taro中建议更好的用三元运算符

// 短路条件
// about.js
export default class About extends Component {

    state = {
        test: true
    }
    
    render(){
        return(
            { !true || <Button>测试</Button> }
        )
    }
}

// 三元运算符
state ={
    test: true    
}

render(){
    return(
        { this.state.test ? <Button>测试</Button> : null /> 
    )
}

```
- 注意Taro中，render之外不允许定义JSX（小程序编译导致的限制）
```
export default class About extends Component{
    getDom = () => {
        return <Button>测试</Button>
    }
    
    render(){
        return(
            // 这样在小程序编译是不可行的
            { this.getDom() } 
        )
    }
}
```

#### 6.列表循环展示
```
import Taro, { Component } from '@tarojs/taro'
import { View,Text } from '@tarojs/components' 
export default class List extends Component{
    // 这样
    constructor(){
        super();
        this.state = {
            list: [
                { id:1, name: 'test1' },
                { id:2, name: 'test2' },
                { id:3, name: 'test3' },
                { id:4, name: 'test4' }
            ]            
        }
    }
    
    // 或者这样
    state = {
        list: [
            { id:1, name: 'test1' },
            { id:2, name: 'test2' },
            { id:3, name: 'test3' },
            { id:4, name: 'test4' }
        ]
    }
    render(){
        return(
            <View>
                {
                    this.state.list.map( (item,index) => {
                        <View>
                            <Text>{ item.name }</Text>
                        </View>
                    })
                }
            </View>
        )
    }
}

```
- 注意Taro中不能处理map循环渲染之后的模板，得先处理数组，然后再让数组去渲染模板
```
// 借用上面代码
{
    this.state.list.map( (item,index) => {
        <View>
            <Text>{ item.name }</Text>
        </View>
        // 这样继续操作在Taro中是错误的
    }).filter()
}

const { list } = this.state
// 先处理数组数据
list = list.filter( v => v.name !== 'xm4');
// 再渲染
{
    this.state.list.map( (item,index) => {
        <View>
            <Text>{ item.name }</Text>
        </View>
    })
}
```

#### 7. 子组件内容展示由父组件传值（this.props.children,相当于Vue的slot）
- 注意Taro中this.props.children是只读的，不能做任何修改
```
// 父组件
import Child from './child'
export default class Index extends Component{
    render(){
        return(
            <Child>
                <Button>父组件传递的btn</Button>
            </Child>
        )
    }
}

// 子组件
export default class Child extends Component{
    render(){
        <View>
            { this.props.children } 
        </View>
    }
}

```
- 注意怎样区分head和body的内容
```
// 父组件
render(){
    return(
        <Child childHead={ <Button>父组件传递的Head Btn</Button> }>
            <Button>父组件传递的Btn</Button>
        </Child>    
    )
}


// 子组件
render(){
    return(
        <View>
            // 属性里面传递的
            { this.props.childHead }
            
            // 标签内容传递的
            { this.props.children }
        </View>
    )
}


```

#### 8. 事件处理
- 注意事件不能使用直接在方括号箭头函数执行的形式，编译成小程序报错，得在上面定义函数的形式
```
import Taro, { Component } from '@tarojs/taro'
import { View } from '@tarojs/components'

export default class Index extends Component{
    btnClick = () => {
        console.log('点击')
    }
    
    render(){
        return(
            // 这是正确的
            <Button onClick={ this.btnClick }>测试</Button>
            
            // 这是不可行的
            <Button onClick={ () => console.log('点击') }>测试</Button>
            
            // 子组件
            // 传递时建议加上on好兼容小程序
            <Child ontest={ this.btnClick }></Child>
        )
    }
}

```

#### 9. 样式需要类选择器，其他编译有问题 
```
// 正确
.index{
    color: red
}

// 正确
.index{
    .test{
        color: #ffffff
    }
}

// 错误
#id{
    color: red
}

// 错误
span[class="test"]{
    color: red
}

// 错误
div > span{
    color: red
}

```

#### 10. 其他问题
- Taro中input的onChange事件得写onInput
```
export default class Dialog extends Component{

    handleInput = (e) => {
        this.state = {
            inputVal: e.target.value
        }
    }
    
    render(){
        return(
            <Input placeholder='请输入问题标题' onInput= { this.handleInput } />
        )
    }   
}


```






