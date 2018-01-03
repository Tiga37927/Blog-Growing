---
title: 服务端渲染与react同构
tags:
  - seo
  - 同构
  - reactjs16
  - react-router-v4
  - nodejs
  - redux
categories:
  - 前端优化
  - react
  - nodejs

date: 2017-12-27 23:36:45
---

##### 前后端分离不在满足于SPA这种物理层面的分离，而是MVC业务层的分离，因此，如果前端掌握Controller，一切就简单了

  * 客户端渲染 vs 服务端渲染 vs 同构

    ######客户端渲染

    页面在js、css等资源文件加载完成后开始渲染，路由为客户端路由，也就是我们经常谈到的SPA

    ###### 服务端渲染

    页面由服务端直接返回给浏览器，路由为服务端路由，URL的变更刷新页面

    ###### 同构

    英文表述为Isomorphic或Universal，即编写的js可同时运行于浏览器及Node.js两套环境中，用服务端渲染来提升首屏的加载速度，首屏之后的路由由客户端控制，即在用户到达首屏后，整个应用仍是一个SPA

* 总体架构


// todo