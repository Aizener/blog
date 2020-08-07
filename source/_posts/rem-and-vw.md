---
title: "说一下移动端的适配问题"
date: 2020-04-27 23:27:42
tags:
	- CSS
	- HTML
	- JavaScript
categories:
	- Web前端
cover: https://s1.ax1x.com/2020/05/19/YIeGNT.jpg
---

> Web前端的开发中，PC端需要适配各种浏览器，异常繁琐。而移动端浏览器因为基本都是基于`webkit`内核的，所以不需要兼容，但是也有个问题，就是手机机型大小不同，所以移动端就需要处理一定的适配问题。

### 方式

适配问题的话，我这里简单举例两种：

1. rem布局：因为`rem`的大小是通过浏览器的**根字体**来决定值的，所以我们可以通过设置跟字体的大小来改变rem的值，以此来达到适配的目的；
2. vw布局：vw是新的单位，100vw表示浏览器的宽度，所以通过vw也能达到适配效果。

### 实现

1. rem布局：

   rem布局的核心就是动态改变根字体，所以这里要用到`JavaScript`来实现：

   ```js
   (function () {
     function rem () {
       const baseFontSize = 100    // 定义1rem为设计稿的100px，那么750px的设计稿总宽为7.5rem
       const designWidth = 750     // 设计稿宽度750px
       // 现在来计算当前的字体大小：
       // 设计稿宽度 / 设计稿字体大小 = 屏幕宽度 / 屏幕字体大小
       // 屏幕字体大小 =  屏幕宽度 / 设计稿宽度 * 设计稿字体大小 
       const currentFontSize = (window.innerWidth / designWidth) * baseFontSize
       const html = document.querySelector('html')
       document.querySelector('html').style.fontSize = currentFontSize + 'px'
       document.querySelector('body').style.fontSize = '16px'
     }
     window.onresize = rem
   
     window.onload = function () {
       rem()
     }
   })()
   ```

   上面是一个IIFE，就是一个立即执行函数，函数体内就是`rem`布局的实现：

   1. 我们规定：

      `1rem=100px`，为什么要是这个值呢，因为整除100比较好计算些，那么总宽就应该为`7.5rem`；

   2. 通过等式：**设计稿宽度/设计稿定义的字体大小=浏览器屏幕/浏览器屏幕的字体大小**，有：

      `750px/100px=375px/fontSize`, 得到`fontSize`为`50px`（以调试的屏幕宽度为375时计算的）

      所以这里需要用到`js`来动态改变根字体大小，因为屏幕宽度会变化。

   3. 设置好`html`的根字体后，需要给`body`重新把字体定义回去，否则其他标签会有影响，`rem`取决于`html`的根字体大小，所以修改`body`是不会影响的。

   **网上有看到另一种方案，通过媒介查询来实现，就可以不用js了，暂不补充。**

2. vw布局：

   vw的实现就比较简单了，实现的方法也有许多，这里介绍一个简单的`less`来实现，不懂`less`是什么，先去看看[less官网](http://lesscss.cn/)

   思路：

   假设设计稿为`750px`，那么有这样一个公式：

   `设计稿宽度/100vw=宽度/目标vw值`，所以：`目标vw值=宽度/7.5vw`;	

   ```css
   @vw: 7.5vw;
   .box {
       width: 750/@vw;
       height: 200/@vw;
       background: red;
   }
   ```

   

   这样，按着设计稿的宽度，只需要除以`@vw`变量即可。

   再推荐一个`scss`的`vw`适配：

   ```scss
   @function vw($px) {
       // 750是设计稿的宽度，$px是对应设计稿的某个元素宽度
       return ($px / 750) * 100vw;
   }
   ```

   

   **不管是rem布局还是vw布局，个人意见主要是宽度来适配，而高度视情况而定，像上面，如果涉及是一个750px宽高200px的横幅，适配之后，宽度的确还是撑满的，但是高度就变为100了**