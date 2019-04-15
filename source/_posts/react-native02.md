---
title: 初识React-native（二）
date: 2019-04-15
tags:
  - IT技术
  - 前端
  - React-native
---

#### 1. RN 中样式的注意事项及写样式的三种方式
- RN 中除了有flex布局还有绝对定位布局
- RN 中新建项目时自带的样式
```
export default class App extends Component{
  render(){
    return(
      <View style={style.container}>
        <!-- 第二种数组式 -->
        <View style={[style.welcome,{backgroundColor: '#000'}]}></View>

        <!-- 第三种对象式 -->
        <View style={{backgroundColor: '#f00'}}></View>
      </View>
    )
  }
}

const style = StyleSheet.create({
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
  }  
})

```

#### 2. RN 调试技巧
- Android 端 Ctrl + m 调出控制菜单
- 点击第一个菜单重新载入，相当于刷新
- 点击第二个菜单跳转到谷歌页面可以进行JS的调试，在resource里面的debuggerWorker.js里面的8081可查看项目源码进行断点调试或者在需要断点的地方加上一个debugger
- 点击第三个选项可实现热更新

#### 3. 手动禁止黄色警告
```
export default class App extends Component{
  constructor(props){
    super(props)
    console.disableYellowBox = true;
  }
}

```

#### 4. 部分组件介绍
```
import { Button,ActivityIndicator,Image } from 'react-native'

export default class App extends Component{
  render(){
    return(
      <View>
        <!-- button 按钮点击事件 onpress -->
        <Button title='测试按钮' onPress={ () => alert('测试') } />

        <!-- 加载中图标 animating 显示隐藏 -->
        <ActivityIndicator size='large' animating={ false } hidesWhenStopped={ false } />

        <!-- Image 不是src而是source 不是url而是uri 加载本地图片不需要设置宽高，网络图和base 64需要设置宽高 -->
        <Image 
          source={require('./images/1.png')}
          或
          source={{ uri: 'https://somewhere/1.png }} style={{width:64, height: 50}}
        />
      </View>
    )
  }
}
```

#### 注意事项
默认情况下，Android是不支持GIF和webP格式的，你需要在Android/app/build.gradle文件中根据需要手动添加一下模块：
```
dependencies: {
  // 如果你需要支持 GIF 动图
  compile `com.facebook.fresco:animated-gif:1.10.0`
  // 如果你需要支持 webP 格式，包括 webP 动图
  compile `com.facebook.fresco:animated-webp:1.10.0`
  compile `com.facebook.fresco:webpsupport:1.10.0`

   // 如果你只需要支持 webP 格式 而不需要动图
  compile `com.facebook.fresco:webpsupport:1.10.0` 

}
```