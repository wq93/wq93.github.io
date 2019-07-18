# ① 基础篇

## Vue生命周期

### 常用的生命周期钩子

1. `created` 实例创建完成后调用,此阶段完成了数据的观测等,但没有挂载,$el还不可使用.需要初始化处理一些数据时会比较有用(譬如:发送ajax)
2. `mounted` el挂载到实例后调用,一般我们的第一个业务逻辑会在这里面开始
3. `beforeDestory`实例销毁之前调用. 主要解绑一些使用`addEventListener`监听的事件等
    ![生命周期-1](https://upload-images.jianshu.io/upload_images/6541235-633f61c186c84df4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)
## 计算属性概念
1. 计算属性里可以完成各种复杂的逻辑包括运算,函数调用等,只要最终返回一个结果就行. 计算属性还可以依赖多个`Vue`实例的数据,只要其中任一数据的变化,计算属性就会重新执行,视图也会更新.
2. 每个计算属性都包含一个`getter`和`setter`,利用`getter`来读取. 但手动修改计算属性的值就像修改一个普通数据那样,就会触发setter函数,执行自定义的操作
```js
...
computed:{
  fullName:{
    // getter, 用于读取
    get: function () {
      return ...
    },
    // setter 写入时触发
    set : function (newValue) {
      // 逻辑代码
    }
  }
}
...
```
> 计算属性还有两个很实用的小技巧: 1.计算属性可以依赖其他计算属性;2.计算属性不经可以依赖当前`vue`实例的数据,还可以依赖其他实例的数据
3. 与`methods`的对比
    ![计算属性-1](https://upload-images.jianshu.io/upload_images/6541235-f3c57597e655bef1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

在 methods 里定义了一个方法实现了相同的效果，甚至该方法还可以接受参数，使用起来更灵活。既然使用 `methods` 就可以实现，那么为什么还需要计算属性呢？
> 原因就是计算属性是基于它的依赖缓存的。 一个计算属性所依赖的数据发生变化时，它才会重新取值，所以`text` 只要不改变，计算属性也就不更新.

使用计算属性还是 methods 取决于你是否需要缓存，当遍历大数组和做大量计算时，应当使用计算属性，除非你不希望得到缓存。
## v-bind及class与style绑定

### 绑定 class 的几种方式
#### 对象语法
![class绑定.png](https://upload-images.jianshu.io/upload_images/6541235-4fa440ce940015e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/840)
- 当： `class` 的表达式过长或逻辑复杂时，还可以绑定一个计算属性，这是一种很友好和常见的用法，一般当条件多于两个时，都可以使用` data `或 `computed`，例如使用计算属性 ：
  ![class绑定-2](https://upload-images.jianshu.io/upload_images/6541235-4cbfd1c63812171b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 数组语法
![class绑定-3](https://upload-images.jianshu.io/upload_images/6541235-7270d0a9e864d9db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 绑定内联样式
![样式绑定](https://upload-images.jianshu.io/upload_images/6541235-14b6cdad0c92d26f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`css` 属性名称使用驼峰命名`（ camelCase ）`或短横分隔命名`（ kebab-case ）`， 擅染后 的结果为：
```js
<div style="color: red ; font-size: 14px ">文本＜/div>
```
#### 对象语法
![内联样式](https://upload-images.jianshu.io/upload_images/6541235-30bbd2e216134f4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 内置指令
### v-if 与 v-show 的选择
`v-if` 和` v-show` 具有类似的功能，不过` v-if `才是真正的条件渲染，它会根据表达式适当地销毁或重建元素及绑定的事件或子组件。若表达式初始值为 `false`，则一开始元素／组件并不会渲染，只有当条件第一次变为真时才开始编译。
- 而` v-show `只是简单的 `css `属性切换，无论条件真与否，都会被编译。相比之下， `v-if `更适合条件不经常改变的场景，因为它切换开销相对较大，而` v-show `适用于频繁切换条件。
### v-for
- 遍历对象属性时，有两个可选参数，分别是键名和索引：
  ![v-for.png](https://upload-images.jianshu.io/upload_images/6541235-b02a94745cc347d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- v-for 还可以迭代整数 ：
  ![v-for](https://upload-images.jianshu.io/upload_images/6541235-84d3217a5536b307.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 数组更新
`Vue` 的核心是数据与视图的双向绑定，当我们修改数组时， `Vue` 会检测到数据变化，所以用`v-for `渲染的视图也会立即更新。 `Vue `包含了 一组观察数组变异的方法，使用它们改变数组也会触发视图更新：
• push()
• pop()
• shift()
• unshift()
• splice()
• sort()
• reverse()

使用以上方法会改变被这些方法调用的原始数组，有些方法不会改变原数组，例如：
• filter()
• concat()
• slice()
> 解析:
>   它们返回的是一个新数组，在使用这些非变异方法时，不会返回新的数组,但是我们可以用新数组来替换原数组
### 执行过程
Vue 在检测到数组变化时，并不是直接重新渲染整个列表，而是最大化地复用 DOM 元素。替换的数组中，含有相同元素的项不会被重新渲染，因此可以大胆地用新数组来替换旧数组，不用担心性能问题 (前提是我们得为每个遍历的子元素绑定唯一的key值)。
### (例外)不触发视图更新
1. 通过索引直接设置项，比如 app.books[3] = { ... }.
 2. 修改数组长度，比如叩p.books.length = 1
    ![$set](https://upload-images.jianshu.io/upload_images/6541235-4c89bbfdae26158d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/840)
### 事件修饰符
在＠绑定的事件后加小圆点“．”，再跟一个后缀来使用修饰符。` Vue `支持以下修饰符：
```js
 .stop
 .prevent
 .capture
 .self
 .once
```
![事件修饰符](https://upload-images.jianshu.io/upload_images/6541235-34a570fccc818295.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 键盘修饰符
在表单元素上监昕键盘事件时，还可以使用按键修饰符，比如按下具体某个键时才调用方法：
![](https://upload-images.jianshu.io/upload_images/6541235-972ea573f18da9bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)![事件修饰符.png](https://upload-images.jianshu.io/upload_images/6541235-88e71e5933bfbcb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/840)
## v-model
![v-model.png](https://upload-images.jianshu.io/upload_images/6541235-85a32833304e12b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/920)
Forexample:
![](https://upload-images.jianshu.io/upload_images/6541235-47fe29ebd5650b0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1024)![forexample-1.png](https://upload-images.jianshu.io/upload_images/6541235-3e5a4aa8b2dcd9f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Select选择列表
`<option＞`是备选项，如果含有` value `属性 ，` v-model `就会优先匹配 `value `的值 ： 如果没有 ， 就会直接匹配`＜option＞`的 `text`，比如选中第二项时 ， `selected` 的值是` js` ， 而不是 `JavaScript`。
给`＜selected＞`添加属性 `multiple` 就可以多选了， 此时` v-model `绑定 的是一个数组 ， 与复选框用法类似，示例代码如下 ：
ForExample:
![select](https://upload-images.jianshu.io/upload_images/6541235-cb316aef3e87c37b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### v-modal修饰符
#### `.lazy`

在输入框中，` v-model `默认是在` input `事件中同步输入框的数据（除了提示中介绍的中文输入法情况外），使用修饰符` .lazy `会转变为在 `change `事件中同步，`(在失焦或按回车时才更新)`示例代码如下：
![v-modal修饰符](https://upload-images.jianshu.io/upload_images/6541235-cb625cb5835cea8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### `.number`

![number修饰符.png](https://upload-images.jianshu.io/upload_images/6541235-d33293ef3c52d51d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### `.trim`

![trim修饰符.png](https://upload-images.jianshu.io/upload_images/6541235-bdf02489e9f9f7ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 组件

### 为什么`data()`需要`return`新的对象?

`JavaScript `对象是引用关系 ， 所 以如果 `return` 出 的对象 引 用了 外部 的一个对象 ， 那这个对象就 是共享的 ， 任何一方修改都会同步.这肯定不是我们期望的效果 ， 所以给组件返回一个新的 `data `对象来独立  .

### props

>概念: 
>
>组件不仅仅是要把模板的内容进行复用，更重要的是组件间要进行通信。通常父组件的模板 中包含子组件，父组件要正向地向子组件传递数据或参数，子组件接收到后根据参数的不同来渲染 不同的内容或执行操作。这个正向传递数据的过程就是通过` props` 来实现的  

### 单向数据流(重点)

​	`Vue 2.x `与 `Vue l.x `比较大的一个改变就是， `Vue2.x` 通过 `props `传递数据是单向的了， 也就是父组件数据变化时会传递给子组件，但是反过来不行。而在` Vue l.x` 里提供了 `. sync `修饰符来支持双向绑定。之所以这样设计，是尽可能将父子组件解稿，避免子组件无意中修改了父组件的状态。 业务中会经常遇到两种需要改变 `prop` 的情况，一种是父组件传递初始值进来，子组件将它作为初始值保存起来，在自己的作用域下可以随意使用和修改。这种情况可以在组件` data` 内再声明 一个数据，引用父组件的 `prop`

​	所有的 prop 都使得其父子 prop 之间形成了一个**单向下行绑定**：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。

​	额外的，每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值。这意味着你**不**应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。

### 双向数据绑定(重点)-(另起博客)

参考博客: http://www.cnblogs.com/canfoo/p/6891868.html

​	vue.js 是采用数据劫持结合发布者-订阅者模式的方式，通过 Object.defineProperty()来劫持各个属性的 setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

具体步骤：
第一步：需要 observe 的数据对象进行递归遍历，包括子属性对象的属性，都加上 setter 和 getter 这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化

第二步：compile 解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图

第三步：Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁，主要做的事情是:

- 在自身实例化时往属性订阅器(dep)里面添加自己
- 自身必须有一个 update()方法
- 待属性变动 dep.notice()通知时，能调用自身的 update() 方法，并触发 Compile 中绑定的回调，则功成身退。

第四步：MVVM 作为数据绑定的入口，整合 Observer、Compile 和 Watcher 三者，通过 Observer 来监听自己的 model 数据变化，通过 Compile 来解析编译模板指令，最终利用 Watcher 搭起 Observer 和 Compile 之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据 model 变更的双向绑定效果。

### 非父子组件通信

*   兄弟组件和跨级组件

在`Vue.js l.x` 中，除了 `$emit()`方法外，还提供了`$dispatch（）`和`$broadcast（）`这两个方法 。 `$dispatch()`用于向上级派发事件，只要是它的父级（一级或多级以上〉，都可以在`Vue`实例的 `events`选项内 接收.
同理， `$broadcast（）`是由上级向下级广播事件的，用法完全一致，只是方向相反 。
### 中央事件总线Bus
Forexample:

![](https://upload-images.jianshu.io/upload_images/6541235-d1e1513fce6edd99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![](https://upload-images.jianshu.io/upload_images/6541235-faa68da5728883af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![bus.img](https://upload-images.jianshu.io/upload_images/6541235-a78d350597253f88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/840)

### 父链
在子组件中，使用 `this.$parent `可以直接访问该组件的父实例或组件，父组件也可以通过` this.$children `访问它所有的子组件，而且可以递归向上或向下无限访问， 直到根实例或最内层的组件。  
### solt分发
`props `传递数据、` events` 触发事件和 `slot `内容分发就构成了 Vue 组件的 3 个 API 来源，再复杂的组件也是由这 3 部分构成的。
#### 访问solt
在 `Vue. l.x` 中 ， 想要获取某个` slot `是比较麻烦的 ， 需要用 `v-el `间接获取。而 `Vue 2.x `提供了用来访问被 `slot` 分发的内容的方法` $slots`
ForExample:
![](https://upload-images.jianshu.io/upload_images/6541235-955d96b16d0ee838.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![访问solt.png](https://upload-images.jianshu.io/upload_images/6541235-4fc3c38dc369f1b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
通过`$slots` 可以访问某个具名 `slot`, `this .$slots.default `包括了所有没有被包含在具名 slot 中的节点。
### 组件的高级用法
#### 动态组件
`Vue.js `提供了 一个特殊的元素`＜component> `用来动态地挂载不同的组件 ， 使用` is`特性来选择要挂载的组件。
![](https://upload-images.jianshu.io/upload_images/6541235-377c3a5917cb504f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![动态组件](https://upload-images.jianshu.io/upload_images/6541235-ad8c043c78f92773.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 异步组件
#### $nextTick(重要)

示例代码:
![$nextTick](https://upload-images.jianshu.io/upload_images/6541235-34df6ed79118e9a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Vue 一个重要的概念：异步更新队列。
​    Vue 在观察到数据变化时并不是直接更新 `DOM`，而是开启 一个队列，并缓冲在同一事件循环中发生的所有数据改变。在缓冲时会去除重复数据，从而避免不必要的计算和` DOM `操作。然后，在下一个事件循环 tick 中，` Vue `刷新队列井执行实际（己去重的）工作。所以如果你用一个`for `循环来动态改变数据 100次，其实它只会应用最后一次改变，如果没有这种机制，` DOM `就要重绘 100次，这固然是一个很大的开销。
Vue 会根据当前浏览器环境优先使用原生的 `Promise.tnen` 和 `MutationObserver`，如果都不支持，就会采用 `setTimeout `代替。
知道了 `Vue` 异步更新 `DOM `的原理，上面示例的报错也就不难理解了 。 事实上，在执行`this.showDiv ＝ true`同时，` div `仍然还是没有被创建出来，直到下一个 Vue 事件循环时，才开始创建。`$nextTick `就是用来知道什么时候 DOM 更新完成的，所以上面的示例代码需要修改为：
![$nextTick-2.png](https://upload-images.jianshu.io/upload_images/6541235-b86ad89b7f1d1489.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/840)



因为 `$nextTick()` 返回一个 Promise 对象，所以你可以使用新的 [ES2016 async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)语法完成相同的事情：

```js
methods: {
  async updateMessage: function () {
    this.message = 'updated'
    console.log(this.$el.textContent) // => '未更新'
    await this.$nextTick()
    console.log(this.$el.textContent) // => '已更新'
  }
}
```



#### 手动挂载实例

Vue.extend 是基础 Vue 构造器，创建一个“子类”，参数是一个包含组件选项的对象。

如果 Vue 实例在实例化时没有收到 el 选项，它就处于“未挂载”状态，没有关联的 DOM 元素。可以使`$mount（）`手动地挂载一个未挂载的实例。这个方法返回实例自身，因而可以链式调用其他实例方法。示例代码如下：

![$mount](https://upload-images.jianshu.io/upload_images/6541235-e8cdec5d120a34ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也可以写成:

![](https://upload-images.jianshu.io/upload_images/6541235-6f79cd9a6cb443e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 自定义指令(了解)
自定义指令的注册方法和组件很像，也分全局注册和局部注册，比如注册一个` v-focus` 的指令，用于在`＜input>`、`＜textarea>`元素初始化时自动获得焦点，两种写法分别是：
![directive](https://upload-images.jianshu.io/upload_images/6541235-ea038a4a40053635.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 自定义指令的钩子函数
![自定义指令的钩子函数](https://upload-images.jianshu.io/upload_images/6541235-760d2483480ede7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![自定义指令的钩子函数-例子.png](https://upload-images.jianshu.io/upload_images/6541235-e4bbde4eeb34922b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 自定义指令的钩子函数的几个参数
![钩子函数参数.png](https://upload-images.jianshu.io/upload_images/6541235-73daadca363f04fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/940)

---
# ② 进阶篇
## Render函数
### 什么是Virtual Dom?
`Vue.js 2.x` 与 `Vue.js 1.x` 最大的区别就在于 2.x 使用了` Virtual Dom `（虚拟 DOM ）来更新` DOM`节点，提升擅染性能。
`React` 和 `Vue 2 `都使用了` Virtual Dom` 技术，` Virtual Dom `并不是真正意义上的 `DOM` ，而是一个轻量级的 `JavaScript` 对象，在状态发生变化时，` Virtual Dom `会进行` Diff `运算，来更新只需要被替换的 `DOM`，而不是全部重绘。
### ` Virtual Dom `运行过程
![ Virtual Dom运行过程.png](https://upload-images.jianshu.io/upload_images/6541235-8cf52e2e953343f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Virtual Dom结构.png](https://upload-images.jianshu.io/upload_images/6541235-3297efcc72f30512.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### createElement用法
`createElement `构成了 `Vue Virtual Dom `的模板，它有 3 个参数：
![](https://upload-images.jianshu.io/upload_images/6541235-07535b20673affd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/6541235-00cad9ab9f626e1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/450)![](https://upload-images.jianshu.io/upload_images/6541235-db0ea8c043eec7c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![](https://upload-images.jianshu.io/upload_images/6541235-6408386ee7b59ba5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/420)

### JSX语法
为了让 Render 函数更好地书写和 阅读 ， Vue扣提供了插件` babel-plugin-transform-vue-jsx `来支持 JSX 语法 。
JSX 是一种看起来像` HTML `， 但实际是 `JavaScript `的语法扩展 ，它用更接近 DOM 结构的形式来描述一个组件的 UI 和状态信息 ， 最早在React.js 里大量应用。
![](https://upload-images.jianshu.io/upload_images/6541235-4158680181ca2ca9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1120)![](https://upload-images.jianshu.io/upload_images/6541235-b2f1c86b513524ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



>  PS: 在业务中，生产效率是第一位，所以绝大部分业务代码都应当用 `template `来完成。你不用在意性能问题 ， 如果使用了 `webpack` 做编译 , `template `都会被预编译为 `render` 函数。

## 使用webpack

### 前端工程化

 - JavaScript 、 css 代码的合井和压缩 。
 -  css 预处理 ： Less 、 Sass 、 Stylus 的编译。 
 - 生成雪碧图（ CSS Sprite ）。
 - ES 6 转 ES 5. 
 - 模块化。  
 - ......

### 详细内容

代码仓库: `https://github.com/wq93/Vue-webpack`见commit

## Vuex

仓库 `store `包含了应用的数据（状态）和操作过程。` Vuex` 里的数据都是响应式的，任何组件使用同一` store `的数据时，只要` store `的数据变化，对应的组件也会立即更新。  

### mutation为啥不能异步操作?

注意,`mutation`里尽量不要异步操作数据,如果异步操作数据了,组件在commit后数据不能立马改变,而且不知道什么时候会改变.异步操作我们都会放在`action`里处理

### mutations和actions区别

`mutations` 、` actions `看起来很相似，可能会觉得不知道该用哪个，但是 `Vuex `很像是一种与开发者的约定，所以涉及改变数据的，就使用 `mutations`，存在业务逻辑的，就用` actions`。  (可以通过actions获取数据)

### Vuex的getters和组件的computed的区别

1. 如果数据还有其他组件复用，建议放在` Vuex`。 

2. 如果需要跨多级组件传递数据，建议放在` Vuex` .

3.  需要持久化的数据（如登录后用户的信息），建议放在` Vuex `. 

4. 跟当前业务组件强相关的数据 （ 如示例中的 `filterBrand` 、 `filterColor` ， 它们只在当前组件有 用），可以放在组件内。  



### 总结

```js
在大中型项目中，尤其是多人协同开发时，最重要的是模块解耦。对于公共组件，要定义好 `API` (` props` 、 `events` 、 `slots` ），公用数据要在 `Vuex` 或 `bus `中统一维护。在业务中，要尽可能避免直接操作父链和子链来修改组件的状态，对于跨级通信最好通过 `Vuex` 或 `bus` 完成。 

在协同开发时，可以将路由组件的内容拆分为多个组件，由不同的人维护，这样可以避免冲 突，使模块更清晰，寻找 bug 也更有针对性。公共配置还可以使用混合（ mixins ） 。

 当项目中页面较多，在使用 Vuex 时可以将 store 分发到不同的文件或文件夹内 ， 井参照Vuex 的高级用法，使用 modules 把 store 分割到不同的模板，这样对于复杂的应用更具维护性。  
```

## 服务端渲染与 Nuxt.js

使用服务端渲染 (SSR)

### 为什么使用服务端渲染 (SSR)?

       	1. 主要目的是做搜索引擎优化（ SEO ）
       	2. 客户端的网络可能是不稳定的，有的地方很快，有的地方会很慢。 这种情况下，通过 SSR 减少请求量和客户端渲染可以相对快速地看到内容。  
