---
title: 初识React-native
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

- windows平台下载安装Android Studio，不翻墙[点此](developer.google.cn)。需配置Android环境变量，新建ANDROID_HOME,其值Android sdk安装位置
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