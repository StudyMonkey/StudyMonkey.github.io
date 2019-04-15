---
title: 初识React-native（三）
date: 2019-04-17
tags:
  - IT技术
  - 前端
  - React-native
---

#### 发现的bug问题
```
import { View, Button, ActivityIndicator } from 'react-native'

export default class App extends Component{

    state = {
        // show为true是不存在bug的
        show: false
    }

    handleClick = () => {
        this.setState({
            show: !this.state.show
        })
    }

    render(){
        const { show } = this.state
        return(
            <View>
                <Button title='测试按钮' onPress={ this.handleClick } />

                <!-- 测试发现这样是没有效果的 -->
                <ActivityIndicator  size='large' animating={ show } hidesWhenStopped={ false } />

                <!-- 正确写法 -->
                { show && <ActivityIndicator  size='large' animating={ show } hidesWhenStopped={ false } /> }
            </View>
        )
    }
} 

```