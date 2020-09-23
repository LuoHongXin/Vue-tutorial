
## vue的实例化选项
[TOC]
#### DOM
- **el**
**类型**：`string | Element`
**限制**：只有在 new 创建实例时生效
**详细**：
锁定页面已存在的一个 dom 元素作为 vue 实例的挂在目标，可以是 CSS 选择器，也可以是一个 HTMLElement 实例。
挂载之后可以用 vue 实例属性 ```vm.$el``` 访问。
如果在实例化时存在这个选项，实例将立即进入编译阶段，否则，需要显示调用 ```vm.$mount(el)``` 手动开启编译。
例如，脚手架搭建的 vue 实例化是没有 el：
```js
new Vue({
  store,
  router,
  render: h => h(App),
}).$mount('#app')
```
**注意**：
1、锁定的挂载元素，会被 Vue 生成的 DOM （render，template）替换，所以不推荐挂载元素为 ```<html>``` 或 ```<body>```
2、若 ```render``` 函数和 ```template``` 属性都不存在，挂载的 DOM 元素的 HTML 会被提取出来用作模板，此时，必须使用 Runtime + Compiler 构建的 Vue库。
补充：vue 框架有两种编译模式：```Compiler```（模板）模式和 ```Runtime``` （运行时）模式。
在 vue 的安装与引入中，通过 ```<script>``` 引入的是完整版模式 （ Runtime + Compiler ）即两种模式都有。
而通过 vueCli 脚手架搭建的，默认只有 ```Runtime（运行时）``` 模式，因为加上 ```Compiler 模式``` ，打包后会比原来大 10kb 左右，而且一般不用 ```template``` 进行替换，所以不需要 Compiler 模式。若需要在脚手架搭建的 vue 实例中使用 template，需要添加 vue.config.js文件，该文件会覆盖 vueCli 里面的配置文件
```js
module.exports = {
    //增加编译器，让组件支持 template 属性
    runtimeCompiler:true
}
```
&nbsp;
- **template**
**类型**：`string`
**详细**：
template 是一个字符串模板作为 Vue 的实例。模板将会 替换 挂载的元素。挂载元素的内容也都会被忽略掉，除非模板的内容有分发插槽。
如果值是以 ```#``` 开始，则它会像 el 一样用作选择符。
**注意**：
1、出于安全考虑，你应该只使用你信任的 Vue 模板。
2、如果有 render 渲染函数，则 template 渲染模板会被忽视。
&nbsp;
- **render**
**类型**：function | (createElement: () => VNode) => VNode
**详细**：字符串模板的代替方案，允许你发挥 JavaScript 最大的编程能力。该渲染函数接收一个 ```createElement``` 方法作为第一个参数用来创建 ```VNode``` 。
如果组件是一个函数组件，渲染函数还会接收一个额外的 ```context``` 参数，为没有实例的函数组件提供上下文信息。
&nbsp;
- **renderError**
**类型**：`function | (createElement: () => VNode, error: Error) => VNode`
**详细**：
2.2.0新增，只在开发者环境下工作。
当 render 函数错误时，提供另外的一种渲染输出。其错误会作为第二个参数传递到 renderError 。

**总结**
实例化过程中，程序会找到挂载点 el 所在根元素及其内容，如果 template 有内容，那么会忽略挂载点内容，并用 template 的内容会替代整个挂载点显示，若 render 存在，则 render 函数渲染的内容会代替 el 和 template 的内容。
```render > template > el```

#### 数据
- **data**
**类型**：`Object | Function`
**限制**：Vue实例组件的定义只接受 function。
**详细**：
Vue 实例的数据对象。Vue 实例化时，将会递归 data 的 property 转换为 getter/setter ，从而让 data 的 property 能够响应数据变化。 对象形式则必须是纯粹的对象（因为只会将对象第一层设为响应式属性，若对象里还有对象这种，那么第二层对象则不是响应式属性），浏览器 API 创建的原生对象，原型上的 property 会被忽略。data 不推荐观察拥有状态行为的对象，应该只是单纯的数据。
&nbsp;
一旦实例化之后，你就无法在根 vue 实例数据上添加响应式 property 。就算是 vue 的全局API `Vue.set` 或 `Vm.$set` 也只是基于 data 已有的第一层数据，将第二层设置为响应式 property ，但是无法添加新的属性作为响应式属性。因此推荐创建实例之前，就声明所有的根级响应式 property。
&nbsp;
创建实例之后，可以通过 `vm.$data` 访问原始数据对象。因为 vue 实例代理了 data 对象上所有的 property ，所以访问 `vm.a` 等同于 `vm.$data.a` 。
&nbsp;
而以 `_` 或 `$` 开头的 property 不会被 Vue 实例代理，因为它们可能和Vue内置的 property 、API 方法冲突，最好用 `vm.$data._property`
&nbsp;
当一个 vue 实例作为一个组件被定义时， `data` 必须声明为返回一个初始数据对象的函数，因为组件可能被用来创建多个实例。如果 `data` 任然是一个对象，则所有的实例会 `共享引用` 同一个数据对象，容易造成数据冲突！而通过函数的形式，利用函数内局部作用域分离的方法，返回一个初始数据的一个全新副本数据对象，避免 `共享引用` 带来的问题。
&nbsp;
- **props**
**类型**：`Array <string> | Object`
**详细**：
props 用于接收来自父组件所传的数据，可以是简单的数组，也可以是对象，对象允许配置高级选项，如 类型检测、自定义验证和设置默认值。
&nbsp;
你可以基于对象的语法使用以下选项：
`type` ：原生构造函数中的一种：`String` 、 `Number` 、`Boolean` 、`Array` 、 `Object` 、 `Date` 、 `Function` 、 `Symbol` 、 任何自定义的构造函数 、 多个类型的时候可用数组表示。接受 props 数据时，会检查是否符合给定的类型，否则抛出警告。
**例子**：
 ```js
  props: {
        //检测类型
        treeData: Array ,
        // 检测类型 + 其它验证
        deviceType: {
            type: Number,
            default: function () {
                return 2;
            },
            required: true,
            validator: function (value) {
                return value >= 0
            }
        }
    }
  
 ```
