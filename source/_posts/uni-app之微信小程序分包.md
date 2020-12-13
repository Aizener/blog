---
layout: _post
title: uni-app之微信小程序分包
date: 2020-12-13 17:55:40
tags:
	- 微信小程序
	- uni-app
categories:
	- Web前端
cover: https://s3.ax1x.com/2020/12/13/reLcd0.jpg
---

> 转于https://www.cnblogs.com/jsccc520/p/13930358.html并做修改。

### 开始

 最近工作在接触uni-app，用它来开发微信小程序也是第一次接触，其中就因为一个项目比较大，碰到了要分包的情况，这里做一下笔记。

### 分包

这是因为小程序对包的大小要求有限制，当体积过大就必须进行分包。每个包不能超过2M，总体不能超过16M，因为这也是小程序“小”的特点吧。

[uni-app分包相关官方文档](https://uniapp.dcloud.io/collocation/pages?id=subpackages)

**其他解决方案**

其实开发过程中，勾选uni-app的运行时压缩代码是可以避免这种情况产生的：

> 过大的情况可以使用运行时压缩代码
>
> - `HBuilderX`创建的项目勾选`运行-->运行到小程序模拟器-->运行时是否压缩代码`
> - `cli`创建的项目可以在`pacakge.json`中添加参数`--minimize`，示例：`"dev:mp-weixin": "cross-env NODE_ENV=development UNI_PLATFORM=mp-weixin vue-cli-service uni-build --watch --minimize"`

### 使用

#### 首先你得有个uniapp的微信小程序项目

 这里假设你有了,原始项目结构

![图片](http://blog-cc.nos-eastchina1.126.net/1c712ca3-78bb-4b97-a7d9-9fb531227727)

 用微信开发工具打开的样子,没有分包之前

![图片](http://blog-cc.nos-eastchina1.126.net/2ee66c47-9ab3-49ab-a888-269200f7b2e5)

#### 在pages同级创建分包

![图片](http://blog-cc.nos-eastchina1.126.net/544ad85f-c535-454d-8f53-e79671bf9c00)

**这里说明一下，tabbar必须是在主包里面。**

####  配置

![图片](http://blog-cc.nos-eastchina1.126.net/7f20f7d5-30fa-437d-9f44-99c91d5a6570)


#### 分包优化

> 在对应平台的配置下添加`"optimization":{"subPackages":true}`开启分包优化
>
> - 目前只支持`mp-weixin`、`mp-qq`、`mp-baidu`的分包优化
> - 分包优化具体逻辑：
>   - 静态文件：分包下支持 static 等静态资源拷贝，即分包目录内放置的静态资源不会被打包到主包中，也不可在主包中使用
>   - js文件：当某个 js 仅被一个分包引用时，该 js 会被打包到该分包内，否则仍打到主包（即被主包引用，或被超过 1 个分包引用）
>   - 自定义组件：若某个自定义组件仅被一个分包引用时，且未放入到分包内，编译时会输出提示信息

> *我的理解呢就是,既然你分包了功能模块,那相应的静态资源你得分吧,用图片的时候直接是分包下的路径,这才是个完整的分包.*

这里我以微信小程序为例,

![图片](http://blog-cc.nos-eastchina1.126.net/c665ff04-d3e5-4e2c-8ec2-e619bd5a40d7)

![图片](http://blog-cc.nos-eastchina1.126.net/bd2c98f8-37eb-41d2-9cfc-05c0714e5852)

#### 跳转

在需要点击跳转的地方就好啦

```vue
uni.navigateTo({
	url: '/myPackageA/pages/piece/piece'
})
```

#### 查看

![图片](http://blog-cc.nos-eastchina1.126.net/72e214e7-8e7e-4883-9553-4411a8761a9a)

可以看到已经分包成功

### 预加载

在第一次加载分包中页面时，通常会提示“正在加载模块”，在意用户体验的肯定不会接受的吧，所以就有了预加载这么一个技术。

实现，在`uni-app`的`pages.json`里面添加：

```json
 "preloadRule": {
	 "pages/pose-contact/pose-contact": {   //想要实现预加载的页面名称
      "network": "all", //指定网络 all 不限网络   wifi：仅wifi
      "packages": ["subpages"] //分包的 root 或 name
    }
 },
```

上面的`preloadRule`表示预加载的配置，是一个对象：

他的`key`值是页面的路径，表示进入这个页面的时候开始加载对应的`value`里面的`packages`对应的包名：

- `network`表示预加载在什么情况下可以使用，默认是`wifi`，即在链接`wifi`情况下进行预加载；
- `packages`表示加载的报名，是一个数组，可以加载多个包。

app的分包，同样支持preloadRule，但网络规则无效。