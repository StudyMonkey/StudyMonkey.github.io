---
title: React 生命周期的比较
date: 2019-04-23
tags:
  - IT技术
  - 前端
  - React
---

### 一、React的生命周期介绍
React的生命周期分为三种状态
- 1. 初始化
- 2. 更新
- 3. 销毁
![React生命周期图](https://images2015.cnblogs.com/blog/588767/201612/588767-20161205190022429-1074951616.jpg)

#### 初始化
1、 getDefaultProps()
##### 设置默认的props，也可以用dufaultProps设置组件的默认属性.

2、getInitialState()
#####  在使用es6的class语法时是没有这个钩子函数的，可以直接在constructor中定义this.state。此时可以访问this.props
```
constructor(props){
    super(props);
    this.state = {
        name: '小明' 
    }
}
```

3、componentWillMount()
##### 组件初始化时只调用，以后组件更新不调用，整个生命周期只调用一次，此时可以修改state

4、render()
##### react最重要的步骤，创建虚拟dom，进行diff算法，更新dom树都在此进行。此时就不能更改state了。
```
render(){
    return(
        <div>
            This is React component
        </div>
    )
}
```

5、componentDidMount()
##### 组件渲染之后调用，只调用一次。

### 更新
6、componentWillReceiveProps(nextProps)
##### 组件初始化时不调用，组件接受新的props时调用。

7、shouldComponentUpdate(nextProps, nextState)
##### react性能优化非常重要的一环。组件接受新的state或者props时调用，我们可以设置在此对比前后两个props和state是否相同，如果相同则返回false阻止更新，因为相同的属性状态一定会生成相同的dom树，这样就不需要创造新的dom树和旧的dom树进行diff算法对比，节省大量性能，尤其是在dom结构复杂的时候

8、componentWillUpdata(nextProps, nextState)
##### 组件初始化时不调用，只有在组件将要更新时才调用，此时可以修改state

9、render()
##### 组件渲染

10、componentDidUpdate()
##### 组件初始化时不调用，组件更新完成后调用，此时可以获取dom节点

### 卸载
11、componentWillUnmount()
##### 组件将要卸载时调用，一些事件监听和定时器需要在此时清除

### 值得注意的是：
- componentWillMount
- componentWillReceiveProps
- componentWillUpdata
这三个方法现在被标记为不安全的方法，未来可能会被移除。

### componentWillMount 和 componentDidMount的区别
- componentWillMount在render之前，componentDidMount在render之后，这意味着如果你需要在代码中拿到真实dom，则你需要用到componentDidMount。这时使用componentWillMount可能会产生一些麻烦
- 如果需要从远端发起http请求，建议也放在componentDidMount中进行操作
- 在componentDidMount中调用setState方法将会触发一次额外的渲染，但是它将在浏览器刷新屏幕之前发生。即使render调用两次，用户不会看到中间状态

### React中一些提高性能的写法
- 在constructor中为函数绑定this，要比在render中为函数绑定this性能要来
```
constructor(props){
    super(props)
    this.handleClick = this.handleClick.bind(this);
}

// 或者用箭头函数定义方法，性能也要优于render中

handleClick = () => {
    // do something
}

render(){
    return(
        <div>
            <button onClick="{ this.handleClick.bind(this) }">
        </div>
    )
}

```
- 利用shouldComponentUpdate做判断组件是否需要更新

