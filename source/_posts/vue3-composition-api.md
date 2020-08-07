---
layout: _post
title: 浅尝Vue3.0新特性CompositionAPI
date: 2020-08-06 22:59:21
tags:
	- Vue
categories:
  - Web前端
cover: https://cdn.jsdelivr.net/gh/aizener/my-imgs@master/blog/bg11.jpg
---

>Vue3.0于Vue2.0的基础上，改变还是比较多的，新增了Composition API让Vue的写法变得与以往不同，虽然现在还没有正式发布。但已经发不了@vue/composition-api和vue-next作为预热，因此可以先得一窥探，品尝一番。

#### 获取

1. 兼容版本：

   兼容版本是指通过@vue/composition-api引入，来使得Vue2.0能使用3.0最新的语法，此版本能兼容2.0的第三方插件；

   ```
   npm install @vue/composition-api --save
   ```

   

2. 激进版本：

   激进版本是指使用vue-next来使用最新的Vue3，这可能会导致目前还未更新的第三方框架不可用。

   ```
   npm install vue-next --save
   ```

#### 新增概念

Vue3.0新增了API来使用，这些API称为**组合式API（Composition API）**，并且Vue3.0的使用方式与原来方式写法差距也更大了。

##### setup

setup是Vue3.0组件的生命周期，介于原`beforeCreate`和`created`之间（Vue3.0没有这两个生命周期了，由setup取代），此函数作为组合API的入口，起着很大的作用。

- setup的第一个参数是props，也就是父组件传过来的值，需要声明props，否则拿不到值；
- setup的第二个值时上下文ctx，也就相当于vue2的this。

##### reactive&ref

- reactive

  接收一个普通对象然后返回该普通对象的响应式代理。等同于 2.x 的 `Vue.observable()`

  ```js
  const state = reactive({ count: 0 })
  ```

  响应式转换是“深层的”：会影响对象内部所有嵌套的属性。基于 ES2015 的 Proxy 实现，返回的代理对象**不等于**原始对象。建议仅使用代理对象而避免依赖原始对象。

- ref

  接受一个参数值并返回一个响应式且可改变的 ref 对象。ref 对象拥有一个指向内部值的单一属性 `.value`。

区别：`reactive`定义的属性渲染当视图后，再次通过其他方式（v-model特殊的除外）改变，视图值不会跟着更新；而`ref`则能更新。

解决：返回`state`对象的时候，可以通过`toRefs(state)`来返回，这样即是一个响应式且可改变的对象。

##### computed

计算属性，和原来一样。使用方式：

```vue
const newMsg = computed(() => {
	return state.msg.split('').reverse().join('')
})
```

计算属性，可以单独在`setup`里面作为函数书写，也可以在`reactive`书写。

##### watch

监听函数，和原来一样。使用方式：

```vue
watch(() => state.msg, (newVal, oldVal) => {
	console.log(newVal, oldVal)
})
```

传入两个回调函数，第一个回调函数返回的参数即是监听的对象；

第二个回调函数的值，就是修改前后的值。

##### 组件传值

组件传值的方式和原来一致。只是接收方式不同了，定义还是通过`props`来定义，接收（或者说是使用）通过`setup`函数来接收：

```vue
export default {
    props: ['value'],
    setup(props) {
		console.log(props)
	}
}
```

`props`属性是必须的，否则`setup`接收不了传递过来的值。

##### 生命周期

这些生命周期钩子注册函数只能在 `setup()` 期间同步使用， 因为它们依赖于内部的全局状态来定位当前组件实例（正在调用 `setup()` 的组件实例）, 不在当前组件下调用这些函数会抛出一个错误。

组件实例上下文也是在生命周期钩子同步执行期间设置的，因此，在卸载组件时，在生命周期钩子内部同步创建的侦听器和计算状态也将自动删除。

- **与 2.x 版本生命周期相对应的组合式 API**

  - `beforeCreate` -> 使用 `setup()`
  - `created` -> 使用 `setup()`
  - `beforeMount` -> `onBeforeMount`
  - `mounted` -> `onMounted`
  - `beforeUpdate` -> `onBeforeUpdate`
  - `updated` -> `onUpdated`
  - `beforeDestroy` -> `onBeforeUnmount`
  - `destroyed` -> `onUnmounted`
  - `errorCaptured` -> `onErrorCaptured`

- **新增的钩子函数**

  除了和 2.x 生命周期等效项之外，组合式 API 还提供了以下调试钩子函数：

  - `onRenderTracked`
  - `onRenderTriggered`

  两个钩子函数都接收一个 `DebuggerEvent`，与 `watchEffect` 参数选项中的 `onTrack` 和 `onTrigger` 类似：

  ```js
  export default {
    onRenderTriggered(e) {
      debugger
      // 检查哪个依赖性导致组件重新渲染
    },
  }
  ```

##### 依赖注入

`provide` 和 `inject` 提供依赖注入，功能类似 2.x 的 `provide/inject`。两者都只能在当前活动组件实例的 `setup()` 中调用。

```js
import { provide, inject } from 'vue'

const ThemeSymbol = Symbol()

const Ancestor = {
  setup() {
    provide(ThemeSymbol, 'dark')
  },
}

const Descendent = {
  setup() {
    const theme = inject(ThemeSymbol, 'light' /* optional default value */)
    return {
      theme,
    }
  },
}
```

`inject` 接受一个可选的的默认值作为第二个参数。如果未提供默认值，并且在 provide 上下文中未找到该属性，则 `inject` 返回 `undefined`。

- **注入的响应性**

  可以使用 `ref` 来保证 `provided` 和 `injected` 之间值的响应：

  ```js
  // 提供者：
  const themeRef = ref('dark')
  provide(ThemeSymbol, themeRef)
  
  // 使用者：
  const theme = inject(ThemeSymbol, ref('light'))
  watchEffect(() => {
    console.log(`theme set to: ${theme.value}`)
  })
  ```

  如果注入一个响应式对象，则它的状态变化也可以被侦听。