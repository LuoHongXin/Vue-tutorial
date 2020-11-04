
## 响应式属性
总所周知，vue 作为一个 MVVM 视图层框架，它有个核心的功能，就是能根据数据的修改去自动更新视图，而 vue 实例下，那些能够被修改，然后自动响应的属性，我们就称为 `响应式属性`。
其实在 vue 的实例化过程中，Vue 的机制会遍历 data 下所有的属性，并通过 `Object.defineProperty()`  把它们设置为 `存储器属性` ，并写入 Vue 的实例中（在 Vue 3.0 之后就不是用defineProperty 去设置响应式属性了，而是用 proxy ，具体的介绍可看 Vue3.0 的学习，这里主要还是以 Vue 2.x 版本作为学习背景）

#### 属性特性
首先，我们要了解 JavaScript 基础的属性特性，属性特性主要分为两种，一种是 `值属性` (有值的属性)，一种是 `存储器属性` (没有值的属性)

- 值属性
  * configurable - 可配置性 - Bollean - 控制所有属性的开关
  * writable - 可写性 - Bollean
  * enumerable - 可枚举性 - Bollean
  * value - 值 - any
  &nbsp;
- 存储器属性
  * configurable - 可配置性 - Bollean - 控制所有属性的开关
  * enumerable - 可枚举性 - Bollean
  * get - 读取属性时，执行 get 函数
  * set - 设置属性时，执行 set 函数，并传入修改的值

&nbsp;
针对于这些属性特性，JavaScript 也提供了一些方法：

- Object.defineProperty(data, prop, descriptor) 
  用来修改/添加属性特性
  data - 需要修改/添加的目标对象
  prop - 修改/添加的属性
  descriptor - 属性特性
&nbsp;
- Object.getOwnpropertyDescriptor(data, prop)
  用来查看属性特性

注意：
1、传统方式创建的属性，属性特性默认为 true
2、通过 Object.defineProperty(target, prop, descriptor) 创建的属性，属性特性默认为 false
&nbsp;
示例：
```js
// 声明对象，直接创建属性（传统方式）
let data = {
    // 值属性
    name: '小明',
    score: 80,
    // 存储属性
    get score() {
        return this._score // 写score会出现 Maximum call stack size exceeded 情况，所以用一个 _score 来作为中间值保证不进入死循环
    },
    set score(val) {
        this._score = val;
    }
}

// 利用 Object.defineProperty 为普通对象 data 创建属性
// 为 data 对象创建 存储属性 showname
Object.defineProperty(data, 'showname', {
    get () {
        return this.name
    },
    set (val) {
        this.name = val;
        app.innerHTML = val;//Vue里面响应式属性的原理
    }
})
```
以上就是响应式属性的原理，而在vue中，我们通过会想要把一些数据作为响应式属性来使用。
#### 如何添加响应式属性
- **实例化时配置data**（只有第一层作为响应式属性）
下例中 data 中的第一层属性 username 和 score 就是响应式属性（可添加第一层属性作为响应式属性），但是第二层 score 对象中的 math 和 chemical 却不是响应式属性。
```js
    let vm = new Vue({
        el: "#app",
        data: {
            username: '小明',
            score: {
                math: 100,
                chemical: {
                    sore: 100,
                    op: 88,
                    get () {
                        return this.score.chemical.sore;
                    },
                    set (val) {
                       this.score.chemical.sore = val; 
                    }
                }
            }
        }
    })
```
- **Vue.set(target, key, value)/vm.$set(target, key, value)**
注意：target 对象不能是 vue 实例 vm，也不能是 vm.data
```js
    let vm = new Vue({
        el: '#app',
        data: {
            username: '小明',
            score: {
                math: 100
            },
            hobby: ['写代码', '看书', '游泳']
        },
        methods: {
            addScore(class) {
                this.$set(this.score,class,100)
            }
        }
    }) 
```
- **数组的变异方法**
Vue 将被侦听的数组的变异方法进行了包裹，所以它们也将会触发视图更新，这些被包裹的方法包括：
   * push
   * pop
   * unshift
   * shift
   * splice
   * sort
   * reverse