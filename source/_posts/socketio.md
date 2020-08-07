---
title: "简述socket.io的使用"
date: 2020-05-02 11:41:11
tags:
	- JavaScript
	- Node
categories:
    - 服务端
cover: https://cdn.jsdelivr.net/gh/aizener/my-imgs@master/blog/bg10.jpg
---

> Socket.io是一个[WebSocket](https://www.w3cschool.cn/websocket_protocol/)库，包括了客户端的js和服务器端的[nodejs](https://www.w3cschool.cn/nodejs/)，它的目标是构建可以在不同浏览器和移动设备上使用的实时应用。它会自动根据浏览器从WebSocket、[AJAX](https://www.w3cschool.cn/ajax/)长轮询、Iframe流等等各种方式中选择最佳的方式来实现网络实时应用，非常方便和人性化，而且支持的浏览器最低达IE5.5。

#### 获取

npm:

`npm install --save socket.io`

yarn:

`yarn add socket.io`

#### 开始

##### 服务端

服务端的socket.io需要通过`http`对象创建的`server`来获取:

```js
const http = require('http')
const socket = require('socket.io')

const server = http.createServer(handler)
const io = socket(server)

function handler (req, res) {
    ...
}
```

如果是`express`的话：

```js
const express = require('express')
const socket = require('socket')
const http = require('http')

const app = express()
const server = http.createServer(app)
const io = socket(server)

server.listen(port)
```

**注意，监听端口要通过`server`对象来监听**

##### 客户端

```js
<script src="node_mudules/socket.io-client/dist/socket.io.js"></script>
<script>
	const socket = io('http://localhost:port')
</script>
```



#### 事件

socket.io通过**监听***和**发送**事件的方式，来进行客户端和服务端的交流：

```js
... 服务端
io.on('connection', socket => {
	socket.emit('connected', {
        msg: 'Hello.'
    })
})
... 客户端
socket.on('connected', data => {
    console.log(data.msg)	// Hello
})
```

##### emit

emit发送事件，第一个参数为**事件**，第二个是**数据**，第三个是**回调**，回调不是必需的。

##### on

on监听事件，第一个是**事件**，第二个是一个**回调**，不同事件回调可能不同，自定义事件的回调有传过来的数据或者传过来的回调。

既然上面用到了`emit`发送事件和`on`监听事件，以此来进行服务端和客户端的交流，并且其通信方式有很多种可以使用（常用）：

| 方法\对象                     | io                                       | socket                                         |
| ----------------------------- | ---------------------------------------- | ---------------------------------------------- |
| .emit                         | 发送给所有客户端（即包括自己）           | 发送给当前连接的客户端（仅自己）               |
| .broadcast.emit               | \                                        | 发送给除了自己的其他客户端                     |
| .to('name').emit              | \                                        | 发送给名字是name房间的其他客户端（不包括自己） |
| .to('name1').to('name2').emit | \                                        | 同上，不过包含了两个房间                       |
| .in('name').emit              | 发送给房间为name的所有客户端（包括自己） | \                                              |
| .of('name').emit              | 发送给命名空间为name的所有客户端         | \                                              |
| .to(<socketid>).emit          | 发送给socket对应id的客户端               |                                                |

**注意： 下面的事件是保留的，不应该在应用中用作事件名称:**

- `error`
- `connect`
- `disconnect`
- `disconnecting`
- `newListener`
- `removeListener`
- `ping`
- `pong`

#### 房间

通过`socket.join('name')`的方式来加入：

```js
socket.on('connection', socket => {
    socket.join('room')
    socket.to('room').emit('join')
    // 上面可以简写为
    // socket.join('room').emit('join')
})
```

通过此功能，我们就可以做多个聊天室或者群聊的功能了。

#### 命名空间

命名空间默认有一个：`/`，但是你也可以自己来建立（比如你的代码要用在第三方上面，或者要分享出去）：

```
// 服务端
io.of('/chat').on('connectoin', socket => {
	// TODO
})
io.of('/news').on('connection', socket => {
	// TODO
})

// 客户端
const socket1 = io('http://localhost:port/chat')
const socket2 = io('http://localhost:port/news')
// TODO
```

#### 注意

1. 当我使用`http://localhost:port`连接的时候，本地没问题；但是到了线上环境就连不上了，需要把`localhost`改为服务器的公网IP。

2. 当客户端执行了`socket.close()`或者`socket.disconnect()`的时候，客户端的监听获取不到`socket`对象:

   ```js
   socket.on('disconnect', reason => {
       console.log(socket)	// undefined
   })
   setTimeout(() => {
       socket.close()
       // socket.disconnect()
   }, 1000)
   ```

3. 同样的，当连接断开时服务端发送到客户端的事件也无法监听，因为连接已经断了：

   ```js
   // 服务端
   socket.on('disconnect', reason => {
       socket.emit('name', {
           msg: 'over.'	// 客户端是监听不到的
       })
   })
   
   // 可以用下面这个
   socket.on('disconnecting', reason => {
       socket.emit('name', {
           msg: 'over.'	// 客户端能监听此事件
       })
   })
   ```

   