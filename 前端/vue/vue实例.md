**创建一个 Vue 实例**

每个 Vue 应用都是通过用 Vue 函数创建一个新的 Vue 实例开始的

var vm = new Vue({  // 选项 })

文档中经常会使用 vm (ViewModel 的缩写) 这个变量名表示 Vue 实例。

当创建一个 Vue 实例时，可以传入一个选项对象控制vue实例的行为([vue选项.note](note://67865075BAC24B7D82FF9E68FF898696))。

 Vue 应用由一个通过 new Vue 创建的根 Vue 实例、可选的嵌套的、可复用的组件树组成。

**vue数据**

当一个 Vue 实例被创建时，会将 data 对象中的 property 加入到 Vue 的响应式系统中。

响应式数据更新

当data 对象中的 property 改变时，视图会进行重渲染.

```js
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 获得这个实例上的 property
// 返回源数据中对应的字段
vm.a == data.a // => true

// 设置 property 也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```

只有当实例被创建时就已经存在于 data 中的 property 才是响应式的。如果你添加一个新的 property如下,对 b 的改动将不会触发任何视图的更新。

```js
vm.b = 'hi'
```

 Object.freeze()会阻止修改现有的 property，响应系统无法再追踪变化。

```js
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
<div id="app">
  <p>{{ foo }}</p>
  <!-- 这里的 `foo` 不会更新！ -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
```

数据相关的property

请参考:[数据.note](note://42369A2B6EFF4830986FEAA3B0187E1E)

**vue实例的其他property**

**vue实例生命周期**

请参考:[vue实例生命周期.note](note://2EF06ABDB4834C0B98A8BDF519B56A2B)