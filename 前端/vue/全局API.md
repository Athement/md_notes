# Vue.extend( options )
## Vue.extend基本使用
参数：{Object} options

使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。

data 选项是特例，需要注意 - 在 Vue.extend() 中它必须是函数


```
<div id="mount-point"></div>
// 创建构造器
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
  data: function () {
    return {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  }
})
// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount('#mount-point')
```

结果如下：

<p>Walter White aka Heisenberg</p>

## options（待补充）

# Vue.nextTick( [callback, context] )

**参数：{Function} [callback],{Object} [context]**

在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。


```
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

// 作为一个 Promise 使用 (2.1.0 起新增，详见接下来的提示)
Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```
##### [使用场景](https://www.jianshu.com/p/46c9d777cab1)
1. 在Vue生命周期的created()钩子函数进行的DOM操作一定要放在Vue.nextTick()的回调函数中.因为在created()钩子函数执行的时候DOM其实并未进行任何渲染，所以此处一定要将DOM操作的js代码放进Vue.nextTick()的回调函数中。
2. 在数据变化后要执行DOM更新操作，当你设置 vm.someData = 'new value'，DOM并不会马上更新，而是在异步队列被清除，也就是下一个事件循环开始时执行更新时才会进行必要的DOM更新。为了在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用 Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。
3. mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 vm.$nextTick 替换掉 mounted

##### 2.1.0 起新增
> 如果没有提供回调且在支持 Promise 的环境中，则返回一个 Promise。
>
>请注意 Vue 不自带 Promise 的 polyfill，所以如果你的目标浏览器不原生支持 Promise (IE不支持)，你得自己提供 polyfill。

# [Vue.set( target, propertyName/index, value )](https://www.jianshu.com/p/71b1807b1815)
**参数：**

- {Object | Array} target
- {string | number} propertyName/index
- {any} value

**返回值**：设置的值。

**用法：**

> 向响应式对象中新增 property，并确保这个新 property 同样是响应式的，且触发视图更新。
> 
> 必须用于向响应式对象上添加新 property，因为 Vue 无法探测普通的新增 property (如 this.myObject.newProperty = 'hi')

##### 示例

```
<template>
  <div id="resp">
    <p @click="addd(obj)">{{ obj.d }}</p>
    <p @click="adde(obj)">{{ obj.e }}</p>
    <p @click="update(obj)">{{ obj.f }}</p>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  data() {
    return {
      obj: {},
    };
  },
  mounted() {
    this.obj = { d: 0};
    this.obj.e = 0;
    Vue.set(this.obj,'f',{name:"mzw"})
    console.log("after--", this.obj);
  },
  methods: {
    addd(item) {
      item.d = item.d + 1;
      console.log("item--", item);
    },
    adde(item) {
      item.e = item.e + 1;
      console.log("item--", item);
    },
    adde(item) {
      item.e = item.e + 1;
      console.log("item--", item);
    },
    update(item) {
      item.f={age:18};
      console.log("item--", item);;
    },
  },
};
</script>

<style>
</style>
```
##### 示例解析:
1. 对e的更新不会更新视图
1. 对d,f的更新将更新视图,并更新e在视图中的展示


<font color='red' size=4>注意对象不能是 Vue 实例，或者 Vue 实例的根数据对象。</font>

# Vue.delete( target, propertyName/index )
**参数：**

- {Object | Array} target
- {string | number} propertyName/index
> 仅在 2.2.0+ 版本中支持 Array + index 用法。


删除对象的 property。如果对象是响应式的，确保删除能触发更新视图。


<font color='red' size=4>注意对象不能是 Vue 实例，或者 Vue 实例的根数据对象。</font>

# Vue.directive( id, [definition] )
**参数：**
- {string} id
- {Function | Object} [definition]

注册或获取全局指令。

```
// 注册
Vue.directive('my-directive', {
  bind: function () {},
  inserted: function () {},
  update: function () {},
  componentUpdated: function () {},
  unbind: function () {}
})

// 注册 (指令函数)
Vue.directive('my-directive', function () {
  // 这里将会被 `bind` 和 `update` 调用
})

// getter，返回已注册的指令
var myDirective = Vue.directive('my-directive')
```

# Vue.filter( id, [definition] )
**参数：**
- {string} id
- {Function} [definition]

注册或获取全局过滤器。

```
// 注册
Vue.filter('my-filter', function (value) {
  // 返回处理后的值
})

// getter，返回已注册的过滤器
var myFilter = Vue.filter('my-filter')
```

# Vue.component( id, [definition] )
**参数：**
- {string} id
- {Function | Object} [definition]

注册或获取全局组件。注册还会自动使用给定的 id 设置组件的名称
> 通过调用Vue.extend实现组件注册

# Vue.use( plugin )
**参数：**
- {Object | Function} plugin

安装 Vue.js 插件。
> 如果插件是一个对象，必须提供 install 方法。
>
>如果插件是一个函数，它会被作为 install 方法。
>
> install 方法调用时，会将 Vue 作为参数传入。

<font color='red'>注意 </font>
> 该方法需要在调用 new Vue() 之前被调用。
> 
> 当 install 方法被同一个插件多次调用，插件只会被安装一次。

# Vue.mixin( mixin )
**参数：**
- {Object} mixin

全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。

插件作者可以使用混入，向组件注入自定义的行为。
> 不推荐在应用代码中使用。

# Vue.compile( template )
**参数：**
- {string} template

将一个模板字符串编译成 render 函数。


```
var res = Vue.compile('<div><span>{{ msg }}</span></div>')

new Vue({
  data: {
    msg: 'hello'
  },
  render: res.render,
  staticRenderFns: res.staticRenderFns
})
```


# Vue.observable( object )
> 2.6.0 新增

**参数：**
- {Object} object

让一个对象可响应。Vue 内部会用它来处理 data 函数返回的对象。

返回的对象可以直接用于渲染函数和计算属性内，并且会在发生变更时触发相应的更新。也可以作为最小化的跨组件状态存储器，用于简单的场景：

const state = Vue.observable({ count: 0 })

const Demo = {
  render(h) {
    return h('button', {
      on: { click: () => { state.count++ }}
    }, `count is: ${state.count}`)
  }
}
在 Vue 2.x 中，被传入的对象会直接被 Vue.observable 变更，所以如这里展示的，它和被返回的对象是同一个对象。在 Vue 3.x 中，则会返回一个可响应的代理，而对源对象直接进行变更仍然是不可响应的。因此，为了向前兼容，我们推荐始终操作使用 Vue.observable 返回的对象，而不是传入源对象。

# Vue.version
提供字符串形式的 Vue 安装版本号。
> 这对社区的插件和组件来说非常有用，你可以根据不同的版本号采取不同的策略。