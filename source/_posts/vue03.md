---
title: Vue项目记录（二）
date: 2019-04-21
tags:
  - IT技术
  - 前端
  - Vue
---

#### 1. 聊天界面的多个tabs会同时发起多个请求的问题
```
// vue 路由重定向
{ path: '*', redirect: '/chat' }

// 考虑修改为二级路由懒加载
但是内容被不同的tabs包裹，router-view不清楚放在哪里，所以放弃了这种实现方式

// 改为点击不同的tabs产生不同的请求
import { getData } from '@/utils/utils' 

methods: {
    async handleTabsChange(key){
        if ( key === 'topic' ){
            const res = await getData('topicList', {}); const { data: { data } } = res;
            this.topicList = data
        } else if ( key === 'member' ) {
            const res = await getData('memberList', {}); const { data: { data } } = res;
            this.memberList = data;            
        }
    }
}

// 再由props传递给子组件
<chat-topic :listTopic="topicList" />

```

#### 2. 根据以上实现方案发现子组件页面加载时内容为空
```
export default {
    name: 'topic',
    props: {
        listTopic: {
            type: Array,
            required: true
        }
    },
    data(){
        return{
            topicList: this.listTopic 
        }
    },
    create(){
        // 这条信息不打印
        console.log(this.topicList);
    }
}

// 考虑增加watch监听listTopic
watch: {
    listTopic(n,o){
        if ( n !== o ) {
            this.topicList = this.listTopic
        }
    }
}

```

#### 3. ant-design-vue图标不能满足项目需求，另外引入了iconfont图标
```
// 我这边是UI将项目所需图标添加到项目库，我这边下载之后，解压到src/assets目录下，文件夹更名为font，在main.js引入iconfont.css，在其他vue文件中调用类名即可

// iconfont引入在判断好友关系时，分别显示添加好友和删除好友的图标
<span :class="[ item.friend ? 'iconshanhaoyou' : 'iconjiahaoyou', 'iconfont' ]"></span>

显示在页面上的结果为：
<span class="0 1"></span>  不知道是啥原因

// 第二天发现原因，受外层tooltip影响，估计slot没有做相关处理
```

#### 4. computed的复杂运算
```
// 需求是根据json返回的identityNum字段的值来判断显示不同的身份，总共分为群主，管理员，成员三种，对应值为0,1，大于1

<tr v-for="(item,index) in memberList" :key="index">
    <td class="username">
        <span :class="identityClass(item.identityNum)"></span>
        <span v-text="item.username"></span>
    </td>
</tr>

export default {
    name: 'member',
    // 错误写法
    computed: {
        identityClass(num){
            if ( num === 0 ) {
                return 'iconfont iconqunzhu'
            } else if ( num === 1 ) {
                return 'iconfont iconguanliyuan'
            } else {
                return 'pl22'
            }
        }
    }
}

// 起初按照上面的写法报错了，说_vm.identityClass is not a function， 有点懵，不知道为啥，后面百度了下资料

Vue 的 computed 计算属性

// 简单计算
computed: {
    name(){
        return this.firstName + this.lastName
    }
}

// 复杂计算
computed: {
    identityClass(num){
        return function(num) {
            if ( num === 0 ) {
                return 'iconfont iconqunzhu'
            } else if ( num === 1 ) {
                return 'iconfont iconguanliyuan'
            } else {
                return 'pl22'
            }            
        }
    }
}
```

#### 5. vue的深度选择器
在之前的一些页面中尝试修改ant-design-vue的样式，有时候能改成功，有时候加了不起作用，之前没有深究，用的笨办法就是将style的scoped去掉，发现会起作用，但是这样存在隐患可能会影响到其他页面的样式，页面少还能去仔细检查，页面多就只能等暴露出问题了再去修改。
查找资料发现了Vue 的 Deep Selectors：
- If you want a selector in scoped styles to be "deep", i.e. affecting child components, you can use the >>> combinator:
```
<style scoped>
.a >>> .b { /* ... */ }
</style>
```

- 在less中使用 >>> 会报错， /deep/ 这样正常
```
.groupLogoWrap{
    display: flex;
    button{
        margin-top: 0;
    }
    /deep/.ant-upload-list{
        float: right;
        .ant-upload-list-item{
            margin: 0 10px 0 0;
            float: left;
        }
    }
}
```
[参考VUE](https://vue-loader.vuejs.org/guide/scoped-css.html#child-component-root-elements)