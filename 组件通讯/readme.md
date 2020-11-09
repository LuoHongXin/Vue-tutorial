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
        props: ['addData'],//先接受，再调用
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
            add () { // 不用接收，可直接调用
                this.$emit('addData',this.name);
            }
        }
    }
    ```


  - 用事件修饰符 'sync' (简单数据可采用方案)
    * v-bind:xx.sync = '要传的父组件属性'
    * this.$emit('update:xx',子组件传的值) 
    
    示例：
    父组件
    ```html
    <btn-change :color.sync="color">
    </btn-change>
    ```
    子组件
    ```js
    methods: {
        changeColor () {
            this.$emit('update:color','red')
        }
    }
    ```
   ###### 兄弟组件通讯
   核心思想：利用兄弟组件之间的共同父级组件进行通讯
  
&nbsp;
#### 无关联/多层组件间通讯
###### 中间组件
利用一个Vue实例 bus (也可以用根组件) 作为中间件桥梁实现传参
- 接收方（组件B）：监听 bus 的自定义事件 bus.$on('自定义事件名',自定义事件函数)
- 传输方（组件A）：触发 bus 的自定义事件 bus.$emit('自定义事件名',要传的参数)
示例：
    先创建一个中间组件，可以是根组件，最好是全局的组件
    ```js
    let bus = new Vue();
    ```
    组件A 传值给 组件B
    组件A
    ```js
    let comA = {
       data () {
           return {
               msg: 'i am A'
           }  
       },
       methods: {
           send () {//组件A使用send函数时，会触发bus实例中remove事件，并传参 msg
               bus.$emit('remove',this.msg)
           }
       }
    }
    ```
    组件B
    ```js
    let comB = {
        data () {
            return {
                name:''
            }
        },
        mounted () {
            bus.$on('remove',val => this.name = val);
        }
    }
    ```
###### Vuex
  也可以利用 Vuex，其实 Vuex 的仓库 store 就类似中间组件，不过它不用创建 Vue 实例，还有很多方法去管理各组件间的数据，不用把所有数据和方法挂载在中间件下，造成臃肿或是创建过多 Vue 实例作为中间件，具体的 Vuex 的学习和使用就去看 Vuex 章节。