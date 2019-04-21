---
title: Vue项目记录（一）
date: 2019-04-19
tags:
  - IT技术
  - 前端
  - Vue
---

#### 1、git上创建项目，拉取到本地。本地运行vue create mooc-chat，添加路由vue add router

#### 2、引用Ant-design-vue

```
yarn add ant-design-vue -S

// 配置按需加载 babel.config.js

module.exports = {
    presets: [ '@vue/app' ],
    plugins: [
        ["import", {libraryName: 'ant-design-vue', libraryDirectory: 'es', style: 'css' }]
        // 原本style是true，下载less less-loader的依赖之后还是报错，就改成了css
    ]
}

// main.js 
import { Button } from 'ant-design-vue'

Vue.component(Button.name, Button);
Vue.component(List.name, List);
Vue.component(List.Item.name, List.Item);
Vue.component(Tabs.name, Tabs);
Vue.component(Tabs.TabPane.name, Tabs.Tabpane);

// app.vue
<template>
    <a-button type="primary">测试按钮</a-button>
</template>

// message 稍稍有点特殊
// main.js

import { message } from 'ant-design-vue'

Vue.prototype.$message = message

// chat.vue
method(){
    handleChatSend(){
        this.$message.info('发起私聊请求')
    }
}
```

#### 3. 修改滚动条样式
![滚动条样式](https://www.w3cways.com/wp-content/uploads/2014/09/scrollbarparts.png)
```
::-webkit-scrollbar //滚动条整体部分，其中的属性有width,height,background,border（就和一个块级元素一样）等。（位置1）
::-webkit-scrollbar-button //滚动条两端的按钮。可以用display:none让其不显示，也可以添加背景图片，颜色改变显示效果。（位置2）
::-webkit-scrollbar-track  // 外层轨道。可以用display:none让其不显示，也可以添加背景图片，颜色改变显示效果（位置3）
::-webkit-scrollbar-track-piece  //内层轨道，滚动条中间部分（位置4）
::-webkit-scrollbar-thumb //滚动条里面可以拖动的那部分（位置5）
::-webkit-scrollbar-corner //边角（位置6）
::-webkit-resizer //定义右下角拖动块的样式（位置7）

body{
  &::-webkit-scrollbar {
      /*滚动条整体样式*/
      width: 2px; /*高宽分别对应横竖滚动条的尺寸*/
      height: 2px;
  }
  &::-webkit-scrollbar-thumb {
      /*滚动条里面小方块样式*/
      border-radius: 5px;
      -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
      background: #06c5d2;
  }
  &::-webkit-scrollbar-track {
      /*滚动条里面轨道样式*/
      -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
      border-radius: 0;
      background: rgba(0, 0, 0, 0.1);
  }
}
```

#### 4. 做的时候又忘记了，记录一下，循环产生li点击添加样式
```
<li 
    v-for="(item,index) in chatList"
    :class="isActive === index ? 'bg_active' : '' "
    @click="handleLiClick(index)"
></li>

data(){
    return {
        isActive: ''
    }
},
methods: {
    handleLiClick(index){
        this.isActive = index
    }
}

```
a标签点击改样式，使用的时候用了跟上面一样的方法，但是直接跳转了路由，没有改变样式，测试了装饰器的stop,prevent,self都不起作用，最后看到了router-link-exact-active的类名，在这个类名下添加自定义样式即可

#### 5. 项目引用vuex

```
$ vue add vuex

// src目录下新建store文件夹，store.js文件

// store.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.store({
    state: {
        userList: [],
        chatConList: []
    },
    mutations: {
        addUserList (state, list) {
            state.userList = list
        },
        addChatConList( state, list ) {
            state.chatConList = list
        }
    
    }
    
})

// main.js
import Vuex from 'vuex'
import store from './store/store'

Vue.use(Vuex);

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')

// chatList.js 应用
created(){
    console.log( this.$store );
}
methods: {
    async getUserList(init){
        this.showLoading = true;
        const res = await getData('userList', {});
        this.showLoading = false
        let { data: { data } } = res;
        init ? this.messageList = this.messageList.concat(data) : this.messageList = data;
        // 存储到vuex
        this.$store.commit('addUserList', this.messageList);
    },
}
```