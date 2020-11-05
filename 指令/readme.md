[toc]
## 指令
#### v-text
- 预期：`string`
- 详细：
更新元素的 `textContent`。如果要更新部分的 `textContent`，需要使用 `{{ mustache }}` 插值。
- 示例：
```html
<span v-text='msg'></span>
<!--和下面一样-->
<span>{{msg}}</span>
```

#### v-html
- 预期：`string`
- 详细：
更新元素的 `innerHTML`。注意：内容按普通 HTML 插入 - 不会作为 Vue 模板进行编译。如果试图使用 `v-html` 组合模板，可以重新考虑是否通过使用组件来替代。
- 注意：
在网站动态渲染任意 HTML 是非常危险的，因为容易导致 XSS 攻击。只可在可信内容上使用 `v-html`，永不用在用户提交的内容上。
在 单文件组件 里，`scoped` 的样式不会应用在 `v-html` 内部，因为那部分 HTML 没有被 Vue 的模板编译器处理。如果你希望针对 `v-html` 的内容设置带作用域的 CSS，你可以替换为 `CSS Modules` 或用一个额外的全局 `<style>` 元素手动设置类似 BEM 的作用域策略。
- 示例：
`<div v-html="html"></div>`

#### v-show
- 预期：`any`
- 用法：
根据表达式之真假值，切换元素的 `display` CSS property。

#### v-if
- 预期：`any`
- 用法：
根据表达式的真假值来有条件的渲染元素。在切换时元素及它的数据绑定/组件被销毁重建。如果元素时 `<template>`，将提出它的内容作为条件块。
- 注意：
不要和 v-if 用在同一个元素，v-for 的优先级比 v-if 更高，这意味着 v-if 将分别重复运行于每个 v-for 循环中。

#### v-else
- 不需要表达式
- 限制：前一兄弟元素必须有 v-if 或 v-else-if

#### v-else-if
- 类型：`any`
- 限制：前一兄弟元素必须有 v-if 或 v-else-if

#### v-for
- 预期：`Array | Object | number | string | Iterable`
- 用法：
基于源数据多次渲染元素或模板块。此指令之值，必须使用特定语法 `alias in expression`，为当前遍历的元素提供别名：
```html
<div v-for="(item, index) in items">
    {{ item.text }}
<div>

<div v-for="(val, key) in object">
<div>

<div v-for="(val, name, index) in object">
<div>
```
`v-for` 的默认行为会尝试原地修改元素而不是移动它们。要强制其重新排序元素，你需要用特殊的属性 `key` 来提供一个排序提示
从 2.6 起，v-for 也可以在实现了 可迭代协议 的值上使用，包括原生的 Map 和 Set 。不过不支持可响应的 Map 和 Set 值，所以无法自动探测变更。

#### v-on
- 缩写：`@`
- 预期：`Function | Inline Statement | Object`
- 参数：`event`
- 修饰符：
  * .stop - 调用 event.stopPropagation()。
  * .prevent - 调用 event.preventDefault()。
  * .capture - 添加事件侦听器时使用 capture 模式。
  * .self - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
  * .{keyCode | keyAlias} - 只当事件是从特定键触发时才触发回调。
  * .native - 监听组件根元素的原生事件
  * .once - 只触发一次回调
  * .left - 只当点击鼠标左键时触发
  * .right - 只当点击鼠标右键时触发
  * .middle - 只当点击鼠标中间时触发
  * .passive - 以 { passive:true } 模式添加侦听器

#### v-bind
- 缩写：`:`
- 预期：`any (width argument) | Object (widthout argument)`
- 参数：`attrOrProp (optional)`
- 修饰符：
* .prop - 作为一个 DOM property 绑定而不是作为 attribute 绑定
* .camel - 将 kebab-case attribute 名转换为 camelCase
* .sync 语法糖，会扩展成一个更新父组件绑定值的 v-on 侦听器。

#### v-model
- 预期：随表单控件类型不同而不同。
- 限制：
* `<input>`
* `<select>`
* `<textarea>`
* components
- 修饰符：
* .lazy - 取代 `input` 监听 `change` 事件
* .number - 输入字符串转为有效的数字
* .trim - 输入首位空格过滤
- 用法
在表单控件或组件上实现双向绑定。v-model(双向数据绑定) 等同于 单向绑定 + 事件 （v-bind + v-on） 

#### v-slot
- 缩写：`#`
- 预期：可放置在函数参数位置的 JavaScript 表达式（在 支持的环境下 可使用解构）。可选即只需要为插槽传入 prop 的时候使用。
- 参数：插槽名（可选，默认值是 default）
- 限用于
* `<template>`
* 组件
- 用法：
提供具名插槽或需要接受 prop 的插槽
- 示例：
```html
<!-- 具名插槽 -->
<base-layout>
    <template v-slot:header>
    header
    </template>
    <div>
    body...
    </div>
    <template v-slot:footer>
    footer
    </template>
</base-layout>
<!-- 具名插槽 -->

<!-- 接受 prop 的具名插槽 -->
<int-box>
    <template v-slot:item='slotProps'>
        <div>
            {{ slotProps.text }}  
        </div>  
    </template>
</int-box>
<!-- 接受 prop 的具名插槽 -->
```  
```html
<!-- base-layout组件 -->
<div>
    <header>
    <slot name='header'>
    </slot>
    </header>
    <main>
    <slot>
    </slot>
    </main>
    <footer>
    <slot name='footer'>
    </slot>
    </footer>
</div>
<!-- base-layout组件 -->

<!-- int-box组件 -->
<div>
    <slot v-bind:user='item'>
        {{ user.text2 }}
    </slot>
</div>
<!-- int-box组件 -->
```

#### v-pre
- 不需要表达式
- 用法：
跳过这个元素和它的子元素的编译过程。可以用来显示原始 Mustache 标签。跳过大量没有指令的节点会加快编译。
- 示例：
```html
    <span v-pre>
    {{ this will not be compiled }}
    </span>
```

#### v-cloak
- 不需要表达式
- 用法：
这个指令保持在元素上直到关联示例结束编译。和 CSS 规则如 `[v-vloak] { display: none }` 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。
- 示例：
```css
[v-cloak] {
    display:none;
}
``` 
```html
<div v-cloak>
    {{ message }}
</div>
```

#### v-once
- 不需要表达式
- 详细：
只渲染元素和组件一次。随后的重新渲染，元素/组件及所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。 
