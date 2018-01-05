---
title: koa学习进阶
tags:
  - koa
  - es6/7
  - nodejs
categories:
  - nodejs
  - koa2

date: 2017-08-02 03:36:45
---

### 开发环境

node7.6+

``` javascript
npm init -y
npm install koa
```

### 中间件

```
async/await中间件只有在koa2中才能使用
```

### ES6模块与CommonJS模块差异

CommonJS模块输出的是一个值的拷贝，ES6模块输出的是值的引用

CommonJS模块是运行时加载，ES6模块式编译时输出接口

建议在Dao中使用promisefyAll处理，async/await不适合批量处理

### 使用koa-router作为路由

``` javascript
view中间件一定要在route中间件之前注册，否则会提示ctx.render错误
npm install --save koa-router@7
const Router = require('koa-router')

let home = new Router()

// 子路由1
home.get('/', async ( ctx )=>{
  let html = `
    <ul>
      <li><a href="/page/helloworld">/page/helloworld</a></li>
      <li><a href="/page/404">/page/404</a></li>
    </ul>
  `
  ctx.body = html
})

// 子路由2
let page = new Router()
page.get('/404', async ( ctx )=>{
  ctx.body = '404 page!'
}).get('/helloworld', async ( ctx )=>{
  ctx.body = 'helloworld page!'
})

// 装载所有子路由
let router = new Router()
router.use('/', home.routes(), home.allowedMethods())
router.use('/page', page.routes(), page.allowedMethods())

// 加载路由中间件
app.use(router.routes()).use(router.allowedMethods())
```

### 使用koa-bodyparser解析参数

``` javascript
npm install --save koa-bodyparser@3
get请求使用querystring解析
post使用bodyParser
const bodyParser = require('koa-bodyparser')

// 使用ctx.body解析中间件
app.use(bodyParser())
```

### 使用koa-static处理静态文件

``` javascript
const Koa = require('koa')
const path = require('path')
const static = require('koa-static')

const app = new Koa()

// 静态资源目录对于相对入口文件index.js的路径
const staticPath = './static'

app.use(static(
  path.join( __dirname,  staticPath)
))


app.use( async ( ctx ) => {
  ctx.body = 'hello world'
})

app.listen(3000)
console.log('[demo] static-use-middleware is starting at port 3000')

访问http://localhost:3000/v2.jpg
```

### 文件上传使用koa-multer处理

``` javascript
const Koa = require('koa')
const path = require('path')
const router = require('koa-router')()
const multer = require('koa-multer')

const app = new Koa()

app.use(router.routes())
   .use(router.allowedMethods())
// 上传之后是一个二进制文件
const upload = multer({ dest: 'uploads/' })
// diskStorage存在服务器上，memoryStorage存在内存之中
const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, 'uploads/')
  },
  // 设置文件名
  filename: function (req, file, cb) {
    var splits = file.originalname.split('.');
    var suffix = splits[splits.length - 1]
    cb(null, splits[0] + '-' + Date.now() + '.' + suffix)
  }
})
const upload = multer({ 
  storage: storage
})

router.post('/profile', upload.single('upfiles'),function (ctx, next){
  ctx.body = "upload is success";
});
app.listen(3000)
console.log('[demo] static-use-middleware is starting at port 3000')

访问http://localhost:3000/v2.jpg
```

### koa2实现session
前言
koa2原生功能只提供了cookie的操作，但是没有提供session操作。session就只用自己实现或者通过第三方中间件实现。在koa2中实现session的方案有一下几种
如果session数据量很小，可以直接存在内存中
如果session数据量很大，则需要存储介质存放session数据
数据库存储方案

将session存放在MySQL数据库中
需要用到中间件
koa-session-minimal 适用于koa2 的session中间件，提供存储介质的读写接口 。
koa-mysql-session 为koa-session-minimal中间件提供MySQL数据库的session数据读写操作。
将sessionId和对于的数据存到数据库
将数据库的存储的sessionId存到页面的cookie中
根据cookie的sessionId去获取对于的session信息

``` javascript
const Koa = require('koa')
const session = require('koa-session-minimal')
const MysqlSession = require('koa-mysql-session')

const app = new Koa()

// 配置存储session信息的mysql
let store = new MysqlSession({
  user: 'root',
  password: 'abc123',
  database: 'koa_demo',
  host: '127.0.0.1',
})

// 存放sessionId的cookie配置
let cookie = {
  maxAge: '', // cookie有效时长
  expires: '',  // cookie失效时间
  path: '', // 写cookie所在的路径
  domain: '', // 写cookie所在的域名
  httpOnly: '', // 是否只用于http请求中获取
  overwrite: '',  // 是否允许重写
  secure: '',
  sameSite: '',
  signed: '',
  
}

// 使用session中间件
app.use(session({
  key: 'SESSION_ID',
  store: store,
  cookie: cookie
}))

app.use( async ( ctx ) => {

  // 设置session
  if ( ctx.url === '/set' ) {
    ctx.session = {
      user_id: Math.random().toString(36).substr(2),
      count: 0
    }
    ctx.body = ctx.session
  } else if ( ctx.url === '/' ) {

    // 读取session信息
    ctx.session.count = ctx.session.count + 1
    ctx.body = ctx.session
  } 
  
})

app.listen(3000)
console.log('[demo] session is starting at port 3000')
```

### 文件上传使用busboy解析

``` javascript
const inspect = require('util').inspect 
const path = require('path')
const fs = require('fs')
const Busboy = require('busboy')

// req 为node原生请求
const busboy = new Busboy({ headers: req.headers })

// ...

// 监听文件解析事件
busboy.on('file', function(fieldname, file, filename, encoding, mimetype) {
  console.log(`File [${fieldname}]: filename: ${filename}`)


  // 文件保存到特定路径
  file.pipe(fs.createWriteStream('./upload'))

  // 开始解析文件流
  file.on('data', function(data) {
    console.log(`File [${fieldname}] got ${data.length} bytes`)
  })

  // 解析文件结束
  file.on('end', function() {
    console.log(`File [${fieldname}] Finished`)
  })
})

// 监听请求中的字段
busboy.on('field', function(fieldname, val, fieldnameTruncated, valTruncated) {
  console.log(`Field [${fieldname}]: value: ${inspect(val)}`)
})

// 监听结束事件
busboy.on('finish', function() {
  console.log('Done parsing form!')
  res.writeHead(303, { Connection: 'close', Location: '/' })
  res.end()
})
req.pipe(busboy)
```

### 操作mysql

``` javascript
const mysql = require('mysql')

// 创建数据池
const pool  = mysql.createPool({
  host     : '127.0.0.1',   // 数据库地址
  user     : 'root',    // 数据库用户
  password : '123456'   // 数据库密码
  database : 'my_database'  // 选中数据库
})
 
// 在数据池中进行会话操作
pool.getConnection(function(err, connection) {
   
  connection.query('SELECT * FROM my_table',  (error, results, fields) => {
    
    // 结束会话
    connection.release();
 
    // 如果有错误就抛出
    if (error) throw error;
  })
})

```

### mongoose处理mongDB

```
const mongoose = require('mongoose')

mongoose.connect('mongodb://localhost:27017/auto')
```

