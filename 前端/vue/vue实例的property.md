# vue实例的property
## vm.$data

类型：==Object==

Vue 实例观察的数据对象。Vue 实例代理了对其 data 对象 property 的访问。

## vm.$props

类型：==Object==

当前组件接收到的 props 对象。Vue 实例代理了对其 props 对象 property 的访问。

## vm.$el
类型：Element

**只读**,Vue 实例使用的根 DOM 元素。

## vm.$options
类型：Object

**只读**,用于当前 Vue 实例的初始化选项。

## vm.$parent
类型：==Vue instance==

**只读**,父实例，如果当前实例有的话。

## vm.$root
类型：==Vue instance==

**只读**,当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自己。

## vm.$children

类型：==Array<Vue instance>==

**只读**,当前实例的**直接子组件**。

#### 注意
> $children 并不保证顺序，也不是响应式的。
> 
> 如果你发现自己正在尝试使用 $children 来进行数据绑定，考虑使用一个数组配合 v-for 来生成子组件，并且使用 Array 作为真正的来源。

## vm.$slots(待学习)

类型：=={ [name: string]: ?Array<VNode> }==

**只读**,**无响应性**

vm.$slots用来访问被插槽分发的内容。

每个具名插槽有其相应的 property
default property 包括了所有没有被包含在具名插槽中的节点，或 v-slot:default 的内容。

## vm.$scopedSlots

类型：=={ [name: string]: props => Array<VNode> | undefined }==

- **只读**
- 用来访问作用域插槽。对于每一个插槽，该对象都包含一个返回相应 VNode 的函数。
- vm.$scopedSlots 在使用渲染函数开发一个组件时特别有用。

#### 注意：从 2.6.0 开始，这个 property 有两个变化：

> 作用域插槽函数现在保证返回一个 VNode 数组，除非在返回值无效的情况下返回 undefined。
> 
> 所有的 $slots 现在都会作为函数暴露在 $scopedSlots 中。
> 

## vm.$refs(待学习)

类型：==Object==

- **只读**
- 一个对象，持有注册过 ref attribute 的所有 DOM 元素和组件实例。

#### 注意
> $refs不能在created生命周期中使用,因为在组件创建时候 该ref还没有绑定元素

## vm.$isServer(待学习)
类型：==boolean==

- **只读**
- 当前 Vue 实例是否运行于服务器。

## vm.$attrs(待学习)

类型：=={ [key: string]: string }==

- **只读**
- 包含了父作用域中不作为 prop 被识别 (且获取) 的 attribute 绑定 (class 和 style 除外)。
- 可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。

## vm.$listeners(待学习)

类型：=={ [key: string]: Function | Array<Function> }==

- **只读**
- 包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。
- 可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用。
