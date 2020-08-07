---
layout: _post
title: 初窥React基础知识
date: 2020-08-06 23:02:24
tags:
	- React
categories:
	- Web前端
---

> React 是一个用于构建用户界面的 JavaScript 库。你可以在[官方首页](https://react.docschina.org/)或[官方教程](https://react.docschina.org/tutorial/tutorial.html)中学习什么是 React。当前，React的生态可谓是前端里面最庞大的了，其使用量也遍布全球，所以，很有必要了解一下这个十分优秀的框架。

其特点为：

- 声明式：

  >  React 使创建交互式 UI 变得轻而易举。为你应用的每一个状态设计简洁的视图，当数据改变时 React 能有效地更新并正确地渲染组件。

- 组件化：

  > 创建拥有各自状态的组件，再由这些组件构成更加复杂的 UI。

### 获取&安装

这里介绍两种方式：

1. 直接引用（适用于刚学习练习）：

   ```html
   <script src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
   <script src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
   <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
   ```

2. 脚手架安装（初级使用）：

   - 使用npm进行安装`create-react-app`脚手架，然后通过`create-react-app demo`来创建项目；
   - 使用npx来进行创建项目：`npx create-react-app demo`。

### Hello React！

这里我新建了一个html文件，并且使用react输出了一行`Hello React！`：

```html
<script type="text/babel">
    class App extends React.Component {
      render() {
        return (
          <h1>Hello.</h1>
        )
      }
    }

    ReactDOM.render(
      <App />,
      document.querySelector('#app')
    )
  </script>
```

**注意，上面script标签有`type="text/babel"`，是因为这里使用了浏览器不可识别的`JSX`语法，需要使用baebel编译为可读代码。**

> ReactDOM.render(element, container[, callback])

在提供的 `container` 里渲染一个 React 元素，并返回对该组件的[引用](https://zh-hans.reactjs.org/docs/more-about-refs.html)（或者针对[无状态组件](https://zh-hans.reactjs.org/docs/components-and-props.html#function-and-class-components)返回 `null`）。

如果 React 元素之前已经在 `container` 里渲染过，这将会对其执行更新操作，并仅会在必要时改变 DOM 以映射最新的 React 元素。

如果提供了可选的回调函数，该回调将在组件被渲染或更新之后被执行。

`App`是一个自定义组件，继承自于`React.Component`，这也是我们创建组件的方法，通过继承此类后，实现`render`方法，并返回一个`JSX`对象即可。

### VSCODE的配置

因为`JSX`不是可识别的语法，所以`JSX`里面写代码需要配置一些选项，这里罗列VSCODE的配置：

1.  安装 JS JSX Snippets插件；
2.  安装ES7 React/Redux/GraphQL/React-Native snippets插件；
3.  在设置里搜索`Emmet`，或者直接搜索要改的值：
    1.  在文件里配置`"emmet.triggerExpansionOnTab": true` 或 在UI界面直接勾选；
    2.  在文件里配置 `"emmet.includeLanguages": {"javascript": "javascriptreact"}`或者通过UI界面直接新增键值对。

这样，React在VSCODE的一些配置就搞定了。

### JSX简介

如下声明在js代码里的这段代码：

```jsx
const element = <h1>Hello, world!</h1>;
```

这个有趣的标签语法既不是字符串也不是 HTML。

它被称为 JSX，是一个 JavaScript 的语法扩展。我们建议在 React 中配合使用 JSX，JSX 可以很好地描述 UI 应该呈现出它应有交互的本质形式。JSX 可能会使人联想到模版语言，但它具有 JavaScript 的全部功能。

#### 为什么会有JSX呢？

React 认为渲染逻辑本质上与其他 UI 逻辑内在耦合，比如，在 UI 中需要绑定处理事件、在某些时刻状态发生变化时需要通知到 UI，以及需要在 UI 中展示准备好的数据。

React [不强制要求](https://zh-hans.reactjs.org/docs/react-without-jsx.html)使用 JSX，但是大多数人发现，在 JavaScript 代码中将 JSX 和 UI 放在一起时，会在视觉上有辅助作用。它还可以使 React 显示更多有用的错误和警告消息。

#### 在JSX中使用表达式

在JSX中，通过`{}`的方式，在代码中插入表达式，例如：

```jsx
const name = 'Hello'
const title = <h1>{ name }</h1>
```

**除了插入函数，变量等等以外，`{}`里还能插入JSX，因为`JSX`也是一个表达式；**

#### JSX 特定属性

你可以通过使用引号，来将属性值指定为字符串字面量：

```
const element = <div tabIndex="0"></div>;
```

也可以使用大括号，来在属性值中插入一个 JavaScript 表达式：

```
const element = <img src={user.avatarUrl}></img>;
```

在属性中嵌入 JavaScript 表达式时，不要在大括号外面加上引号。你应该仅使用引号（对于字符串值）或大括号（对于表达式）中的一个，对于同一属性不能同时使用这两种符号。

> **警告：**
>
> 因为 JSX 语法上更接近 JavaScript 而不是 HTML，所以 React DOM 使用 `camelCase`（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定。
>
> 例如，JSX 里的 `class` 变成了 [`className`](https://developer.mozilla.org/en-US/docs/Web/API/Element/className)，而 `tabindex` 则变为 [`tabIndex`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/tabIndex)。

假如一个标签里面没有内容，你可以使用 `/>` 来闭合标签，就像 XML 语法一样，我们不必考虑内部是怎么实现转换的，因为Babel会把JSX转译成一个名为`React.createElement()` 函数调用。

### 组件

组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。

#### 函数组件

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

该函数是一个有效的 React 组件，因为它接收唯一带有数据的 “props”（代表属性）对象与并返回一个 React 元素。这类组件被称为“函数组件”，因为它本质上就是 JavaScript 函数。

#### 类组件

```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上述两个组件在 React 里是等效的。

#### 渲染组件

之前，我们遇到的 React 元素都只是 DOM 标签：

```
const element = <div />;
```

不过，React 元素也可以是用户自定义的组件：

```
const element = <Welcome name="Sara" />;
```

当 React 元素为用户自定义组件时，它会将 JSX 所接收的属性（attributes）以及子组件（children）转换为单个对象传递给组件，这个对象被称之为 “props”。

**注意： 组件名称必须以大写字母开头。**

> React 会将以小写字母开头的组件视为原生 DOM 标签。例如，`<div />` 代表 HTML 的 div 标签，而 `<Welcome />` 则代表一个组件，并且需在作用域内使用 `Welcome`。
>
> 你可以在[深入 JSX](https://react.docschina.org/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized)中了解更多关于此规范的原因。

#### 组件传参

1. 父传子：

   父传子，只需要在使用组件的时候传递参数即可，例如：

   ```react
   <Demo value={this.state.value} />
   ```

2. 子传父：

   子传父，是通过执行父级传过来的函数实现的，例如：

   ```react
   <Parent getChildValue={this.getChildValue}	// getChildValue是一个方法
   // 下面是Child组件来传参给父组件：
   render () {
   	return {
           <button onClick={this.props.getChildValue.bind(null, 'child')}></button>
       }        
   }
   // 通过以上方法执行了父级的方法，并且传入了一个参数'child'
   ```

   

3. 祖传孙：

   祖传孙有两种方式：

   1. 通过getChildContext方法实现（官方已不推荐）：

      ```react
      1. 声明上下文数据类型：
      static childContextTypes = {}
      2. 向上下文控件中存值, 方法名是固定写法
      getChildContext () {
          return {
          }
      }
      3. 在需要使用Context的子组件中定义
      static contextTypes = {
          value: PropTypes.string
      }
      即可通过this.context对象来获取祖级定义的值
      ```

   2. 通过createContext方法来实现：

      ```react
      const DemoContext = React.createContext('react')
      // 1.祖组件中不使用Provider，直接在子孙组件中定义contextType即可使用：
      static contextType = DemoContext
      console.log(this.context.name)	// react
      // 2.祖组件中使用了Provider，Provider可以通过value来覆盖原值，子组件使用同上：
      (
      <DemoContext value="i am react">
          <Child />
      </DemoContext>
      )
      // 子孙组件：
      static contextType = DemoContext
      console.log(this.context.name)	// i am react
      ```

#### 生命周期

React生命周期大致分为三个部分：挂载期，更新期，卸载期。

<img src="https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3703623186,1159232405&fm=26&gp=0.jpg" />

##### 挂载期

1. 初始化（constructor）：

   组件初始化就是在初始化data和props的值；

2. 挂载前：

   挂载前就是在render函数执行之前，有一个钩子函数可执行：componentWillMount；

3. 挂载后：

   挂载前就是在render函数执行之前，有一个钩子函数可执行：componentDidMount；

##### 更新期

 1. state更新：

    1. state更新后会执行一个钩子函数shouldComponentUpdate，这个函数的返回值表示是否进行重新渲染。
       2. 如果不进行重新渲染，则直接跳过以下过程；
          3. 如果进行重新渲染，则在render前会有一个componentWillUpdate钩子函数，render后有一个componentDidUpdate钩子函数。

 2. props更新：

    props更新后会有一个钩子函数componentWillReceiveProps，此钩子函数执行之后，会和state更新时的第一个步骤一样。

##### 卸载期

​	当组件卸载前，会有一个componentWillUnmount钩子函数，执行之后组件卸载。