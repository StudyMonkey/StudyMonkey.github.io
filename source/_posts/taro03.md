---
title: Taro练习踩坑记录(二)——使用mobx
date: 2019-04-10
tags:
  - IT技术
  - 前端
  - Taro
  - 小程序
---

#### 1. Taro中使用mobx
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

#### 2. 编码过程中遇到了mobx在H5报错，小程序运行正常的问题

##### 开始把官网的加减示例放在登录部分的js操作，{ counter }报错counter找不到，编译到小程序的时候运行正常在h5的render部分打印this.props为空对象，仔细查看代码发现

#### 3. Taro中使用mobx，似乎不能在React子组件中使用mobx，需要在父组件引用mobx，然后由子组件将需要操作的值传递过来
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

#### 4. React组件中inject两个数据
```
// counterView.js   counterCollect.js
import counterView from '../../store/view'
import counterCollect from '../../store/collect'
import { Provider } from '@tarojs/mobx'

const store = {
  counterViewList,
  counterCollect
}

render(){
    return(
        <Provider store={ store }>
            <Index />
        </Provider>        
    )
}

Taro.render(<App />, document.getElementById('app'))


// detail.js

import { observer,inject } from '@tarojs/mobx'

@inject('counterView')
@inject('counterCollect')
@observer
export default class Detail extends Component{

    constructor(props){
        super(props)
    }

    render(){
        return(
            <View>
                // mobx等相关操作
            </View>
        )
    }
}
```