&nbsp;
 - **propsData**
 **类型**：`{ [key: string]: any }`
 **限制**：只用于 `new` 创建的实例中，组件不可使用。
 **详细**：
 创建实例时传递props，一般很少用，主要作用是方便测试。
&nbsp;
 - **computed**
 **类型**：`{ [key: tring]: Function | { get: Function, set: Function } }`
 **详细**：
 计算属性并被混入到 Vue 实例中。所有 getter 和 setter 的 this 上下文自动绑定 Vue 实例。
 &nbsp;
 注意如果你为一个计算属性使用了箭头函数，则 this 不会指向这个组件的实例，不过你仍然可以将其实例作为函数的第一个参数来访问。
    ```js
    computed: {
        abc: vm => vm.a * 2
    }
    ```
    计算属性的结果会被缓存，除非依赖的响应式 property 变化才会重新计算。注意，如果某个依赖（比如非响应式 property）在该实例范畴之外，则计算属性是不会被更新的。
**示例**：
    ```js
    var vm = new Vue({
        data: { a: 1},
        computed: {
            //仅读取
            aa: function () {
                return this.a * 2
            },
            //读取和设置
            bb: {
                get: function () {
                    return this.a + 1
                },
                set: function (v) {
                    this.a = v - 1
                }
            }
        }
    })
    vm.bb //2
    vm.bb = 3
    vm.a //2
    vm.aa //4
    ```
&nbsp;
- **methods**
**类型**：`{ [key: string]: Function }`
**详细**：
methods 将会混入到 Vue 实例中。可以直接通过 VM 实例访问这些方法，或者在指令表达式中使用。方法中的 `this` 自动绑定为 Vue 实例。
注意，不应该用箭头函数来定义 methods 函数，因为箭头函数绑定的是父级作用域的上下文，所以 this 将不会安装期望指向 Vue 实例。
&nbsp;
- **watch**：
**类型**：`{ [key:string]:string | Function | Object | Array }`
**详细**：
一个对象，键是需要观察的表达式，值是对应回调函数。值也可以是方法名，或者包含选项的对象。Vue 实例将会在实例化时调用 `$watch()` ，通过 watch 监听对象的每一个  property。
**示例**：
  ```js
  var vm = new Vue({
      data: {
          a: 1,
          b: 2,
          c: 3,
          d: 4,
          e: {
              f: {
                  g: 5
              }
          }
      },
      watch: {
          a: function (val, oldval){
              console.log(`new: %s, old: %s`, val, oldval);
          },
          //方法名
          b: 'someMethod',
          // 该回调会在任何被侦听的对象的 property 改变时被调用，不论其被嵌套多深
          c: {
              pro: function (val, oldval) {},
              deep: true
          },
          // 该回调将会在侦听开始之后被立即调用
          d: {
              pro: 'someMethod',
              immediate: true
          },
          // 可以传入回调数组，它们会被逐一调用
          e: [
              'handle1',
              function handle2 (val, oldval){},
              {
                  handler: function handle3 (val, oldval) {},
              }
          ],
          'e.f':function (val, oldval) {}
      }
  })
  ```

#### 生命周期
 <img src='../assets/lifecycle.png'>

- **beforeCreate**
  这是在实例初始化之后，数据观测 （data observer）和 event / watcher 事件配置之前被调用。
- **created**
 实例创建完成后立即被调用。这时，实例已完成：数据观测（data oberver），property 和 方法的运算，watcher / event 事件回调。然而，挂载阶段还没开始。
