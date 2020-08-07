---
title: "简单说一下跨域问题以及解决方案"
date: 2020-04-26 23:16:55
tags:
	- Axios
	- Vue
	- Node
	- Nginx
categories:
  - Web前端
cover: https://s1.ax1x.com/2020/05/19/YIen3Q.jpg
---

> 因为SPA单页应用的广泛使用，现在都流行前后端分离，各自开发，让每个程序员专注自己的事情，但是因此也会产生一些问题，比如说非常常见的：跨域问题。

### 跨域

先来说下什么是跨域：

出于浏览器的同源策略限制。同源策略（Sameoriginpolicy）是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互。所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主机（host）和端口号（port）。

上面了那么多，其实总结下来就一句话：***只要协议，域名，端口有一个不同，则为跨域***。

### 解决方案

1. 后端解决：

   让后端来解决跨域问题，实际上生产环境也是靠后端来解决的。

   - 允许跨域（Node为例）：

   ```js
   res.writeHead(200, {
   	'Access-Control-Allow-Origin': '*'
   });
   ```

   - Nginx代理：

   ```nginx
   server {
       location /api {
           proxy_pass http://api.xxx/
       }
   }
   ```

   

2. 现在的项目基本都有脚手架的，通过脚手架起的服务来实现跨域（Vue-Cli3.0举例）：

   **根目录下新建一个`vue.config.js`，并如下配置：**

   ```js
   module.exports = {
     devServer: {
       proxy: {
         '/api': {
           target: '<url>',
           pathRewrite: {
               '^/api': ''
           }
         }
       }
     }
   }
   ```

   这里解释一下上面配置的含义：

   - `devServer`：表示的是开发环境；
   - `proxy`: 代理的配置项，是一个对象，可配置多个；
   - `target`: 要代理跨域的请求地址；
   - `pathRewirte`: 路径重写，因为这个配置会把`/api`打头的请求接口代理来实现跨域，所以往往我们的请求地址是有一个`/api`的，如果服务端提供的接口没有的话，需要把这个`/api`重写为空（如上）。

   **更多配置，请看`vue-cli`的[官网](https://cli.vuejs.org/zh/config/#devserver-proxy)。**

   