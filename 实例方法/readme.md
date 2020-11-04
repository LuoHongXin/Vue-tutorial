
[toc]
## Vue 的实例方法
#### 数据
 - **vm.$watch(expOrFn,callback,[options])**
 
    **参数**：
    * `{string | Function} expOrFn`
    * `{Function | Object} callback`
    * `{Object} [options]`
      - `{boolean} deep`
      - `{boolean} immediate`

   **返回值**：`{Function} unwatch`

   **用法**：
   监听 Vue 实例上的一个表达式或者一个函数计算结果的变化，回调函数得到的参数为新值和旧值，表达式只接受简单的键路径，对于更复杂的表达式，用一个函数取代。
   ```注意：在变更（不是替换）对象或数组时，旧值将于新值相同（newVal === oldVal）,因为它们的引用指向同一个对象/数组。Vue不会保留变更之前值的副本```

   **示例**：
   ```js
     //键路径
     vm.$watch('a.b.c',function (newVal, oldVal) {

     })

     //函数
     vm.$watch(function () {
         //每次结果不同时，回调函数都会被调用
         return this.a + this.b
     }, function (newVal, oldVal) {

     })
   ```
   `vm.$watch` 返回一个取消观察函数，用来停止触发回调：
   ```js
    var unwatch = vm.$watch('a',function(newVal, oldVal){});
    //取消监听
    unwatch();
   ```

   **选项**：deep
   为了发现对象内部值的变化，可以在选项参数中指定 `deep: true`。注意监听数组的变更不需要这么做。
   ```js
   vm.$watch('someObject',callback,{
       deep: true
   })
   vm.someObject.someValue = 111//触发callback函数
   ```

   **选项**：immediate
   在选项参数中指定 ```immediate: true``` 将立即以表达式的当前值触发回调：
   ```js
   vm.$watch('a', callback, {
       immediate: true
   })
   ```
&nbsp;

- **vm.$set(target,propertyName/index,value)**
    **参数**：
    * `{Object | Array} target`
    * `{string | number} propertyName/index`
    * `{any} value`

    **返回值**：设置的值
    **用法**：这是全局 `Vue.set` 的别名，设置响应性属性

&nbsp;

- **vm.$delete(target,propertyName/index)**
    **参数**：
    * `{Object | Array} target`
    * `{string | number} propertyName/index`
    **用法**：
    这是全局 `Vue.delete` 的别名，去除响应式属性

&nbsp;

#### 事件
 - **vm.$on(event,callback)**
    **参数**：
     * `{string | Array<string>} event`
     * `{Function} callback`

    **用法**：
     监听当前实例上的自定义事件，事件可以由 `vm.$emit` 触发。回调函数会接收所有传入事件触发函数的额外参数。
    **示例**：
    ```js
     vm.$on('test', function (msg) {
         console.log(msg)
     })
     vm.$emit('test', 'hi')// => hi
    ``` 

&nbsp;

 - **vm.$once(event,callback)**
    **参数**：
     * `{string} event`
     * `{Function} callback`

    **用法**：
     监听一个自定义事件，但是只触发一次。一旦触发之后，监听器就会被移除。

&nbsp;

 - **vm.$off(event,callback)**
    **参数**：
     * `{string | Array<string>} event`
     * `{Function} callback`

    **用法**：
     移除自定义事件监听器。
     * 若没有提供参数，则移除所有的事件监听器；
     * 若只提供了事件，则移除该事件所有的监听器；
     * 如果同时提供了事件与回调，则只移除这个回调的监听器。

&nbsp;

 - **vm.$emit(eventName,[...args])**
    **参数**：
     * `{string} eventName`
     * `[...args]`
    触发当前实例上的事件，附加参数[...args]都会传给监听器的回调。

&nbsp;

#### 生命周期
 - **vm.$mount([elementOrSeletor])**
    **参数**：
     * `{Element | string} [elementOrSeletor]`
     * `{boolean} [hydrating]`

    **返回值**：`vm` 实例自身

    **用法**：
     如果 Vue 实例在实例化时没有收到 el 选项，则它处于“未挂载”状态，没有关联的 DOM 元素。可以使用 `vm.$mount()` 手动挂载一个未挂载的实例。
     如果没有提供 `elementOrSelector` 参数，模板将被渲染为文档之外的元素，并且你必须使用 DOM API 把它插入文档中。
     这个方法返回实例自身，因而可以链式调用其它实例方法。
    **示例**：
    ```js
     var VueDom = Vue.extend({
         template: '<div>Hello!</div>'
     })
     // 在实例创建时，利用el属性挂载（替换掉 #app）
     new VueDom({
         el: '#app'
     })
     // 创建实例后，手动挂载（替换掉 #app）
     new VueDom().$mount('#app')
     // 在文档渲染之外渲染并随后挂载
     var myVueDom = new VueDom().$mount();
     document.getElementById('app').appendChild(myVueDom.$el);
    ``` 

&nbsp;

- **vm.$forceUpdate()**
    **示例**：
    迫使 Vue 实例重新渲染。注意它仅仅影响实例本身和插入插槽内容的子组件，而不是所有子组件。

&nbsp;

- **vm.$nextTick([callback])**
    **参数**：
    * `{Function} [callback]`
   
    **用法**：
    将回调延迟到下次 DOM 更新循环之后执行。在修改数据之后立即使用它，然后等待 DOM 更新。它跟全局方法 Vue.nextTick 一样，不同的是回调的 this 自动绑定到调用它的实例上。

&nbsp;

- **vm.$destory()**
    **用法**：
    完全销毁一个实例。清理它与其它实例的连接，解绑它的全部指令及其事件监听器。触发 `beforeDestroy` 和 `destroyed` 的钩子。
