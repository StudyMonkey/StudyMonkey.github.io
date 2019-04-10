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



