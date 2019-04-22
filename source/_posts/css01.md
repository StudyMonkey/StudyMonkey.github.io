---
title: 元素上下左右居中的几种方法
date: 2019-03-15
tags:
  - IT技术
  - 前端
  - Css
---

### 一、fixed + margin: auto布局
```
// 测试代码：

    <div class="box">1234</div>

    <style>
        .box{
            width: 200px;
            height: 200px;
            positiion: fixed;
            top: 0;
            right: 0;
            left: 0;
            bottom: 0;
            margin: auto;
        }    
    </style>
```

### 二、绝对定位布局
```
// 测试代码：
    <div class="box">1234</div>

    <style>
        .box{
            width: 200px;
            height: 200px;
            position: absolute;
            top: 50%;
            left: 50%;
            margin-top: -100px;
            margin-left: -100px;
        }
    </style>
```

### 三、table布局
父级元素：{display: table}
子级元素：{display: table-cell;vertical-align: middle}

### 四、flex布局
```
    <div class="boxWrap">
        <div class="box">1234</div>
    </div>
       
    <style>
        .boxWrap{
            display: flex;
            width: 600px;
            height: 600px;
            justify-content: center;
            align-items: center;
        }
        .box{
            width: 200px;
            height: 200px;
        }
    </style>
```

### 五、translate
```
    position: absolute;
    top: 50%;
    left: 50%;
    -webkit-transform: translate(-50%, -50%);
    -moz-transform: translate(-50%, -50%);
    -ms-transform: translate(-50%, -50%);
    -o-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
```