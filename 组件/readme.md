[toc]

## 组件Component
如果说指令是属性，那么组件就是标签。组件是 Vue 最强大的功能之一，组件可以扩展 HTML 元素，封装可重用的代码。组件系统让我们可以用独立可复用的小组件来构建大型应用，几乎任意类型的应用的界面都可以抽象为一个组件树
优点：
  - 代码复用
  - 便于维护

#### 组件定义与使用
组件是可复用的 Vue 实例，带有 name ，它们与 new Vue 对象接受相同的选项，除了没有 el，且 data 属性必须为函数。

###### 组件要求
 - data 属性必须为 Function 类型，因为组件要实现复用
 - 每个组件的 template 必须只有一个根元素
 - 注册时，组件名可以是 kebab-case 或 PascalCase ，但在 html 页面上使用时，必须写成遵循 W3C 规范中的自定义组件名（字母全小写且必须包含一个连字符）

###### 全局组件
使用 Vue.component() 创建的为全剧组件，所有的Vue实例都可以使用
例：
```js
 Vue.component('my-compoment', {
     data: function () {
         return ''
     };
     template: '<div> hello </div>'
 })
```

###### 局部组件
在某个 Vue 实例（组件也是 Vue 实例，可以在组件1中写组件2，但是组件2的标签中只能用在组件1的template的标签中），通过 Vue 实例的配置参数 components 属性注册的组件为全局组件，只有当前实例能使用。
例：
```js
  var child = {
      data () {
          return {
              username: '局部组件1'
          }
      }，
      template:'<p>{{ username }}</p>'
  }
  new Vue({
      el: '#app',
      components: {
          child,
          components: {
            child2:{
              template:'<p>局部组件2</p>'
            }
          }
      }
  })
```