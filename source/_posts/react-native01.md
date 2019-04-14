---
title: 初识React-native（一）
date: 2019-04-13
tags:
  - IT技术
  - 前端
  - React-native
---

#### 1. 环境搭建
```
npm i -g yarn react-native-cli // 下载yarn 和 react-native命令行工具

```

- windows平台下载安装Android Studio，不翻墙[点此](https://developers.google.cn)。需配置Android环境变量，新建ANDROID_HOME,其值Android sdk安装位置
- mac平台下载xcode
- java 环境的配置，安装java sdk，并在环境变量中配置JAVA_HOME,其值为java sdk安装位置

#### 2. react-native初始化项目
```
react-native init projectname

例：
react-native init RNApp

```

#### 3. 运行初始化项目
```
// 进入目录
cd RNApp

// mac系统运行ios版本
react-native run-ios

// windows 系统运行Android版本
react-native run-android

// 运行命令需开启Android模拟器，或用Android Studio运行查看
```

#### 4. 目录介绍
- android 文件夹是编译好的Android文件
- ios 文件夹是编译好的Ios文件
- node_modules nodejs下载的依赖包文件
- index.js 入口文件
- App.js app组件文件

#### 5. App.js 解构解析
```
// 引入所需内容
import React, {Component} from 'react';
import {Platform, StyleSheet, Text, View} from 'react-native';

// 导出 app 类
type Props = {};
export default class App extends Component<Props> {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>Welcome to React Native!</Text>
        <Text style={styles.instructions}>To get started, edit App.js</Text>
        <Text style={styles.instructions}>{instructions}</Text>
      </View>
    );
  }
}

// 定义相关样式
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
    color: 'red'
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
})

```

#### 6. 注意事项
- flex布局：RN的横轴和纵轴和web端是相反的
- 注意在RN里面是不带单位，好让自己去自适应
- flex，RN中只接受一个属性，Web css可接受多个

