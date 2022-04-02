## Vue使用

### Vue基本使用

1. #### 模板（指令，插值）

   插值，表达式

   指定，动态属性

   v-html: 会有xss风险，会覆盖子组件

2. #### computed和watch

   computed有缓存，data不变则不会重新计算，提高运算的性能

   watch默认是浅监听，只会监听对象第一层的数据变化

   watch如何深度监听?

   watch监听引用类型，拿不到old value

   ```javascript
   export default {
       data() {
           return {
               name: '夏衣旦'
               info: {
               	city: '北京'
           	}
           }
       },
       watch() {
       	name(oldVal, val) {
               // 值类型，可以正常拿到oldVal和val
               console.log('watch name', oldVal, val) 
           },
           info: {
               handler(oldVal, val) {
                   // 引用类型，拿不到oldVal，因为指针相同，此时已经指向val
                   // 注意：引用类型赋值是指针赋值的关系
                   console.log('watch info', oldVal, val)
               },
               deep: true // 深度监听
           }
   	}
   }
   ```

3. #### class和style

   使用动态属性 :class :style class可以是一个对象或数组的写法, style也可以是对象的写法

   style要用驼峰式写法

4. #### 条件渲染

   v-if v-else的用法，可使用变量，也可以使用===表达式

   v-if和v-show的区别?

   v-if和v-show的使用场景?

5. #### 循环列表渲染

   如何遍历对象? v-for

   ```html
   // 遍历数组的方式
   <ul>
   	<li v-for="(item, index) in listArr" :key="item.id">
   		{{index}}-{{item.id}}-{{item.title}}
   	</li>
   </ul>
   // 遍历对象的方式
   <ul>
   	<li v-for="(val, key, index) in listObj" :key="key">
   		{{index}}-{{key}}-{{val.title}}
   	</li>
   </ul>
   ```

   key的重要性，key不能乱写（如random或者index)，需要写一个和业务相关联的信息

   **v-for和v-if不能一起使用**

6. #### 事件

   event参数，自定义参数

   [观察]事件被绑定到哪里？

   ​	1. event是原生的

   ​	2. 事件被挂载到当前元素

   事件修饰符

   ```html
   <!-- 阻止单击事件继续传播 -->
   <a v-on:click.stop="doThis"></a>
   
   <!-- 提交事件不再重载页面 -->
   <form v-on:submit.prevent="onSubmit"></form>
   
   <!-- 修饰符可以串联 -->
   <a v-on:click.stop.prevent="doThat"></a>
   
   <!-- 只有修饰符 -->
   <form v-on:submit.prevent></form>
   
   <!-- 添加事件监听器时使用事件捕获模式 -->
   <!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
   <div v-on:click.capture="doThis">...</div>
   
   <!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
   <!-- 即事件不是从内部元素触发的 -->
   <div v-on:click.self="doThat">...</div>
   ```

   按键修饰符

   ```html
   <!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
   <button @click.ctrl="onClick">A</button>
   
   <!-- 有且只有 Ctrl 被按下的时候才触发 -->
   <button @click.ctrl.exact="onClickExact">A</button>
   
   <!-- 没有任何系统修饰符被按下的时候才触发 -->
   <button @click.exact="onClick">A</button>
   ```

7. #### 表单

   v-model

   常见表单项 textarea checkbox radio select

   修饰符 lazy number trim

   ​	.trim 截取前后的空格

   ​	.lazy 相当于防抖的效果，等输入完的时候才会变化

   ​	.number 数字

8. #### refs

   获取DOM元素节点的方式

### Vue组件使用

1. #### props和$emit (父子组件的通讯方式)

2. #### 组件间通讯-自定义事件 (兄弟组件的通讯)

   ```javascript
   import event from './event'
   
   // 组件A
   methods: {
       addTitle() {
           // 调用父组件的事件
       	this.$emit('add', this.title)
           // 调用自定义事件 'onAddTitle'
           event.$emit('onAddTitle', this.title)
       }
   }
   
   // 组件B
   mounted() {
       // 绑定自定义事件
       event.$on('onAddTitle', this.addTitleHandler)
   },
   beforeDestroy() {
       // 及时销毁，否则可能造成内存泄漏
       event.$off('onAddTitle', this.addTitleHandler)
   },
   methods: {
       addTitleHandler (title){
   		console.log('on add title : ', title)
   	}
   }
   ```

3. #### 组件生命周期

   生命周期（单个组件）

    	1. 挂载阶段
    	2. 更新阶段
    	3. 销毁阶段

   created和mounted有什么区别?

   ​	created只是把js的实例初始化了，但它只是存在于JS内存的一个变量而已，这个时候并没有开始渲染

   ​	mounted组件在页面渲染完了

   生命周期方法

   | 创建 | beforeCreate()  | created()   |
   | ---- | --------------- | ----------- |
   | 挂载 | beforeMount()   | mounted()   |
   | 更新 | beforeUpdate()  | updated()   |
   | 销毁 | beforeDestroy() | destroyed() |

   生命周期（父子组件）

   | 加载渲染   | 父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount- >子mounted->父mounted |
   | ---------- | ------------------------------------------------------------ |
   | 子组件更新 | 父beforeUpdate->子beforeUpdate->子updated->父updated         |
   | 父组件更新 | 父beforeUpdate->父updated                                    |
   | 销毁过程   | 父beforeDestroy->子beforeDestroy->子destroyed->父destroyed   |

   组件的调用顺序都是先父后子，渲染完成的顺序都是先子后父

   组件的销毁操作是先父后子，销毁完成的顺序是先子后父

