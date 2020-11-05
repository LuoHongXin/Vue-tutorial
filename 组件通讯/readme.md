[toc]

## 组件通讯
- 遵循原则
  * 状态提升：把数据放在操作数据组件的共同父级
  * 谁的数据谁修改

#### 有关联组件通讯
###### 父组件 传 子组件（props）
  组件实例的作用域是孤立的，这意味着不能（也不应该）在子组件的模板内直接引用父组件的数据。要让子组件使用父组件的数据，需要通过子组件的 props 选项。
  props 传递是单向的
  操作：
  父组件：给子组件标签用 v-bind 设置需要传递的属性
  子组件：利用 props 接收父组件通过 v-bind 传来的值
  示例：

```html
<!-- 父组件 -->
<child-copm v-bind:data = 'dataList'></child-copm>
```

```js
// 子组件
{
    name: 'childCopm',
    props: ['data']
}
```

 ###### 子组件 传 父组件（子组件利用父组件传过来的方法来进行传参）
  - 用 v-bind 和 props 
    示例：
    ```html
    <!-- 父组件 -->
    <child-copm v-bind:add-data = 'addData'></child-copm>

    {
        methods: {
            addData (val) {
                this.data = val;
            }
        }
    }
    ```
    ```js
    // 子组件
    {
        name: 'childCopm',
        props: ['addData'],
        methods: {
            add () {
                this.addData(this.name);
            }
        }
    }
    ```

  - 用 v-on 和 $emit
     示例：
    ```html
    <!-- 父组件 -->
    <child-copm v-on:add-data = 'addData'></child-copm>

    {
        methods: {
            addData (val) {
                this.data = val;
            }
        }
    }
    ```
    ```js
    // 子组件
    {
        name: 'childCopm',
        methods: {
            add () {
                this.$emit('addData',this.name);
            }
        }
    }
    ```
  - 用事件修饰符 'sync' (简单数据可采用方案)
    * v-bind:xx.sync = '要传的父组件属性'
    * this.$emit('update:xx',子组件传的值) 