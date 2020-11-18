[toc]
## 插槽
#### 插槽内容
Vue 实现了一套内容分发的 API，这套 API 的设计灵感源自 Web Components 规范草案，将 `<slot>` 元素作为承载分发内容的出口。
示例：

```html
  <nav-link>
     hello
  </nav-link>
```
然后在 `nav-link` 的模板：
```html
<a>
    <slot></slot>
</a>
``` 
当组件渲染的时候， `<slot></slot>` 将会被替换为 “hello”。插槽内容包括任何模板代码，包括 HTML，甚至其它的组件：
```html
<nav-link>
    <span>
        hello
    </span>
    <com-span>
        hi 
    </com-span>
</nav-link>
```
上面的内容都会被渲染到，但是如果 `nav-link` 的 `template` 没有 `<slot>` 元素的话，则组件起始标签和结束标签之间的所有内容都会被抛弃掉，不会被渲染。

&nbsp;

#### 编译作用域
在插槽中使用数据：
```html
 <nav-link url='/home'>
    {{ user.name }}
    <!-- 插槽跟模板的其它地方一样，可以访问相同的实例 
    property（也就是作用域），而不能访问 <nav-link> 的作用域。  -->
    {{ url }}
    <!-- 这里的url会是 undefined ，因为其内容是传递给<nav-link>的 -->
 </nav-link>
```
总之一句话，父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。

&nbsp;

#### 后备内容
有时为一个插槽设置具体的后备（也就是默认的）内容，让插槽在没有提供内容的情况下被渲染。
示例：
在一个 sub-btn 组件中：
```html
 <button>
    <slot></slot>
 </button>
```
需要让 sub-btn 组件默认渲染 提交 字样，我们可以将 提交 两个字放在 slot 里面。
```html
 <button>
    <slot>提 交</slot>
 </button>
```
在父组件使用 sub-btn 的时候
```html
 <!-- 不提供任何内容，将会默认渲染提交 -->
 <sub-btn>
 </sub-btn>
 <!-- 提供内容则会只显示提交的内容 -->
 <sub-btn>
    保存
 </sub-btn>
```
&nbsp;

#### 具名插槽
某个组件需要传多个插槽的时候，就需要具名插槽，而使用具名插槽的方法有两种，一种是利用 slot 的 name 属性（Vue 3.0 之后不会支持），还有一种是用 v-slot 指令。
示例：
某个需要多个插槽的组件 `<base-layout>` ，对于这样的情况， `<slot>` 元素有一个特殊的 attribute: name 这个属性可以用来定义额外的插槽：
```html
   <div>
      <header>
      <!-- 这里放页头 -->
      <slot name='header'></slot>
      </header>
      <main>
      <!-- 主要内容 -->
      <slot></slot>
      </main>
      <footer>
      <!-- 这里放页脚 -->
      <slot name='footer'></slot>
      </footer>
   </div>
``` 
不带 name 的 slot 出口会带有隐含的名字“default”
在使用时，我们可以用 `v-slot` 指令在 `<template>` 元素上使用：
```html
<base-layout>
  <template v-slot:header>
      <h1>头部</h1>
  </template>

  <p>具体内容1</p>
  <p>具体内容2</p>

  <template v-slot:footer>
      <h1>结尾</h1>
  </template>
</base-layout>
```
带有 `v-slot` 具名的模板都会进入对应的具名插槽，任何没有被包裹的内容都会被默认为默认插槽的内容。
注意： `v-slot` 只能添加在 `<template>` 上。`v-slot:header` 也可以缩写为 `#header`


