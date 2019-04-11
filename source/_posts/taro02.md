---
title: Taro练习踩坑记录(一)
date: 2019-04-08
tags:
  - IT技术
  - 前端
  - Taro
  - 小程序
---

#### 1. 部分语法编译不兼容报错
```
// 利用taro-ui实现一个带搜索框的导航栏，UI自带的没有，所以利用AtNavBar的title属性JSX语法写上AtSearchBar
// search.js 代码如下

import Taro, { COmponent } from '@tarojs/taro'
import { View } from '@tarojs/components'
import { AtNavBar,AtSearchBar } from 'taro-ui'

export default class Search extends Component{
    render(){
        return(
            <View>
                <AtNavBar
                    onClickLeftIcon={this.handleClick}
                    color='#000'
                    title={ 
                        <AtSearchBar 
                            value={ value } 
                            onChange={ this.handleChange } 
                            onActionClick={this.onActionClick} 
                        /> 
                    }
                    leftIconType='chevron-left'
                />
            </View> 
        )
    }
}

// 这样编译的h5没问题，微信小程序报错

暂时想到的解决办法是：AtSearchBar另外添加一个AtIcon返回箭头的样式

```

#### 2. 做项目的过程中调取Cnode.org的Api，有部分取到的内容是html内容，不知道React怎样渲染，查找所得
```
import Taro,{ Component } from '@tarojs/taro'
import { View } from '@tarojs/component'

export default class Detail extends Component{

    state = {
        detail: ''
    }

    componentWillMount(){
        const id = this.$router.params.id;
        console.log(id);
        getNode('topic/'+id).then( res => {
            console.log(res);
            this.setState({
                detail: res.data.data
            })
        })
    }

    render(){
        return(
            <View>
                <View className='acticle_h3'>{ detail.title }</View>
                // dangerouslySetInnerHTML={{ __html: detail.content}}
                <View dangerouslySetInnerHTML={{ __html: detail.content}}>
                </View>
            </View>
        )
    }
}

```

#### 3. Taro中使用mobx
```
// 根据Taro官网示例

// 下载依赖包
yarn add mobx@4.8.0 @tarojs/mobx @tarojs/mobx-h @tarojs/mobx-rn
/ cnpm i / npm i 

// src目录下新建store/counter.js

import { observable  } from '@tarojs/mobx'

const counterStore = observable ({
    counter: 0,
    counterStore() {
        this.counter++
    },
    increment() {
        this.counter++
    },
    decrement() {
        this.counter++
    },
    incrementAsync() {
        setTimeout( () => {
            this.counter++
        }, 1000)
    }
})

export default counterStore;

// src/app.js

import { Provider } from '@tarojs/mobx'
import counterStore from './store/counter'

const store = {
    counterStore
}

class App extends Component {
    render(){
        return(
            <Provider store={ store }>
                <Index />
            </Provider>
        )
    }
}

Taro.render(<App />, document.getElementById('app'));


```

#### 4. Taro中使用async/await 语法
```
// app.js
import '@tarojs/async-await'

// index.js
export default class Index extends Component{
    
    async componentDidMount(){
        const res = await getApi('topics', { page: 1 })
        console.log(res)
    }

    render(){
        return(
            <View>
                This is index page
            </View>
        )
    }
}
```

#### 5. Taro中H5和微信小程序要做特殊处理的地方，需用到判断process.env.TARO_ENV === 'h5'
```
// 像在处理触底刷新这类的小程序独有的事件时

onReachBottom(){
    // 请求新数据，合并之前数据，渲染
}

// h5 不支持这类事件，需判断环境另外处理

// 暂行办法：加入按钮的点击事件去加载新数据

state = {
    isH5: process.env.TARO_ENV === 'h5'
}

handleH5Click = () => {
    // 请求新数据，合并之前数据，渲染
}

render(){
    return(
        <View>
            { isH5 ? <AtButton onClick={ this.handleH5Click }>点击</AtButton> : null }
        </View>
    )
}
```

#### 6. 编码过程中遇到了mobx在H5报错，小程序运行正常的问题

##### 开始把官网的加减示例放在登录部分的js操作，{ counter }报错counter找不到，编译到小程序的时候运行正常在h5的render部分打印this.props为空对象，仔细查看代码发现

#### 7. Taro中使用mobx，似乎不能在React子组件中使用mobx，需要在父组件引用mobx，然后由子组件将需要操作的值传递过来
```
// study.js

import { inject, observer } from '@tarojs/mobx'
import StudyList from '../../components/studyList'

@inject('counterViewList')
@observer
export default class Study extends Component{

    state = {
        nodeList: []
    }

    constructor(props){
        super(props);
    }

    pushViewList = (id) => {
        const { counterViewList } = this.props;
        const { nodeList } = this.state;
        const list = nodeList.filter( v => v.id === id );
        counterViewList.addViewList.push(list)
    }

    render(){
        const { nodeList } = this.state
        return(
            <View>
                <StudyList onhandlePushList={ this.pushViewList } list={ nodeList } />
            </View>
        )
    }
}

// studyList.js

export default class StudyList extends Component{
    constructor(props){
        super(props)
    }

    handleItemClick = (id) => {
        const { onhandlePushView } = this.props;
        onhandlePushView(id);
        Taro.navigateTo({
            url: '/pages/detail/detail?id='+id
        })
    }
    
    render(){
        return(
            <View>
                <AtList>
                    {
                        list.map ( item => {
                            return(
                                <AtListItem
                                    onClick={ () => this.handleItemClick(item.id) }
                                    key={ item.id }
                                    title={ item.title }
                                    extraText={ computedTime(item.create_at) }
                                    thumb={ item.author.avatar_url }
                                />
                            )
                        })
                        
                    }
                </AtList>                
            </View>
        )
    }
}

```




