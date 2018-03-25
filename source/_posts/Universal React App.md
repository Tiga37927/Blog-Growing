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


详见：https://medium.com/leanjs/universal-create-react-app-step-by-step-b80ba68d125d

### Step 1

* create-react-app my-app

* npm run eject 要修改一些配置

* 创建三个文件夹

  src/client
  src/server
  src/shared

* /client

  src/index.js-->src/client/index.js

  src/index.css-->src/client/index.css

  src/registerServiceWorker.js to src/client/registerServiceWorker.js

  copy App.css内容到client/index.css里面

  添加react-router-dom，修改/client/index.js内容：

  - Replace import App from ‘./App’; for import App from ‘../shared/App’;
  - Add import { BrowserRouter as Router } from ‘react-router-dom’
  - Replace 
    ReactDOM.render(<App />, document.getElementById(‘root’));
    with:
    ReactDOM.render(
     <Router><App /></Router>,
     document.getElementById(‘root’)
    );

* /shared

  - src/App.js to src/shared/App.js
  - src/App.test.js to src/shared/App.test.js
  - src/logo.svg to src/shared/logo.svg

* /server

  - src/server/index.js
  - src/server/app.js
  - src/server/render.js
  - yarn add express nodemon webpack-node-externals http-proxy-middleware isomorphic-fetch

### Step 2

​	**/src/server/index.js**

* 1.创建和启动服务

* 2.如果是生产环境，要代理url路径

  [https://github.com/leanjscom/universal-create-react-app/blob/master/src/server/index.js]: https://github.com/leanjscom/universal-create-react-app/blob/master/src/server/index.js

* 3.根据根路径映射url资源`app.use(‘/’, express.static(‘build/client’))`

* 4.使用与react相关

  #### /src/server/app.js

* 接收请求，处理路由

  #### /src/server/render.js

* 渲染Dom

### Step 3

​	/package.json

​	add:

​	"serve": "NODE_ENV=production node ./build/server/bundle.js",

​    	"build-client": "node scripts/build-client.js",

   	"build-server": "node scripts/build-server.js",

   	 "build": "npm run build-client && npm run build-server"



















