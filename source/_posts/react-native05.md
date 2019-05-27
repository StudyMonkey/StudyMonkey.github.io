---
title: 初识React-native(五)
date: 2019-05-11
tags:
  - IT技术
  - 前端
  - React-native
---

#### 底部 tabBar 的实现
```
// HomePage.js

import React, { Component } from 'react'
import { View, Text } from 'react-native'
// react-native-vector-icons 下载之后，还需要与react-native link关联一下
import FontAwesome from 'react-native-vector-icons/FontAwesome'
import IndexPage from './IndexPage'
import CollectPage from './CollectPage'
import MyPage from './MyPage'

const bottomTab = {
    IndexPage: {
        screen: IndexPage,
        navigationOptions: {
            tabBarLabel: '首页',
            tabBarIcon: ({tintColor, focused}) => (
                <FontAwesome 
                    name={"home"}
                    size={26}
                    style={{color: tintColor}}
                />
            )
        }
    },
    CollectPage: {
        screen: CollecPage,
        navigationOptions: {
            tabBarLabel: '收藏',
            tabBarIcon: ({tintColor, focused}) => (
                <FontAwesome 
                    name={'heart'}
                    size={26}
                    style={{color: tintColor}}
                />            
            )
        }
    },
    MyPage: {
        screen: MyPage,
        navigationOptions: {
            tabBarLabel: '我的',
            tabBarIcon: ({tintColor, focused}) => (
                <FontAwesome 
                    name={'user'}
                    size={26}
                    style={{color: tintColor}}
                />
            )
        }
    }
}

export default class HomePage extends Component{

    _genBottomTabs = () => {
        const { IndexPage, MyPage } = bottomTab;
        const tabs = { IndexPage, MyPage };
        return createAppContainer(
            createBottomNavigator(tabs, {
                tabBarOptions: {
                    
                }
            })
        )        
    }

    render(){
        const Tabs = this._genBottomTabs();
        return <Tabs />
    }
}
```

#### 头部 tabBar 的实现
```
// IndexPage.js 
import React, { Component } from 'react'
import { View, Text } fom 'react-native'

export default class IndexPage extends Component{

    constructor(props){
        super(props);
        this.topTabs = ['Ios', 'React', 'Vue', 'NodeJs', 'React-Native', 'Angular']
    }
    
    _genTopTabs = () => {
        const Tabs = {};
        this.topTabs.forEach( (item, index) => {
            Tabs[`Tab${index}`] = {
                // screen: IndexTab,
                // 动态显示 screen 内容
                screen: props => <IndexTabs {...props} tabName={ item } />,
                navigationOptions: {
                    title: item
                }
            }
        })
    }

    render(){
        const TopTabs = this._genTopTabs();
        return <TopTabs />
    }
}

export class IndexTabs extends Component{
    render(){
        return (
            <View>
                <Text>{ this.props.tabName }</Text>
            </View>
        )
    }
}
```

#### 内层 navigator 跳转外层 navigator 页面失效解决
```
// AppNavigator.js
// react-navigation 也需要与 react-native link 关联一下
import {
    createAppContainer,
    createStackNavigator
} from 'react-navigation'

const MainNavigation = createStackNavigator(
    HomePage: {
        screen: HomePage,
        navigationOptions: {
            headerTitle: null
        }
    },
    DetailPage: {
        screen: DetailPage,
        navigationOptions: {
            headetTitle: null
        }
    }
)

export default createAppContainer(MainNavigation);

// IndexPage.js

export default class IndexPage extends Component{
    render(){
        // 同级 navigator 跳转时，从 props 里面取值
        const { navigation } = this.props;
        return (
            <Button 
                title="go Detail"
                onPress={ () => {
                    // 这里的写法是没办法跳转的，因为IndexPage是HomePage的内层，无法跳转到外层的 navigator
                    navigation.navigate("DetailPage")      
                }}
            />
        )
    }
}

/* 正确写法 */

// HomePage.js
import NavigationUtil from '../navigation/NavigationUtil'

export default class HomePage extends Component{
    render(){
        NavigationUtil.navigation = this.props;
        return()
    }
}

// NavigationUtil.js

export default class NavigationUtil{
    static goToPage(page){
        const { navigation } = NavigationUtil.navigation;
        navigation.navigate(page);
    }
}

// IndexPage.js
import NavigationUtil from '../navigation/NavigationUtil'

export default class IndexPage extends Component{
    render(){
        return (
            <Button 
                title="go detail"
                onPress={ () => {
                    NavigationUtil.goToPage("DetailPage")        
                }}
            />
        )
    }
}
```