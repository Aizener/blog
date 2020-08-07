---
title: '浅谈Axios的使用与封装'
date: 2020-04-26 18:52:52
tags:
	- Axios
	- Vue
categories:
  - Web前端
cover: https://cdn.jsdelivr.net/gh/aizener/my-imgs@master/blog/bg0.jpg
---

> Axios是一款基于Promise的HTTP库，可在nodejs或浏览器端使用，并且是现在项目中大量使用的请求库，使用起来也非常简单明了。

 ### 安装

1. npm

   `npm intall axios --save`

2. yarn

   `yarn add axios`

### 请求

下面是官方的例子：

```js
// 为给定 ID 的 user 创建请求
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

// 可选地，上面的请求可以这样做
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

可以看到，axios可以通过别名的形式来执行`get`，`post`请求等，并且通过`then`和`catch`的回调函数方式来知道响应。

**axios的其他别名**：

- axios.request(config)

- axios.get(url[, config])

- axios.delete(url[, config])

- axios.head(url[, config])

- axios.post(url[, data[, config]])

- axios.put(url[, data[, config]])

- axios.patch(url[, data[, config]])

***除了通过别名的方式以外，axios也可以直接请求***

```js
// 发送 POST 请求
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```



### 实例

axios除了可以直接请求以外，还能通过`create`方法来获得一个实例，获得的实例与axios一样可以使用别名来请求，并且也有默认值、拦截器等axios原有的功能。

```js
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
instance.get('/demo');
```

**`create`里面的参数不是必须的**

### 响应

axios的响应结构一般如下：

```js
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 服务器响应的头
  headers: {},

  // `config` 是为请求提供的配置信息
  config: {}
}
```

### 全局默认值

axios可以通过`defaults`属性来设置全局的默认值：

```js
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

### 拦截器

axios可以通过拦截器来对**请求前**和**响应前**时的状态属性进行更改：

```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```

如果你想在稍后移除拦截器，可以这样：

```js
var myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

### 封装

会使用axios之后，我们应该考虑怎样更好的使用它，一般来说，我们的请求都会封装起来，方便维护使用，这里简单封装一下：

1. 封装请求的方法

```js
import axios from 'axios'

const baseURL = process.env.NODE_ENV === 'development' ? 'http://localhost:3000' : '';
class HttpRequest {
  constructor () {
    this.axios = axios.create()
  }

  getConfig () {
    return {
      baseURL: baseURL,
      headers: {
        //
      }
    }
  }

  setInterceptors(instance) {
    instance.interceptors.request.use(config => {
      console.log(config)
      return config
    })

    instance.interceptors.response.use(res => {
      console.log(res)
      return res
    })
  }

  request(params) {
    const config = Object.assign(this.getConfig(), params)
    this.setInterceptors(this.axios)
    return this.axios(config)
  }
}

export default new HttpRequest()
```

2. 通过封装的请求方法来发送请求，举例获取文章的：

   ```js
   import axios from './axios.js';
   
   export const getArticle = () => {
       return axios.request({
           url: '/getArticle',
           method: 'get'
       })
   }
   ```

   *通过`getArticle`返回的对象是`Promise`对象。*

   

***这些都只是摘抄官网最基本的，详情浏览[Axios官网](https://www.kancloud.cn/yunye/axios/234845)。***

