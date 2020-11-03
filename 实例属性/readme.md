[注意：因为 Markdown 对 `$` 字符的不友好，所有部分$前面会有 \ 符号，观看时可直接忽略该符号]
[toc]
## Vue 的实例属性
#### 属性
 - **vm.$data**
    **类型**： `Object`
    **详细**：
    通过该属性可以访问到当前实例的 data 数据对象 

&nbsp;

 - **vm.$props**
   **类型**： `Object`
   **详细**：
   通过该属性可以访问到当前实例接收到的 props 对象

&nbsp;

 - **vm.$el**（只读）
   **类型**： `Element` 
   **详细**：
   Vue 实例使用的根元素。

&nbsp;

 - **vm.$options**（只读）
   **类型**： `Object`
   **详细**：
   通过该属性，可以访问当前实例的实例化选项

&nbsp;

- **vm.$parent**（只读）
  **类型**： `Vue instance`
  **详细**：
  当前实例的父实例，若存在父实例的话。

&nbsp;

- **vm.$root**（只读）
**类型**： `Vue instance`
**详细**：
当前组件的根 Vue 实例，若没有父实例，则会是自己。

&nbsp;

- **vm.$children**（只读）
**类型**： `Array<Vue instance>`
**详细**：
当前实例的直接子组件。不保证顺序，也不是响应式的。

&nbsp;

- **vm.$slots**（只读）
**类型**： `{ [name: string]: ?Array<VNode> }`
**详细**：
用来访问被 插槽分发 的内容。每个 具名插槽 有其相应的 property （例： `v-slot:foo` 中的内容将会在 `vm.\$slots.foo` 中查询到）。default property 包括了所有没有被包含在具名插槽中的节点，或 v-slot:default 的内容。

&nbsp;

- **vm.$refs**（只读）
**类型**： `Object`
**详细**：
一个对象，持有注册过 ref 属性的所有 DOM 元素和组件实例。一般用于父组件调用子组件的方法或获取子组件的属性

&nbsp;

- **vm.$isServer**（只读）
**类型**： `boolean`
**详细**：
当前 Vue 实例是否运行于服务器，该属性一般用于服务器渲染。

&nbsp;

- **vm.$attrs**（只读）
**类型**： `{ [key: string]: string }`
**详细**：
包含了父作用域中不作为 prop 被识别（且获取）的 attribute 绑定（class 和 style 除外）。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定，并且可通过 `v-bind="\$attrs"` 传入内部组件

&nbsp;

- **vm.$listeners**（只读）
**类型**： `{ [key: string]: Function | Array<Function> }`
**详细**：
包含了父作用域的（不含 `.native` 修饰器的）`v-on` 事件监听器。它可以通过 `v-on="\$listeners"` 传入内部组件。