4. #### beforeDestroy() 里面我们会做哪些事情？

   1. 及时的解绑事件，否则会造成内存泄漏
   2. 销毁子组件
   3. 事件监听器

### Vue高级特性

1. #### 自定义v-model

   ```javascript
   // 父组件
   <template>
   	<CustomModel v-model="name"/>
   </template>
   <script>
   import CustomModel from './CustomModel'
   
   export default {
   	components: {
   		CustomModel
   	}
   }
   </script>
   
   // 子组件
   <template>
   	// 1. input 使用了 :value 而不是v-model
   	// 2. change1 和 model.event1 要对应起来
   	// 3. text1 属性对应起来
   	<input type="text" :value="text1" @input="$emit('change1', $event.target.value)">
   </template>
   export default {
   	model: {
   		prop: 'text1', // 对应 props text1
   		event: 'change1'
   	},
   	props: {
   		text1: String,
   		default() {
   			return ''
   		}
   	}
   }
   ```

2. #### $nextTick

   原理：vue（和react）是异步渲染

   data改变之后，DOM不会立刻渲染

   页面渲染时，会将data的修改做整合，多次data修改只会渲染一次

   $nextTick会在DOM渲染之后被触发，以获取最新DOM节点

3. #### slot插槽

   基本使用：父组件往子组件插入一段内容

   作用域插槽

   ​	作用域插槽允许你传递一个模板而不是已经渲染好的元素给插槽，模板虽然是在父级作用域中渲染的，却能拿到子组件的数据

   具名插槽

   ```javascript
   <slot name="header"></slot>
   <slot></slot>
   <slot name="footer"></slot>
   
   <template v-slot:header>
   	<h1>将插入header slot中</h1>
   </template>
   <p>将插入到main slot中，即未命名的slot</p>
   <template v-slot:footer>
   	<p>将插入footer slot中</p>
   </template>
   ```

   

4. #### 动态组件

    需要根据数据，动态渲染的场景，即组件类型不确定

   ```html
   <component :is="component-name"/>
   ```

5. #### 异步组件

   import () 函数

   按需加载，异步加载大组件

   ```javascript
   export default {
   	components: {
   		FormDemo: ()=>import('../FormDemo')
   	}
   }
   ```

   

6. #### keep-alive

   缓存组件

   频繁切换，但不需要重复渲染的时候

   vue常见性能优化的解决方案之一

7. #### mixin

   多个组件有相同的逻辑，抽离出来

   配置信息会进行融合

   缺点：

   ​	1. 变量来源不明确，不利于阅读

   ​	2. 多mixin可能会造成命名冲突

   ​	3. mixin和组件可能会出现多对多的关系，复杂度较高

   ```javascript
   import myMixin from './mixin'
   export default {
   	mixins: [myMixin]
   }
   ```

### Vuex 使用

1. #### 基本概念

   state

   getters

   action

   mutation

2. #### 用于Vue组件

   dispatch

   commit

   mapState

   mapGetters

   mapActions

   mapMutations

### Vue-router使用

1. #### 路由模式

   1. hash模式：http://abc.com/#/user/10

   2. H5 history模式：http://abc.com/user/20 （需要server端支持，因此无特殊需求可选择前者）

      ```javascript
      const router = new VueRouter({
      	mode: 'history', // 使用h5 history模式
      	routers: [...]
      })
      ```

2. #### 路由配置（动态路由，懒加载）

   1. ##### 动态路由

      ```javascript
      const User = {
          // 获取参数如 10 20
      	template: '<div>User {{ $route.params.id }} </div>'
      }
      const router = new VueRouter({
      	routes: [
      		// 动态路径参数，以冒号开头，能命中'/user/10' '/user/20'等格式的路由
      		path: '/user/:id',
               component: User
      	]
      })
      ```

   2. ##### 懒加载

      ```javascript
      export default new VueRouter({
          routes: [
              {
                  path: '/',
                  component: ()=> import(
                  	'./../components/Navigator'
                  )
              },{
                  path: '/feedback',
                  component: ()=> import(
                  	'./../components/FeedBack'
                  )
              }
          ]
      })
      ```

## Vue原理

### 组件化和mvvm

​	“很久以前”就有组件化

​		asp jsp php已经有组件化了

​		node.js中也有类似的组件化

​	数据驱动视图：

​		传统组件，只是静态渲染，更新还要依赖于操作DOM

​		数据驱动视图：Vue MVVM, React setState

### 响应式原理

### vdom和diff算法

### 模板编译

### 组件渲染过程

### 前端路由

## 面试题

### Vue面试题

