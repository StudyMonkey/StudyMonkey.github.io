---
title: Vue基础介绍
date: 2019-03-27 09:11:53
tags:
  - IT技术
  - 前端
  - Vue
---

## 一、Vue 安装

```

$ npm install -g @vue/cli
$ cnpm install -g @vue/cli
$ yarn add @vue/cli -g

```

## 二、快速原型开发

```

$ npm install -g @vue/cli-service-global
$ cnpm install -g @vue/cli-service-global
$ yarn add @vue/cli-service-global -g

```

## 三、创建一个项目

```

$ vue create hello-world

```

## 四、添加路由

```

$ vue add router

```

### router/index.js 代码

```
    export default new Router({
        mode: 'history',
        routes: [
            {
                path: '/',
                component: Main
            },
            {
                path: '/login',
                component: Login
            }
        ]
    })

    // app.vue
    <div id="app">
        <router-view></router-view>
    </div>

    // main.vue
    <div>
        <router-link to="/"></router-link>
        <router-link to="/login"></router-link>
    </div>

```

## 五、路由重定向

```

export default new Router({
    mode: 'history',
    routes: [
        {path: '/', redirect: '/main'}

        // 当只有8080和8080/ 的时候，调到主页
    ]
})

```

## 六、定义子路由

```

export default new Router({

    routes: [
        {
            path: '/',
            component: Main
        },
        {
            path: '/login',
            component: Login
        },
        {
            path: '/admin',
            component: Admin,
            children: [
                {path: 'buttons', component: Buttons }, // 注意需要使用相对地址
                {path: 'Tables', component Tables }
            ]
        }
    ]
})

// 父级路由存在<router-view></router-view>

```

## 七、路由守卫

- 全局 beforeRouter
- 路由 beforeEnter
- 组件 beforeRouteEnter

```

const router = new Router({});

router.beforeEach((to, from, next) => {

    //to 前往的路由, from 来的路由, next 下一步钩子函数，没有问题，必须执行next()

    console.log(to);

    if (to.path !== '/login') {

        if (window.isLogin) {

            next()

        } else {

            next('/login?redirect='+ to.path)

        }

    } else {

        next()

    }

})

export default router

```
