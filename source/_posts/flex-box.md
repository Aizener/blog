---
title: FlexBox布局笔记
date: 2020-06-15 11:31:37
tags:
	- CSS3
categories:
	- Web前端
---

> [弹性布局](http://caibaojian.com/t/弹性布局)（flexible box）模块（目前是w3c候选的推荐）旨在提供一个更加有效的方式来布置，对齐和分布在容器之间的各项内容，即使它们的大小是未知或者动态变化的。
>
> 弹性布局的主要思想是让容器有能力来改变项目的宽度和高度，以填满可用空间（主要是为了容纳所有类型的显示设备和屏幕尺寸）的能力。

## Flex布局概念

Flex布局重要的几个点：父容器、子元素以及轴。

### 父容器

父容器即是父级元素的`display`设置为`flex`，即Flex布局，设置此元素的父元素也叫父容器，子元素会有其布局的排列特征。

```html
<style>
    .box {
        display: flex;
        width: 500px;
        height: 200px;
        background: aliceblue;
    }
    .item {
        width: 100px;
        height: 100px;
        border: 1px solid #ccc;
    }
</style>
<body>
    <div class="box">
        <div class="item">
        </div>
        <div class="item">
        </div>
        <div class="item">
        </div>
    </div>
</body>
```

<div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;">
    <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
    </div>
    <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
    </div>
    <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
    </div>
</div>

如上，我们看到，本应该为一行一行排列的`div`元素，因为处于`flex`布局下，而改变了原有的特性，这是因为`flex`有自己的默认特性。

## 轴

Flex布局默认X轴为主轴，Y轴为交叉轴，当设置`flex-direction: column`时，则相反。

## 父容器属性

1. flex-direction: 适用于父容器上，改变父容器的主轴方向，默认为row；

   （可选值row|row-reverse|column|column-reverse）

   下面是一个设置为`column`后的容器表现：

<div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-direction: column;">
    <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
    </div>
    <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
    </div>
    <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
    </div>
</div>

​	2. flex-wrap: 适用于父容器上，子元素是否可以换行，默认为`nowrap`；

​		(可选值nowrap|wrap|wrap-reverse)

 3. flex-flow：适用于父类容器上，是1和2的简写：flex-flow: <‘flex-direction’> || <‘flex-wrap’>

 4. justify-content：适用于父元素上，设置主轴方向的排序；

    （可选值flex-start|flex-end|center|space-between|space-around|space-evenly）

    下面是一个设置了`justify-content`属性的示例：

    <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;justify-content: space-evenly;">
        <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
        </div>
        <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
        </div>
        <div class="item" style="width: 100px;height:100px;border:1px solid #ccc">
        </div>
    </div>

   5. align-items: 适用于父容器上，设置交叉轴的排序方式；

   6. align-content：适用于父容器上，设置交叉轴子元素的排序方式。

      下面例子展示出了5和6的区别：

      

<div class="box" style="width: 200px; height: 300px; display: flex;background:aliceblue;flex-wrap:wrap;align-items: center">
    <div class="item" style="width: 150px;height:100px;border:1px solid #ccc">
    </div>
    <div class="item" style="width: 150px;height:100px;border:1px solid #ccc">
    </div>
</div>

上面是设置了`align-items`时的状态；

<div class="box" style="width: 200px; height: 300px; display: flex;background:aliceblue;flex-wrap:wrap;align-content: center">
    <div class="item" style="width: 150px;height:100px;border:1px solid #ccc">
    </div>
    <div class="item" style="width: 150px;height:100px;border:1px solid #ccc">
    </div>
</div>

上面是设置了`align-content`时的状态。

可以看到，`align-items`是设置每个子元素的对齐方式，而`align-content`是设置整个子元素的排序方式（前提是有多行，单行无效果）

## 子容器属性

1. order：适用于子元素上，设置子元素的排列顺序，默认为0，可以设置为负值，越小排列越前；

   ```html
   <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-wrap:wrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;order:1;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;">
       </div>
   </div>
   ```

   

   <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-wrap:wrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;order:1;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;">
       </div>
   </div>

2. flex-grow：适用于子元素上，用来给子元素扩容来分配父容器剩余的空间，默认为0；

   ```html
   <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-wrap:wrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;flex-grow: 2;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;flex-grow: 1;">
       </div>
   </div>
   ```

   

   <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-wrap:wrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;flex-grow: 2;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;flex-grow: 1;">
       </div>
   </div>

3. flex-shrink： 适用于元素上，用来缩小子元素来分配超出父元素的多余空间，默认为1；

   ```html
   <div class="box" style="width: 200px; height: 200px; display: flex;background:aliceblue;flex-wrap:nowrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;flex-shrink: 2;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;flex-shrink: 1;">
       </div>
   </div>
   ```

   

   <div class="box" style="width: 200px; height: 200px; display: flex;background:aliceblue;flex-wrap:nowrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;flex-shrink: 2;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;flex-shrink: 1;">
       </div>
   </div>

4. flex-basis： 适用于子元素上，设置主轴的子元素宽度大小；

   ```html
   <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-wrap:nowrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;flex-basis: 300px;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;">
       </div>
   </div>
   ```

   

   <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-wrap:nowrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;flex-basis: 300px;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;">
       </div>
   </div>

5. flex：适用于子元素上，是flex-grow，flex-shrink，flex-basis的缩写。

6. align-self：适用于子元素上，类似`align-items`，可以覆盖父元素的`align-items`的状态。

   ```html
   <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-wrap:nowrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;align-self: flex-end;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid orange;align-self: center;">
       </div>
   </div>
   ```

   

   <div class="box" style="width: 500px; height: 200px; display: flex;background:aliceblue;flex-wrap:nowrap; justify-content: space-evenly;">
       <div class="item" style="width: 150px;height:100px;border:1px solid blue;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid red;align-self: flex-end;">
       </div>
       <div class="item" style="width: 150px;height:100px;border:1px solid orange;align-self: center;">
       </div>
   </div>