- **beforeMount**
在挂载开始之前被调用，即在 render 渲染之前。
该钩子在服务器端渲染期间不被调用。
- **mounted**
实例挂载后调用，这时 el 被新创建的 `vm.$el` 替换了。
注意，`mounted` 不能保证所有的子组件也都一起完成挂载。如果你希望等到整个视图渲染完毕，可以在 `mounted` 内部用 `vm.$nextTick`:
    ```js
    mounted: funciton () {
        this.$nextTick (function () {})
    }
    ```

- **beforeUpdate**
数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的时间监听器。
该钩子函数服务器端渲染期间不被调用，因为只有初次渲染会在服务端进行。
- **updated**
由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。
当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用 `计算属性` 或 `watcher` 代替。
注意 updated 不能保证所有子组件也都一起被重绘。如果你希望等到整个视图都重绘完毕，可以在 `updated` 里使用 `vm.$nextTick`：
    ```js
    updated: function () {
        this.$nextTick(function () {})
    }
    ```
    该钩子在服务器渲染期间不被调用。
- **activated**
被 keep-alive 缓存的组件激活时调用。
该钩子在服务器端渲染期间不被调用。
- **deactivated**
被 keep-alive 缓存的组件停用时调用。
该钩子在服务器渲染期间不被调用。
- **beforeDestroy**
实例销毁之前调用，这个时期的实例仍然完全可用。
该钩子在服务器端渲染期间不被调用。
- **destroyd**
实例销毁后调用。该钩子被调用后，对应 Vue 实例的所有指令都被解绑，所有的事件监听器被移除，所有的子实例也都被销毁。
该钩子在服务器端渲染期间不被调用。
- **errorCaptured**
2.5.0+新增
类型： `（err: Error, vm: Component, info: string） => ?boolean`
当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例、包含错误来源信息的字符串。可返回 false 以阻止该错误继续向上传播。

#### 资源
- **directives**
**类型**：`Object`
自定义指令
全局自定义指令示例：
    ```js
    //注册一个全局的自定义指令 'v-focus'
    Vue.directive('focus', {
        // 当被绑定的元素插入到 DOM 中时执行
        inserted: function (el) {
            //聚焦元素
            el.focus()
        }
    })
    ```
    注册局部指令，组件中可接受一个 `directives` 的选项：
    ```js
    directves: {
        focus: {
            inserted: function (el) {
                el.focus()
            }
        }
    }
    ```
    在模板中调用 v-focus 指令：
    ```html
    <input v-focus>
    ```
    **指令的钩子函数**
    一个指令对象可提供如下几个钩子函数（均为可选）：
    
    - `bind` ：只调用一次，指令第一次绑定到元素时被调用。在这里可以进行一次性的初始化设置。
    - `inserted` ：被绑定的元素插入父节点时调用（仅保证父节点存在，但不一定已被插入文档中）。
    - `update` ：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新
    - `componentUpdated` ：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
    - `unbind` ：只调用一次，指令与元素解绑时调用。
    
    **指令钩子函数的参数**
    指令钩子函数会被传入以下参数：
    
    - `el` ：指令所绑定的元素，可以用来直接操作 DOM。 
    - `binding` ：一个对象，包含以下属性：
        
        - `name` ：指令名，不包含 `v-` 前缀。  
        - `value`：指令的绑定值，例如： `v-mydirective="1 + 1"` 中，绑定值为 2。 
        - `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
        - `expression` ：字符串形式的指令表达式。例如： `v-mydirective="1 + 1"` 中，表达式为 `1 + 1`
        - `arg` ：传给指令的参数，可选。例如： `v-mydirective: foo` 中，参数为 foo
        - `modifiers`：一个包含修饰符的对象。例如：`v-my-directive.foo.bar` 中修饰符对象为 `{
            foo: true, bar: true
        }` 。
    - `vnode` ：Vue编译生成的虚拟节点。
    - `oldVnode` ： 上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。 
    以上参数中，除了 `el` 之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过 `dataset` 来进行。

- **filters**
**类型**： ``Object``
自定义过滤器，可被用于一些常见的文本格式化。过滤器可用在两个地方：双花括号插值 和 v-bind 表达式（后者从 2.1.0+ 开始支持）。过滤器应该被添加在 JavaScript 表达式的尾部，由"管道"符号指示：
    ```html
    <!-- 双花括号 -->
    {{ message | formatMessage }}
    <!-- v-bind -->
    <div v-bind:id="message | formatMessage"></div>
    ```
   在组件的选项 filters 中定义本地的过滤器：
   ```js
    filters: {
        formatMessage: function (value) {
            if (!value) return ''
            value = value.toString()
            return value.charAt(0).toUpperCase() + value.slice(1)
        }
    }
   ```
- **components**
**类型**：`Object`
组件，vue重要的概念，内容很多可看组件章节

