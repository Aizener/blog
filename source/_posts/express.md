---
layout: _post
title: Express学习笔记
date: 2020-06-12 16:54:59
tags:
	- JavaScript
	- Node
categories:
  - 服务端
cover: https://s1.ax1x.com/2020/05/19/YIeZ4S.png
---

> Express是基于Node的一个Web应用程序框架。

### 开始

#### 安装

通过npm进行安装：

`npm install --save express`

#### Hello World！

Express可以很简单的搭起一个Web服务：

```js
const express = require('express')
const app = express()
app.listen(3000)
console.log('The server is running at http://localhost:3000.')
app.get('/', (req, res) => {
    res.send('Hello World!')
})
```

执行文件后，访问网页，便会看到打印出`Hello World!`，Express起一个服务就是如此的简单快速。

### 中间件

#### 基础

中间件就是一堆方法，从一个请求开始，可以直接对请求作出响应，也可以对请求做一些处理后，交给下个中间件进行处理，是介于请求和响应之间的一个状态。

Express的中间件是从上到下执行的，每次处理后记得在响应前要调用`next`方法，否则请求便会一直卡在一个地方。

**app.use**

同`app.get`一样，`app.use`也是定义中间件的一种，只是前者定义的方法以及路径；后者是所有方法或指定路径（不传入路径默认为所有）。

例如：

```js
app.use('/demo', (req, res) => { console.log('This is a demo.') })
```

#### 应用

* 错误处理；

* 登录判断；

* 404跳转

  ...

错误处理，一般需要`err`参数，在异步函数里面需要使用`next(err)`方法来抛出错误，如果是同步的代码里面直接`try catch`抛出异常就行。

#### 路由

路由就是`express`的中间件实现的一种。

##### 模块化路由

如果路由一但复杂起来，在一个页面写起来是十分不好维护的，所以`express`提供了一个`router`对象来使得路由可以模块化开发，例如：

```js
const express = require('express')
const route = express.Router()
route.get('/index', (req, res) => {
    res.send('This is a module router.')
})
module.exports = route
```

上面就通过`express.Router()`定义了一个二级路由，可以通过引入这个模块来实现路由的模块化：

```js
const users = require('./routes/users')
app.use('/users', users)
```

访问`/users/index`就会发现打印出了上面定义出来的信息。

##### 参数获取

* GET参数获取：

  通过`query`对象可以获取到GET传递来的参数；

* POST参数获取：

  通过使用第三方中间件`body-parser`来获取POST传递过来的参数；

  ```js
  const bodyParser = require('body-parser')
  app.use(bodyParser.urlenvoded({extended: false}))
  app.use(bodyParser.json())`
  ```

  * `app.use(bodyParser.urlencoded({extended: false})`用于解析`application/x-www-form-urlencoded`格式的数据

* `app.use(bodyParser.json())`用于解析json格式的数据

##### 路由占位符

通过`:id`的方式可以为`id`设置一个占位符，参数就通过`id`来获取，例如：

    ```js

app.get('/users/:id', (req, res) => {
    console.log(req.params.id)
})
    ```

通过`req.params`方式可以获取路由参数，注意与`req.query`的区别。

#### 静态资源的处理

express内置了一个中间件就是处理静态资源的：`express.static`，通过使用这个中间件，可以处理客户端发过来对静态资源的请求。

```js
const path = require('path')
app.use('/public', express.static(path.join(__dirname, 'public')))
```

当然，也可不不加第一个参数，那么默认的静态资源请求路径就是根路径：`/`。

### Express脚手架

express官方提供了脚手架工具，能快速的构建出一个项目来：

#### 安装

`npm install -g express-generator`

#### 使用

`express --no-view project-name`

--no-view表示不使用模板引擎。