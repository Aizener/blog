---
layout: _post
title: uni-app在小程序里获取地理位置
date: 2020-11-29 19:53:17
tags:
	- 小程序
	- uni-app
	- Vue
categories:
	- Web前端
cover: https://cdn.jsdelivr.net/gh/aizener/my-imgs@master/blog/uni-app.png
---

> uni-app 小程序项目无法直接获取到地理位置，只能通过获取到的经纬度，调用第三方地图Api获取。

这里根据[腾讯位置服务官网](https://lbs.qq.com/miniProgram/jsSdk/jsSdkGuide/jsSdkOverview)给出的开发指南进行操作，微信小程序获取地理位置需要进行的操作

这里开发的是微信小程序，所以选择腾讯地图

下面进行操作演示

### 1.申请密钥

[申请开发者密钥（key）](https://lbs.qq.com/dev/console/key/add)输入即可

![img](https://img-blog.csdnimg.cn/20201119160648671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExODc5MjYyNzE2OA==,size_16,color_FFFFFF,t_70)

### 2.开通webserviceAPI服务

控制台 -> key管理 -> 设置（使用该功能的key）

![img](https://img-blog.csdnimg.cn/20201119161009520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExODc5MjYyNzE2OA==,size_16,color_FFFFFF,t_70)

 

![image-20201129201145474](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20201129201145474.png)

**上面红框框中的L4EBZ那串就是下面要用到的key。**

### 3.下载微信jssdk文件

进入官网，点此下载即可

![img](https://img-blog.csdnimg.cn/20201119161351576.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExODc5MjYyNzE2OA==,size_16,color_FFFFFF,t_70)

下载后，获取文件

![img](https://img-blog.csdnimg.cn/20201119161454299.png)

如下方官网示例代码，引入qqmap-wx-jssdk.js 文件

下面是代码获取位置示例：

```
// 引入SDK核心类

var QQMapWX = require('../../libs/qqmap-wx-jssdk.js');
var qqmapsdk;

Page({
    onLoad: function () {
        // 实例化API核心类
        qqmapsdk = new QQMapWX({
            key: '申请的key'
        });
    },

    onShow: function () {
        // 调用接口
        qqmapsdk.search({
            keyword: '酒店',
            success: function (res) {
                console.log(res);
            },

            fail: function (res) {
                console.log(res);
            },

        complete: function (res) {
            console.log(res);
        }
    });
})
```

这里是代码配置位置

### 4.manifest.json配置

manifest.json- 微信小程序配置 - 填写微信小程序AppID、选择位置接口，填写申请原因

![img](https://img-blog.csdnimg.cn/20201119162614508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExODc5MjYyNzE2OA==,size_16,color_FFFFFF,t_70)

如果配置里面找不到，就进源码视图里去添加代码：

```json
"mp-weixin": {
    /* 微信小程序特有相关 */
    "appid": "wxxxxxxxxxxxxx2",
    "setting": {
        "urlCheck": false
    },
    "usingComponents": true,
    "permission": {
        "scope.userLocation": {
            "desc": "小程序将使用定位功能"
        }
    }
}
```

`permission`那里就是配置权限的。

上面是配置，下面这一部分是[转载的代码](https://blog.csdn.net/BradyCC/article/details/103650722)

```
/**

 * @Author: licheng
 * @Date: 2019/12/21 6:06 下午
 * @Description:
 */

import QQMapWX from "../static/js/qqmap-wx-jssdk.min.js";


const getCity = () => {

  return new Promise((resolve, reject) => {
    // 获取授权信息
    uni.getSetting({
      success: res => {
        // console.log(res);
        if (res.authSetting && res.authSetting.hasOwnProperty("scope.userLocation")) {
          if (res.authSetting["scope.userLocation"]) {
            getCityInfo();
          } else {
            uni.showModal({
              title: "提示",
              content: "请重新授权获取你的地理位置，否则部分功能将无法使用",
              success: (res) => {
                if (res.confirm) {
                  uni.openSetting({
                    success: () => getCityInfo()
                  });
                } else {
                  reject("请授权获取你的地理位置，否则部分功能将无法使用！");
                }
              },
            });
          }
        } else {
          getCityInfo();
        }
      }
    });
    // 获取地理位置信息
    const getCityInfo = () => {
      // 腾讯地图Api
      const qqmapsdk = new QQMapWX({ key: "这里填写腾讯地图的秘钥" });
      // 授权
      uni.authorize({
        scope: "scope.userLocation",
        success: () => {
          uni.getLocation({
            type: "gcj02", //  wgs84: 返回GPS坐标，gcj02: 返回国测局坐标
            success: res => {
              const {latitude, longitude} = res;
              const location = {latitude, longitude};
              qqmapsdk.reverseGeocoder({
                location,
                success: res => resolve(res.result)
              });
            }
          });
        },

        fail: () => reject("请授权获取你的位置，否则部分功能将无法使用！"),
      });
    };
  });
};

export default getCity;
```

这一段实现的是需要用户授权才能继续进行下去的功能。不是简单的获取地理位置。

 **这里我个人补充说明一下，当用户未授权后再去调用uni.chooseLocation是无权限的，但是想调用authorize来获取用户权限又不行，因为微信小程序短期内是无法连续询问权限的，所以只能根据返回的失败提示来引导用户：点击右上角设置主动打开权限。**

简单的只获取位置，但是用户不授权也既可以继续运行的，参考如下：

### 重点。。。

这个是最重要的，之前没看到[腾讯位置服务官网](https://lbs.qq.com/miniProgram/jsSdk/jsSdkGuide/jsSdkOverview)的开发指南，导致走了不少弯路

配置位置：微信公众平台进入->开发->开发管理->开发设置

![img](https://img-blog.csdnimg.cn/20201119164122631.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExODc5MjYyNzE2OA==,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20201119164623689.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExODc5MjYyNzE2OA==,size_16,color_FFFFFF,t_70)



Over.

转载自[uni-app微信小程序获取用户地理位置信息](https://blog.csdn.net/a18792627168/article/details/108962311) 并稍作一点补充。