1. #### v-show和v-if的区别

   v-if只有当指令的表达式返回值为true的时候才会被渲染，为false的时候，元素是不存在于文档中的；

   v-show则不管指令表达式的返回值是什么，都会被渲染，并且只是基于css的样式diplay切换，元素始终存在于文档中

2. #### 什么时候用v-show，什么时候用v-if ?

   1.`v-if`在条件切换时，会对标签进行适当的创建和销毁，而`v-show`则仅在初始化时加载一次，因此`v-if`的开销相对来说会比`v-show`大。
   2.`v-if`是惰性的，只有当条件为真时才会真正渲染标签；如果初始条件不为真，则`v-if`不会去渲染标签。`v-show`则无论初始条件是否成立，都会渲染标签，它仅仅做的只是简单的CSS切换

   结论：组件需要频繁切换的时候，使用v-show，去减少开销

3. #### 为何v-for中要用key

4. #### 描述vue组件生命周期（有父子组件的情况下）

5. #### vue组件如何通讯

6. #### 描述组件渲染和更新的过程

7. #### 双向数据绑定v-model的实现原理

### React面试题

1. #### 组件之间如何通讯

   父子组件props

   自定义事件

   Redux和Context

2. #### JSX本质是什么

   createElement

   执行返回vnode

3. #### Context是什么，如何应用？

   父组件，向其下所有子孙组件传递信息

   如一些简单的公共信息：主题色，语言等

   复杂的公共信息，请用redux

4. #### shouldComponentUpdate (简称SCU)用途

   性能优化

   配合“不可变值”一起使用，否则会出错

5. #### 描述redux单项数据流

   图解

6. #### setState场景题

   ```javascript
   componentDidMount() {
   	// count初始值为0
   	this.setState({count: this.state.count + 1})
   	console.log('1', this.state.count) // 0
   	this.setState({count: this.state.count + 1})
   	console.log('2', this.state.count) // 0
   	setTimeout(()=>{
   		this.setState({count: this.state.count+1})
   		console.log('3', this.state.count) //2
   	})
   	setTimeout(()=>{
   		this.setState({count: this.state.count+1})
   		console.log('4', this.state.count) //3
   	})
   }
   // setState是异步的，并且会合并1和2的操作
   ```

   

7. #### 什么是纯函数

   返回一个新值，没有副作用（不会“偷偷”修改其他值）

   重点：不可变值

   如 arr1 = arr.slice()

8. #### React组件生命周期

   单组件生命周期

   父子组件生命周期

9. #### React发起ajax应该在哪个生命周期

   同vue

   componentDidMount (DOM已经渲染完的生命周期上)

10. #### 渲染列表，为何使用key

    同vue，必须用key，且不能是index和random

    diff算法中通过tag和key来判断，是否是same node

    减少渲染次数，提升渲染性能

11. #### 函数组件和class组件的区别

    纯函数，输入props，输出JSX

    没有实例，没有生命周期，没有state

    不能扩展其他方法

12. #### 什么是受控组件

    表单的值，受state控制

    需要自行监听onChange，更新state

    对比非受控组件

13. #### 何时使用异步组件

    同vue

    加载大组件

    路由懒加载

14. #### 多个组件有公共逻辑，如何抽离

    高阶组件

    Render Props

    mixin已被React废弃

15. #### redux如何进行异步请求

    使用异步action

    如redux-thunk

16. #### react-router如何配置懒加载

    lazy

    ```javascript
    import React, {Suspense, lazy} from 'react'
    
    const Home = lazy(()=>import('./routes/Home'))
    const About = lazy(()=>import('./routes/About'))
    ```

    

17. #### PureComponent有何区别

    实现了浅比较的shouldComponentUpdate

    优化性能

    但要结合不可变值使用

18. #### React事件和DOM事件的区别

    所有事件挂载到document上

    event不是原生的，是SyntheticEvent合成事件对象

    dispatchEvent

19. #### React性能优化

    1. sss渲染列表时加key
    2. 自定义事件，DOM事件及时销毁
    3. 合理使用异步组件
    4. 减少函数bind this的次数
    5. 合理使用SCU PureComponent和memo
    6. 合理使用Immutable.js
    7. webpack层面的优化
    8. 前端通用的性能优化，如图片懒加载
    9. 使用SSR （服务器端渲染）

20. #### React和Vue的区别

    共同点：

    1. 都支持组件化

    2. 都是数据驱动视图
    3. 都是用v dom操作dom

    不同点：

    1. React使用JSX拥抱JS，Vue使用模板拥抱html (开发认知)
    2. React函数式编程，Vue声明式编程
    3. React更多需要自力更生，Vue把想要的都给你

### 框架综合应用

1. 基于React设计一个todolist （组件结构，redux state数据结构）
2. 基于VUE设计一个购物车 (组件结构，vuex state数据结构)

### Webpack面试题

1. 前端代码为何要进行构建和打包
2. module chunk bundle分别是什么意思？有何区别
3. loader和plugin的区别?
4. webpack如何实现懒加载
5. webpack常见性能优化
6. babel-runtime和babel-polyfill的区别

