[toc]
### vue的数据驱动
**背景**
vue 是一个 MVVM 架构模式的视图层框架，采用 `双向绑定`，只需关注数据结构的变化，让 MVVM 框架利用自己的机制去自动更新DOM，从而把开发者从复杂繁琐的DOM操作解放出来。
**原理**
vue 能够做到修改数据，自动更新视图，这个功能主要是利用 `响应式属性` 原理。而什么是 响应式属性，这就要从 js 的属性特性开始说起。

首先 js 的属性主要有两种类型：
**1、值属性（有值的属性）**
具有以下特性：
* `configurable` bollean 可配置性 
* `enumerable` bollean 可枚举性 
* `writable` bollean 可写性 
* `value` any 值

**2、存储器属性（有值的属性）**
具有以下特性：
* `configurable` bollean 可配置性 
* `enumerable` bollean 可枚举性 
* `get` 读取属性时，执行 get 函数
* `set` 设置属性时，执行 set 函数 

vue 的响应式属性则是通过 Object 对象下的方法 `Object.defineProperty ( data , prop ,desc )`去把 vue 实例下的属性修改成 响应式属性。
例：
```html
<input id="box"/>
```
```js
let data = {
    name:'123'//值属性
}
let box = document.getElementById('box');
box.value = data.name;
//给data添加name2存储器属性
Object.defineProperty(data,'name2',{
    get () {
        return this.name;
    },
    set (val) {
        this.name = val;
        box.value = data.name;
    }
})
```
这样data对象实例下多了个name2存储器属性，修改 name2 就可以修改name，而且能自动响应到视图中。这就是 vue 做到修改数据自动响应到视图的原理。
##### Vue的虚拟DOM
其实在修改数据并自动响应到视图这个过程中间，还有一个 虚拟DOM 的操作。在生成 vue 实例过程中，还会伴有 虚拟DOM 树的生成，这是一个JavaScript树对象，结构类似页面节点，这是为了在修改数据时，能在 虚拟DOM 中通过特定的算法去比较，并生成最后的JavaScript树对象，并根据这个最新的对象树去渲染最新的页面节点，相较于以往的jQuery等直接操作页面节点，这个方法能够减少真实DOM操作，极大的提升性能。

**优点**
- 保证性能下限：框架的虚拟DOM 需要适配任何上层API可能产生的操作，它的一些DOM操作的实现必须是普适的，所以它的性能虽然不是最优的，但是比起粗暴的DOM操作，性能还是要好很多，因此 虚拟DOM 至少可以保证在你不需要手动优化的情况下，依然有很不错的性能，保证了性能的下限；
- 无需手动操作DOM：我们不再需要手动去操作DOM，只需写好 view-model 的代码逻辑，框架会根据 虚拟DOM 和 双向数据绑定，帮我们自动更新视图，极大的提高了我们的开发效率
- 跨平台：虚拟DOM 本质上是JavaScript对象，而DOM与平台强相关，相比之下，虚拟DOM可以进行更方便的跨平台操作，例如服务器渲染、weex开发等等。

**缺点**
虽然虚拟DOM足以应对绝大部分应用的性能需求，但在一些性能要求极高的应用中，虚拟DOM无法进行针对性的极致优化。

##### vue为什么要虚拟DOM
其实有了解过vue的同学应该知道，其实vue在实例化时，有通过 watch 去监听，通过数据劫持精准的探测数据在具体的DOM上的变化，那为什么还要用改变的部分去和旧的虚拟DOM树去做对比，再生成新的虚拟DOM树去渲染真实的DOM呢？
其实，现代前端框架有两种侦测变化方式，一种是 pull，一种 push。
- pull：其代表为 react，一般用 setState 去显示更新，然后 react 会进行一层层的 virtual dom diff 操作查找变化，然后 patch 到 dom 上，react 从一开始并不知道哪里有变化，只知道有变化了，然后进行比较暴力的 diff 查找哪发生变化，另外的代表就是 Angular 的脏检查操作。
- push：Vue 的响应式系统则是 push 的代表，当 VUe 程序初始化的时候就会对数据 data 进行依赖的收集，一旦数据发生变化，响应式系统会立刻得知。所以vue是有通过监听知道变化的部分，但是这会产生一个问题，监听需要绑定 watcher ，一旦我们绑定的 细粒度 过高就会产生大量的 watcher ，这会带来内存以及依赖追踪的开销，而 细粒度 过低会无法精准侦测变化，因此 vue 的设计是选择中等细粒度的方案，在组件级别进行 push 侦测，所以 vue 是能侦测发生变化的组件，然后在组件内部进行 virtual dom diff获取更加具体的差异，而 diff 的操作就是pull操作，vue 是 push + pull 结合的方式进行侦测变化的。

**结论**：vue需要 虚拟dom 去实现中等细粒度方案，避免细粒度过高，监听过多导致内存和依赖追踪开销过大。 