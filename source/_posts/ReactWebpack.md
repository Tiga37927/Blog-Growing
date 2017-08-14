---
title: react-webpack
tags:
  - react
  - reactjs
  - webpack
  - 脚手架
categories:
  - react
  - reactjs
  - 工具
  - 脚手架

date: 2017-04-15 23:36:45
---

相关文档[react-webpack迷你书](https://fakefish.github.io/react-webpack-cookbook/index.html)

1. 安装webpack `npm i webpack --save-dev`
   目录结构：
* /app
* main.js
* component.js
* /build
   * bundle.js
   * index.html
* package.json
* webpack.config.js
   ** main.js **
   ​```js
     'use strict';
     import component from './component'

     document.body.appendChild(component());
   ​```
   ** component.js
   ​```js
     'use strict';


	module.exports = function () {
		let element = document.createElement('h1');
	
		element.innerHTML = 'Hello world';
	
		return element;
	};
   ​```
   ** index.html **
   ``` html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8"/>
    </head>
    <body>
    <script src="bundle.js"></script>
    </body>
    </html>
   ```

运行`npm run build` 在浏览器打开index.html，查看
2. 为免去频繁执行`npm run build`，使用webpack-dev-server
   安装：`npm i webpack-dev-sever`
   package.json: `dev": "webpack-dev-server --devtool eval --progress --colors --hot --content-base build"`
   启动参数：
   1. webpack-dev-server: 启动web service，地址：localhost:8080
   2. --devtool eval:创建资源路径，使你能看到出错的文件名和具体行号
   3. --progress:显示build进程
   4. --colors:控制台颜色
   5. --hot:热启动
   6. --content-base:指向设置的输出目录
      如果文件更改，浏览器没有同步刷新，尝试在修改entry：
      ```[
       'webpack/hot/dev-server',
       'webpack-dev-server/client?http://localhost:8080',
       path.resolve(__dirname, 'app/main.js')
       ]```
      ```
3. 使用react
    `npm i react react-dom`
4. webpack配置
  * HtmlWebpackPlugin 用于生成页面模板index.html/index.ejs， bundle.js等，具体[配置参数](https://segmentfault.com/a/1190000007294861)
  * ExtractTextPlugin webpack会将所有文件打包进js，这个插件会将css从js中分离，从而降低js的加载时间和大小，使得页面可以异步加载css，避免页面加载完js后重绘，[具体使用方式](http://www.cnblogs.com/sloong/p/5826818.html)
  * babel
