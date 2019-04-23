---
title: 遇到的IE 8兼容问题记录
date: 2019-03-17
tags:
  - IT技术
  - 前端
  - Css
---

### 一、white-space: nowrap在IE 8还是换行
```
// 解决方案： 

word-wrap: normal
```

### 二、IE 8会报addEventListener错误
```
// 解决方案： 增加判断，IE中使用attachEvent
if ( ie ) {
  window.attachEvent('scroll', () => {
    // do something
  })
} else {
  window.addEventListener('scroll', () => {
    // do something
  })
}
```