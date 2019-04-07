---
title: Nodejs利用mongoose模板操作数据
date: 2019-03-28
tags:
  - IT技术
  - 前端
  - 全栈
  - Nodejs
---

## 一、Nodejs安装
傻瓜式安装下载，直接下一步
[Node官网](https://nodejs.org/en/download/)

## 二、Mongodb下载安装
[Mongodb官网下载安装](https://www.mongodb.com/download-center/community)
- 新建文件夹data,我是在根磁盘下（例，d盘），然后在里面新建db文件夹
- 在mongodb安装目录的bin文件夹下运行命令mongod --dbpath d:\data\db启动mongodb服务

## 三、新建项目目录（示例nodeMongoose）
- 项目目录里面下载mongoose依赖包
`npm i mongoose -S/npm i mongoose --save`

- 新建app.js
```

    const mongoose = require('mongoose');  
    const DB_URL = 'mongodb://127.0.0.1:27017/imooc'  
    mongoose.connect(DB_URL);
    mongoose.connection.on('connected', () => {
        console.log('mongodb connect success')
    })

```

- 创建模型
```

const User = mongoose.model('User', new mongoose.schema({
    name: {type: String, require: true},
    age: {type: Number, require: true}
}))

```

- 新增数据
```

User.create({
    name: 'xiaomao',
    age: 25
}, (err, doc) => {
    if (!err) {
        console.log(doc)
    } else {
        console.log(err)
    }
})

```

- 查询数据
```

User.find({}, (err, doc) => {
    if (!err) {
        console.log(doc) // 得到的是数组
    } else {
        console.log(err)
    }
})
 
User.findOne({}, (err, doc) => {
    if (!err) {
        console.log(doc) // 得到的是对象
    } else {
        console.log(err)
    }
})

```

- 修改数据
```

User.update({name: 'xiaomao'}, {$set: {age:24}, (err,doc) =>{
    if (!err) {
        console.log(doc)
    } else {
        console.log(err)
    }
})

```

- 删除数据
```

User.remove({age: 25}, (err, data) => {
    if (!err) {
        console.log(doc)
    } else {
        console.log(err)
    }   
})

```


