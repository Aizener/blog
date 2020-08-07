---
title: "使用Mockjs来模拟数据"
date: 2020-04-27 12:37:18
tags:
	- JavaScript
	- Axios
categories:
  - Web前端
cover: https://s1.ax1x.com/2020/05/19/YIe9cd.jpg
---

> 在前后端分离的开发中，大家都是同时开发的，往往后端的数据接口不会立即就写出来，此时，就需要我们来本地模拟一些数据来渲染页面，`Mockjs`就是很好的一个工具。

### 安装

npm:

```
npm install mockjs --save-dev
```

yarn:

```js
yarn add mockjs --dev
```

### 使用

先来看看官方给的一个例子：

```js
// 使用 Mock
var Mock = require('mockjs')
var data = Mock.mock({
    // 属性 list 的值是一个数组，其中含有 1 到 10 个元素
    'list|1-10': [{
        // 属性 id 是一个自增数，起始值为 1，每次增 1
        'id|+1': 1
    }]
})
// 输出结果
console.log(JSON.stringify(data, null, 4))
```

是不是有那么一点点蒙蔽，我们慢慢来看Mockjs的特点。

#### mock

`mock`方法是模拟数据接口的方法，可传三个参数：

- url: 模拟的接口地址；
- type: 请求类型；
- template|function(options): 记录用于生成响应数据的对象或函数。

第三个参数的`function`没有怎么用，感兴趣去看[Mockjs官网](http://mockjs.com/)

示例：

```js
Mock.mock('/api/article', 'get'， {
  name: '武动乾坤',
  type: '玄幻'
})
```

1. 上面我模拟了一个`/api/article`接口，没有写`host`的话，我这里用的Vue脚手架，默认的就是脚手架起的服务的`host`，但实际上直接在浏览器中输入对应地址是请求不到的，**因为`Mockjs`只是拦截了前端发起的请求，返回模拟的响应数据而已**。
2. 请求的方式，默认`get`，可不传；
3. `mock`方法的第三个参数就是模拟的数据，是一个方法或对象，后面再详说。
4. 请求的地址可以传递正则表达式来动态匹配。

动态匹配：

```js
Mock.mock(/\/api\/art/, {
  name: '武动乾坤',
  type: '玄幻'
})
```

这样也能匹配到`/api/article`这个接口。

#### setup

配置拦截 Ajax 请求时的行为。支持的配置项：

`timeout`: 目前我就只看到这么一个，这是响应时间的配置，表示请求后经过多久返回回来，单位为**毫秒**

#### Random

这个东西很好用，是一个用于生成随机数据的。

官网给的方法：

| 类型          | 方法                                                         |
| ------------- | ------------------------------------------------------------ |
| Basic         | boolean, natural, integer, float, character, string, range, date, time, datetime, now |
| Image         | image, dataImage                                             |
| Color         | color                                                        |
| Text          | paragraph, sentence, word, title, cparagraph, csentence, cword, ctitle |
| Name          | first, last, name, cfirst, clast, cname                      |
| Web           | url, domain, email, ip, tld                                  |
| Address       | area, region                                                 |
| Helper        | capitalize, upper, lower, pick, shuffle                      |
| Miscellaneous | guid, id                                                     |

更详细详细见[Mockjs官网](http://mockjs.com/)，我这里举几个例子：

1. 生成随机数或对象：

   `'page|18-25': 25`: 生成的age为18-25之间，与后面的值没关系；

   `'user|1-3': [{name: 'author'}]`: 生成1个数组，里面有1-3个对象；

2. 生成随机时间：

   `birthday: '@datetime'`

3. 生成随机中文名：

   `name: '@cname'`

其实用起来很简单，除了`Mockjs`已经内置的以外，还能自己定义`Random`，例如：

```js
Mock.Random.extend({
  constellation: function() {
      var constellations = ['白羊座', '金牛座', '双子座', '巨蟹座', '狮子座', '处女座', '天秤座', '天蝎座', '射手座', '摩羯座', '水瓶座', '双鱼座']
      return this.pick(constellations)
  }
})
```

使用的时候和原来一样：

`constellation: '@constellation'`

最后，看到一个例子，觉得不错，贴下来，[原文地址](https://blog.csdn.net/xiaoxiaojie12321/java/article/details/81301399)：

```js
import Mock from 'mockjs' // 引入mockjs
 
const Random = Mock.Random // Mock.Random 是一个工具类，用于生成各种随机数据
 
let data = [] // 用于接受生成数据的数组
let size = [
  '300x250', '250x250', '240x400', '336x280', 
  '180x150', '720x300', '468x60', '234x60', 
  '88x31', '120x90', '120x60', '120x240', 
  '125x125', '728x90', '160x600', '120x600', 
  '300x600'
] // 定义随机值
for(let i = 0; i < 10; i ++) { // 可自定义生成的个数
  let template = {
    'Boolean': Random.boolean, // 可以生成基本数据类型
    'Natural': Random.natural(1, 10), // 生成1到100之间自然数
    'Integer': Random.integer(1, 100), // 生成1到100之间的整数
    'Float': Random.float(0, 100, 0, 5), // 生成0到100之间的浮点数,小数点后尾数为0到5位
    'Character': Random.character(), // 生成随机字符串,可加参数定义规则
    'String': Random.string(2, 10), // 生成2到10个字符之间的字符串
    'Range': Random.range(0, 10, 2), // 生成一个随机数组
    'Date': Random.date(), // 生成一个随机日期,可加参数定义日期格式
    'Image': Random.image(Random.size, '#02adea', 'Hello'), // Random.size表示将从size数据中任选一个数据
    'Color': Random.color(), // 生成一个颜色随机值
    'Paragraph':Random.paragraph(2, 5), //生成2至5个句子的文本
    'Name': Random.name(), // 生成姓名
    'Url': Random.url(), // 生成web地址
    'Address': Random.province() // 生成地址
  }
  data.push(template)
}
 
Mock.mock('/data/index', 'post', data) // 根据数据模板生成模拟数据
```



分享完毕，Over。

