---
title: 初识React-native（四）
date: 2019-05-08
tags:
  - IT技术
  - 前端
  - React-native
---

#### flex布局
```
import React, { Component } from 'react'
import { View, Text, StyleSheet } from 'react-native'

export default class App extends Component{
    render(){
        return (
            <View style={ styles.appBox }>
                <Text>this is app page</Text>
            </View>
        )
    }
}

const styles = StyleSheet.create({
    appBox: {
        fontSize: 30,
        color: "red",
        alignItems: "center",
        flexDirection: "column"
    }
})
```

#### react-navigation 和 react-native-gesture-handler
> 引用react-navigation时，如果未下载react-native-gesture-handler则会报错。
> react-native-gesture-handler 是 RN 处理用户手势的
> 最后还需将react-native 与 react-native-gesture-handler link 关联一下
```
react-native link react-native-gesture-handler
```

#### 基础路由实现及路由传值且获取
```
// 新建 appNavigator.js

import {
    createStackNavigator,
    createAppContainer
} from 'react-navigation'

import HomePage from '../page/HomePage'
import DetailPage from '../page/DetailPage'
import Register from '../page/Register'

const MainNavigation = createStackNavigation({
    HomePage: {
        screen: HomePage,
        navigationOptions: {
            headerTitle: '首页'
        }
    },
    DetailPage: {
        screen: DetailPage,
        navigationOptions: props => {
            const { navigation } = this.props;
            return (
                headerTitle: navigation.state.params.name
            )
        }
    },
    Register: {
        screen: Register,
        navigationOptions: {
            headerTitle: '注册'
        }
    }
})

export default createAppContainer(MainNavigation);

// HomePage.js

export default class HomePage extends Component{
    const { navigation } = this.props;
    render(){
        return (
            <View>
                <Text>This is home page</Text>
                <Button 
                    title="go to detail"
                    onPress={ () => {
                        navigation.navigate("DetailPage", {
                            name: 'test'
                        })   
                    }}
                />
            </View>
        )
    }
}
```

#### 首屏跳转主页的实现——代码复现
```
import {
    createAppContainer,
    createSwitchTabNavigation
} from 'react-navigation'

import WelcomePage from '../page/WelcomePage'
import HomePage from '../page/HomePage'

const InitNavigation = createStackNavigation({
    WelcomePage: {
        screen: WelcomePage,
        navigationOptions: {
            headerTitle: null
        }
    }
})

const MainNavigation = createStackNavigation({
    HomePage: {
        screen: HomePage,
        navigationOptions: {
            headerTitle: '首页'
        }
    },
    DetailPage: {
        screen: DetailPage,
        navigationOptions: {
            headerTitle: '详情'
        }
    }
})

export default createAppContainer(
    createSwitchTabNavigation({
        Init: InitNavigation,
        Main: MainNavigation
    })
)

// WelcomePage.js
import React,{ Component } from 'react';
import { View, Text, StyleSheet } from 'react-native';

export default class WelcomePage extends Component{

    constructor(props){
        super(props)
    }

    componentDidMount(){
        this.timer = setTimeout( () => {
            const { navigation } = this.props;
            navigation.navigate('Main');
        })
    }
    
    componentWillUnmount(){
        this.timer && clearTimeout(this.timer)
    }

    render(){
        return (
            <View style={ styles.welcomeWrap }>
                <Text>Welcome to RN</Text>
            </View>
        )
    }
}

const styles = StyleSheet.create({
    welcomeWrap: {
        height: '100%',
        alignItems: 'center',
        justifyContent: 'center',
        color: 'red',
        fontSize: 30,
    }
})

```