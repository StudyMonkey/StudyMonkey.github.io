---
title: Github Pages使用
date: 2019-04-04
tags:
  - IT技术
  - 前端
  - Github
---

### 这一切的基础都是在你有了GitHub账号下进行的

##### 在GitHub网页中新建项目，项目名称只能叫做username.github.io,即你的github用户名再加后缀,且账号下只能新建这一个，多建无效

##### 建好项目之后，假设你的电脑上已经下载了git，能够运行git bash，将项目clone至本地电脑，可利用hexo新建整个博客框架，后续发文章可持续进行，坚持就是胜利

#### 项目展示

##### 在GitHub网页上另外新建一个项目，比如叫vue，可添加一个readme.md，在项目设置里面，滚动到GitHub Pages，若按照上面的步骤增加了github.io，则在此只能看到master分支和master/docs目录，以及禁用github pages三个选项，没有gh-pages选项

##### 我的展示是用的master分支，代码放在了code分支上，运行build打包之后，将代码提交到master即可展示出来