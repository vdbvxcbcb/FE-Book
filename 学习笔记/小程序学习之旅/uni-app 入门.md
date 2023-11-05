## 官网使用指南

1.	框架： 配置信息，框架接口(页面，页面生命周期等)
2.	组件： 同小程序组件一样,用于布局
3.	API：  用于实现指定的功能
全局对象 uni
组成: ECMAScript 的 js API + uni 扩展 api 组成
示例: uni.request || uni.setStorage



## 组件注意

easycom 组件规范

只要组件安装在项目的 components 目录下或 uni_modules 目录下，并符合 components/组件名称/组件名称.vue 目录结构。

就可以不用引用、注册，直接在页面中使用。

https://uniapp.dcloud.net.cn/tutorial/page-component.html

uni-app 支持的组件分为 vue 组件和小程序自定义组件，推荐使用 vue 组件。

如果扩展组件符合 uni-app 的 easycom 组件规范，则可以免注册，直接使用。比如 uni-ui 扩展组件就符合 easycom 组件规范。

如果组件不符合 easycom 规范，则需要在代码里手动 import 和注册组件，然后才能使用

如果组件名称或路径不符合 easycom 的默认规范，可以在 pages.json 的 easycom 节点进行个性化设置，自定义匹配组件的策略。

https://uniapp.dcloud.net.cn/component/

每个 vue 文件的根节点必须为 `<template>`，且这个 `<template>` 下只能且必须有一个根 `<view>` 组件。



如果了解小程序开发的话，uni-app 的基础组件会感觉很熟悉。

但需要注意组件上的事件绑定，需要以 vue 的事件绑定语法来绑定，

如 bindchange="eventName" 事件，需要写成 @change="eventName"



## 页面注意

.vue 页面和 .nvue 页面（用于 APP），均全平台支持，

差异在于当uni-app发行到App平台时，.vue文件会使用webview进行渲染，.nvue会使用原生进行渲染



和微信小程序一样，uni-app 会将 pages.json -> pages 配置项中的第一个页面，作为当前工程的首页（启动页）。



删除页面时，需做两件工作：

删除 .vue 文件或 .nvue 文件

删除 pages.json -> pages列表项中的配置



onReachBottom 使用注意 可在 pages.json 里定义具体页面底部的触发距离 onReachBottomDistance，

比如设为 50，那么滚动页面到距离底部 50px 时，就会触发 onReachBottom 事件。



为多端兼容考虑，建议优先从 uni-app 插件市场 (opens new window)获取插件。直接从 npm 下载库很容易只兼容 H5 端。

node_modules 目录必须在项目根目录下。不管是cli项目还是HBuilderX创建的项目。



















































































