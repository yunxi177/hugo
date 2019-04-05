+++
title = "electron-vue  集成 element-ui"
date = 2019-04-04T20:21:44+08:00
tags = ["electron"]
categories = ["electron"]
draft = false
commentId="electron-vue-add-element-ui"
+++

# 简介
## 什么是 electron
Electron  是由 Github 开发，用  HTML，CSS 和 JavaScript 来构建跨平台桌面应用程序的一个开源库。 Electron 通过将 Chromium 和 Node.js 合并到同一个运行时环境中，并将其打包为 Mac  ，Windows 和 Linux 系统下的应用来实现这一目的。

Electron  于 2013 年作为构建  Github 上可编程的文本编辑器 Atom 的框架而被开发出来。这两个项目在2014春季开源。

目前它已成为开源开发者、初创企业和老牌公司常用的开发工具


## 什么是 electron-vue
electron-vue 是为了要避免使用 vue 手动建立起 electron 应用程序。electron-vue 充分利用 vue-cli 作为脚手架工具，加上拥有 vue-loader 的 webpack、electron-packager 或是 electron-builder，以及一些最常用的插件，如vue-router、vuex 等等

## 什么是 elecment ui
Element UI 是一套采用 Vue 2.0 作为基础框架实现的组件库，一套为开发者、设计师和产品经理准备的基于 Vue 2.0 的组件库，提供了配套设计资源，帮助你的网站快速成型


# 安装 electron-vue

```
# 安装 vue-cli 和 脚手架样板代码
npm install -g vue-cli  //如果你已经安装忽略此处
vue init simulatedgreg/electron-vue my-project

# 安装依赖并运行你的程序
cd my-project
yarn # 或者 npm install
yarn run dev # 或者 npm run dev
```
运行结果如下:  
![](http://pic.artacode.com/TIM截图20190404174833.png)

# 安装 element-ui

```
npm i element-ui -S
```

在 `/my-project/src/renderer/main.js` 中引入 element ui
```
// element-ui
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI)
```
此时就可以在任意 .vue 文件中添加和使用 element-ui 元素了。

```
<el-button @click="message" type="success" icon="el-icon-search" round>默认按钮</el-button>
```
运行效果如下:  
![](http://pic.artacode.com/2.png)

# 总结
整个安装过程并不复杂，
为什么是 `electron` 以我目前的技术栈来讲 `electron` 是最容易上手的开发桌面程序的一个途径，尽管他也有这样或那样的缺点，但总归来说还是瑕不掩瑜，此番安装成功，接下来就开始了 `electron` 开发之旅

# 参考链接
[electron-vue 文档](https://simulatedgreg.gitbooks.io/electron-vue/content/cn/)
[element ui](http://element.eleme.io/#/zh-CN/component/installation)