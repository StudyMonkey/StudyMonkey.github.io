---
title: Vue项目记录（三）
date: 2019-04-25
tags:
  - IT技术
  - 前端
  - Vue
---

#### 一、前端节流在Vue中的使用
```
// utils.js 中新建处理节流操作函数的方法

/***
 * 节流方法
 * @param func 输入完成的回调函数
 * @param delay 延迟时间
 */
export function debounce(func, delay) {
    let timer
    return (...args) => {
        if (timer) {
            clearTimeout(timer)
        }
        timer = setTimeout(() => {
            func.apply(this, args)
        }, delay)
    }
}

// searchWrap 组件中的搜索方法

import { debounce } from '@/utils/utils'
export default {
    name: 'searchWrap',
    data (){
        return {
            searchVal: ''
        }
    }
    watch: {
        // 开始自己的想法是在watch中监听searchVal的变化执行工具类的节流方法,发现方法不太好写
    }
    created(){
        // 节流操作
        this.$watch('searchVal', debounce(async (newQuery) => {
            // newQuery为输入的值
            console.log(newQuery) 
            this.$emit('changeSearchVal', newQuery);                            
        }, 300))        
    }
}


```


#### 二、发送消息之后使容器滚动条滚动到最底部
```
export default {
    name: 'chat',
    data(){
        return {
            mesList: []
        }
    },
    methods: {
        getCommonData(){
            // 执行获取数据的方法赋值给mesList
        }
    },
    watch: {
        mesList(){           
            this.$nextTick(() => { // 不加nextTick测试只能滚动到倒数第二条
                const container = this.$el.querySelector('#chatAreaScroll');
                container.scrollTop = container.scrollHeight;
            })
        }
    },
    created(){
        this.getCommonData( false );
    }
}
```