
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