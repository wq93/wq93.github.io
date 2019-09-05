### JS 基础知识点及常考面试题-1

#### 类型转换

![image.png](https://upload-images.jianshu.io/upload_images/6541235-6a99f3482784098c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 转Boolean

在条件判断时，除了 `undefined`， `null`， `false`， `NaN`， `''`， `0`， `-0`，其他所有值都转为 `true`，包括所有对象。

#### 对象转原始类型

对象在转换类型的时候，会调用内置的 `[[ToPrimitive]]` 函数，对于该函数来说，算法逻辑一般来说如下：

- 如果已经是原始类型了，那就不需要转换了
- 调用 `x.valueOf()`，如果转换为基础类型，就返回转换的值
- 调用 `x.toString()`，如果转换为基础类型，就返回转换的值
- 如果都没有返回原始类型，就会报错

当然你也可以重写 `Symbol.toPrimitive` ，该方法在转原始类型时调用优先级最高。

```js
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  },
  [Symbol.toPrimitive]() {
    return 2
  }
}
1 + a // => 3
```

#### 四则运算符

加法运算符不同于其他几个运算符，它有以下几个特点：

- 运算中其中一方为字符串，那么就会把另一方也转换为字符串
- 如果一方不是字符串或者数字的基本类型，那么会将它转换为数字或者字符串
- 如果一方是引用类型的话，会使用`toPrimitive`规则转换成基本类型再参与运算

那么对于除了加法的运算符来说，只要其中一方是数字，那么另一方就会被转为数字

#### 比较运算符

1. 如果是对象，就通过 `toPrimitive` 转换对象
2. 如果是字符串，就通过 `unicode` 字符索引来比较

#### this(重要)

- 对于直接调用 `foo` 来说，不管 `foo` 函数被放在了什么地方，`this` 一定是 `window`
- 对于 `obj.foo()` 来说，我们只需要记住，谁调用了函数，谁就是 `this`，所以在这个场景下 `foo`函数中的 `this` 就是 `obj` 对象
- 对于 `new` 的方式来说，`this` 被永远绑定在了 `c` 上面，不会被任何方式改变 `this`

##### 箭头函数

```js
function a() {
  return () => {
    return () => {
      console.log(this)
    }
  }
}
console.log(a()()())
```

​	箭头函数中的 `this` 只取决包裹箭头函数的第一个普通函数的 `this`。在这个例子中，因为包裹箭头函数的第一个普通函数是 `a`，所以此时的 `this` 是 `window`。另外对箭头函数使用 `bind` 这类函数是无效的。

##### bind

```js
let a = {}
let fn = function () { console.log(this) }
fn.bind().bind(a)() // => ?

// ===> 等价于
let fn2 = function fn1() {
  return function() {
    return fn.apply()
  }.apply(a)
}
fn2()
```

​	可以从上述代码中发现，不管我们给函数 `bind` 几次，`fn` 中的 `this` 永远由第一次 `bind` 决定，所以结果永远是 `window`。

##### 流程图

![image.png](https://upload-images.jianshu.io/upload_images/6541235-3c4a587ab5e67267.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### JS 基础知识点及常考面试题-2

#### == vs ===

对于 `==` 来说，如果对比双方的类型**不一样**的话，就会进行**类型转换**

假如我们需要对比 `x` 和 `y` 是否相同，就会进行如下判断流程：

1. 首先会判断两者类型是否**相同**。相同的话就是比大小了
2. 类型不相同的话，那么就会进行类型转换
3. 会先判断是否在对比 `null` 和 `undefined`，是的话就会返回 `true`
4. 判断两者类型是否为 `string` 和 `number`，是的话就会将字符串转换为 `number`

```js
1 == '1'
      ↓
1 ==  1
```

5. 判断其中一方是否为 `boolean`，是的话就会把 `boolean` 转为 `number` 再进行判断

```js
'1' == true
        ↓
'1' ==  1
        ↓
 1  ==  1
```

6. 判断其中一方是否为 `object` 且另一方为 `string`、`number` 或者 `symbol`，是的话就会把 `object` 转为原始类型再进行判断

```js
'1' == { name: 'yck' }
        ↓
'1' == '[object Object]'
```

##### 流程图

![image.png](https://upload-images.jianshu.io/upload_images/6541235-a41546b1cccfaba5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 深浅拷贝

##### Object.assign

​	首先可以通过 `Object.assign` 来解决这个问题，很多人认为这个函数是用来深拷贝的。其实并不是，`Object.assign` 只会拷贝所有的属性值到新的对象中，如果属性值是对象的话，拷贝的是地址，所以并不是深拷贝

##### JSON.stringify

该方法也是有局限性的：

- 会忽略 `undefined`
- 会忽略 `symbol`
- 不能序列化函数
- 不能解决循环引用的对象

#### 解析console.log([] == ![]) //true

右边：
​	![]: []为true so ![]为false;即 [] == false;
​	当一边为Boolean 则将Boolean转换为Number 即 false 为 0;即 [] == 0;
左边：
​	当一边为对象，则调用对象的valueOf()方法转换 [].valueOf() 为 []；
​	因为还不是原始值则调用toString()： [].toString() 为 "";此时为基本类型则 拿去比较;即 "" == 0;
​	一边为String类型一边为Number类型则将String转Number  即 Number('') 为 0 ;即 0 == 0 //true
所以 [] == ![] //true

##### 原型

- `Object` 是所有对象的爸爸，所有对象都可以通过 `__proto__` 找到它
- `Function` 是所有函数的爸爸，所有函数都可以通过 `__proto__` 找到它
- 函数的 `prototype` 是一个对象
- 对象的 `__proto__` 属性指向原型， `__proto__` 将对象和原型连接起来组成了原型链

### ES6 知识点及常考面试题

#### var、let 及 const 区别

- 函数提升优先于变量提升，函数提升会把整个函数挪到作用域顶部，变量提升只会把声明挪到作用域顶部
- `var` 存在提升，我们能在声明之前使用。`let`、`const` 因为暂时性死区的原因，不能在声明前使用
- `var` 在全局作用域下声明变量会导致变量挂载在 `window` 上，其他两者不会
- `let` 和 `const` 作用基本一致，但是后者声明的变量不能再次赋值

#### 寄生组合继承

```js
function Parent(value) {
  this.val = value
}
Parent.prototype.getValue = function() {
  console.log(this.val)
}

function Child(value) {
  Parent.call(this, value)
}
Child.prototype = Object.create(Parent.prototype, {
  constructor: {
    value: Child,
    enumerable: false,
    writable: true,
    configurable: true
  }
})

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true
```

#### Class语法

```js
class Parent {
  constructor(value) {
    this.val = value
  }
  getValue() {
    console.log(this.val)
  }
}
class Child extends Parent {
  constructor(value) {
    super(value)
    this.val = value
  }
}
let child = new Child(1)
child.getValue() // 1
child instanceof Parent // true
```

#### super的作用

相对于: `Parent.call(this, ...arguments)`

### JS 异步编程及常考面试题

#### 并发（concurrency）和并行（parallelism）区别

​	并发是宏观概念，我分别有任务 A 和任务 B，在一段时间内通过任务间的切换完成了这两个任务，这种情况就可以称之为并发。

​	并行是微观概念，假设 CPU 中存在两个核心，那么我就可以同时完成任务 A、B。同时完成多个任务的情况就可以称之为并行。

#### 回调函数（Callback）

回调函数的缺点:

1. 回调地狱（Callback hell）
2. 嵌套函数存在耦合性，一旦有所改动，就会牵一发而动全身
3. 嵌套函数一多，就很难处理错误
4. 不能使用 `try catch` 捕获错误
5. 不能直接 `return`。

#### async 及 await

缺点:

1. `await` 将异步代码改造成了同步代码,如果多个异步代码没有依赖性却使用了 `await` 会导致性能上的降低。

```js
let a = 0
let b = async () => {
  a = a + await 10
  console.log('2', a) // -> '2' 10
}
b()
a++
console.log('1', a) // -> '1' 1
```

对于以上代码你可能会有疑惑，让我来解释下原因

- 首先函数 `b` 先执行，在执行到 `await 10` 之前变量 `a` 还是 0，因为 `await` 内部实现了 `generator` ，**`generator` 会保留堆栈中东西**，所以这时候 `a = 0` 被保存了下来
- 因为 `await` 是异步操作，后来的表达式不返回 `Promise` 的话，就会包装成 `Promise.reslove(返回值)`，然后会去执行函数外的同步代码
- 同步代码执行完毕后开始执行异步代码，将保存下来的值拿出来使用，这时候 `a = 0 + 10`

上述解释中提到了 `await` 内部实现了 `generator`，其实 `await` 就是 `generator` 加上 `Promise` 的语法糖，且内部实现了自动执行 `generator`。如果你熟悉 co 的话，其实自己就可以实现这样的语法糖。

#### 经典异步面试题

```js
async function async1 () {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}

async function async2 () {
    console.log('async2');
}

console.log('script start');

setTimeout(function () {
    console.log('setTimeout');
}, 0);

async1();

new Promise(function (resolve) {
    console.log('promise1');
    resolve();
}).then(function () {
    console.log('promise2');
});

console.log('script end');

// script start
// async1 start
// async2
// promise1
// script end
// promise2
// async1 end
// setTimeout
```

##### 解析:

这里需要先简单地说一些 event loop 的概念。

- Javascript是单线程的，所有的同步任务都会在主线程中执行。
- 主线程之外，还有一个任务队列。每当一个异步任务有结果了，就往任务队列里塞一个事件。
- 当主线程中的任务，都执行完之后，系统会 “依次” 读取任务队列里的事件。与之相对应的异步任务进入主线程，开始执行。
- 异步任务之间，会存在差异，所以它们执行的优先级也会有区别。大致分为 微任务（micro task，如：Promise、MutaionObserver等）和宏任务（macro task，如：setTimeout、setInterval、I/O等）。同一次事件循环中，微任务永远在宏任务之前执行。
- 主线程会不断重复上面的步骤，直到执行完所有任务。

另外，还有 async/await 的概念。

- async 函数，可以理解为是Generator 函数的语法糖。
- 它建立在promise之上，总是与await一起使用的。
- await会返回一个Promise 对象，或者一个表达式的值。
- 其目的是为了让异步操作更优雅，能像同步一样地书写。

> 我的理解:
>
> 首先，从console的数量上看，会输出8行结果。
>
> 再瞟了一眼代码，看到了setTimeout，于是，默默地把它填入第8行。
>
> 在setTimeout附近，看到了 console.log( 'script start' ) 和 async1()，可以确认它们是同步任务，会先在主线程中执行。所以，妥妥地在第1行填入 script start，第2行填入async1方法中的第一行 async1 start。
>
> 接下来，遇到了await。从字面意思理解，让我们等等。需要等待async2()函数的返回，同时会阻塞后面的代码。所以，第3行填入 async2。
>
> 讲道理，await都执行完了，该轮到console.log( 'async1 end' )的输出了。但是，别忘了下面还有个Promise，有一点需要注意的是：当 new 一个 Promise的时候，其 resolve 方法中的代码会立即执行。如果不是 async1()的 await 横插一杠，promise1 可以排得更前面。所以，现在第4行填入 promise1。
>
> 再接下来，同步任务 console.log( 'script end' ) 执行。第5行填入 script end。
>
> 还有第6和第7行，未填。回顾一下上面提到 async/await 的概念，其目的是为了让异步能像同步一样地书写。那么，我认为 console.log( 'async1 end' ) 就是个同步任务。所以，第6行填入async1 end。
>
> 最后，顺理成章地在第7行填入 promise2。

### Event Loop

 Event Loop 执行顺序如下所示：

- 首先执行同步代码，这属于宏任务
- 当执行完所有同步代码后，执行栈为空，查询是否有异步代码需要执行
- 执行所有微任务
- 当执行完所有微任务后，如有必要会渲染页面
- 然后开始下一轮 Event Loop，执行宏任务中的异步代码，也就是 `setTimeout` 中的回调函数

微任务包括 `process.nextTick` ，`promise` ，`MutationObserver`。

宏任务包括 `script` ， `setTimeout` ，`setInterval` ，`setImmediate` ，`I/O` ，`UI rendering`。

这里很多人会有个误区，认为微任务快于宏任务，其实是错误的。因为宏任务中包括了 `script` ，浏览器会**先执行一个宏任务**，接下来有异步代码的话才会先执行微任务。

### JS 进阶知识点及常考面试题

#### 手写call

```js
Function.prototype.myCall = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  context = context || window
  context.fn = this 	// this就是当前调用的函数
  const args = [...arguments].slice(1)
  const result = context.fn(...args)
  delete context.fn
  return result
}
```

以下是对实现的分析：

- 首先 `context` 为可选参数，如果不传的话默认上下文为 `window`
- 接下来给 `context` 创建一个 `fn` 属性，并将值设置为需要调用的函数
- 因为 `call` 可以传入多个参数作为调用函数的参数，所以需要将参数剥离出来
- 然后调用函数并将对象上的函数删除

#### 手写apply

apply第二个参数传递的是数组

```js
Function.prototype.myApply = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  context = context || window
  context.fn = this
  let result
  // 处理参数和 call 有区别
  if (arguments[1]) {
    result = context.fn(...arguments[1]) // 获取到第二个参数,它是个数组
  } else {
    result = context.fn()
  }
  delete context.fn
  return result
}
```

#### 手写bind

`bind` 需要返回一个函数，需要判断一些边界问题

```js
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  const _this = this
  const args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```

- 前几步和之前的实现大相径庭，就不赘述了
- `bind` 返回了一个函数，对于函数来说有两种方式调用，一种是直接调用，一种是通过 `new` 的方式，我们先来说直接调用的方式
- 对于直接调用来说，这里选择了 `apply` 的方式实现，但是对于参数需要注意以下情况：因为 `bind` 可以实现类似这样的代码 `f.bind(obj, 1)(2)`，所以我们需要将两边的参数拼接起来，于是就有了这样的实现 `args.concat(...arguments)`
- 最后来说通过 `new` 的方式，在之前的章节中我们学习过如何判断 `this`，对于 `new` 的情况来说，不会被任何方式改变 `this`，所以对于这种情况我们需要忽略传入的 `this`

#### new

在调用 `new` 的过程中会发生以上四件事情：

1. 新生成了一个对象
2. 链接到原型
3. 绑定 this
4. 返回新对象

### 浏览器基础知识点及常考面试题

#### 跨域

##### 表单跨域问题

​	跨域是为了阻止用户读取到另一个域名下的内容，Ajax 可以获取响应，浏览器认为这不安全，所以拦截了响应。但是表单并不会获取新的内容，所以可以发起跨域请求。

##### JSONP

JSONP 使用简单且兼容性不错，但是只限于 `get` 请求。

##### CORS

​	服务端设置 `Access-Control-Allow-Origin` 就可以开启 CORS。 该属性表示哪些域名可以访问资源，如果设置通配符则表示所有网站都可以访问资源。

```js
// 以下以 express 框架举例：
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*')
  res.header('Access-Control-Allow-Methods', 'PUT, GET, POST, DELETE, OPTIONS')
  res.header(
    'Access-Control-Allow-Headers',
    'Origin, X-Requested-With, Content-Type, Accept, Authorization, Access-Control-Allow-Credentials'
  )
  next()
})
```

#### 存储

cookie，localStorage，sessionStorage，indexDB

![image.png](https://upload-images.jianshu.io/upload_images/6541235-7740c412b23c605a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 浏览器的渲染原理

#### 为什么操作 DOM 慢?

​	因为 DOM 是属于渲染引擎中的东西，而 JS 又是 JS 引擎中的东西。当我们通过 JS 操作 DOM 的时候，其实这个操作涉及到了两个线程之间的通信，那么势必会带来一些性能上的损耗。

​	操作 DOM 次数一多，也就等同于一直在进行线程之间的通信，并且操作 DOM 可能还会带来重绘回流的情况，所以也就导致了性能上的问题。

#### 重绘（Repaint）和回流（Reflow）

- 重绘是当节点需要更改外观而不会影响布局的，比如改变 `color` 就叫称为重绘
- 回流是布局或者几何属性需要改变就称为回流。

回流**必定**会发生重绘，重绘**不一定**会引发回流。回流所需的成本比重绘高的多，改变父节点里的子节点很可能会导致父节点的一系列回流。

以下几个动作可能会导致性能问题：

- 改变 `window` 大小
- 改变字体
- 添加或删除样式
- 文字改变
- 定位或者浮动
- 盒模型

### 性能优化琐碎事

- 图片优化
- DNS预解析
- 节流
- 防抖
- 预加载
- 预渲染
- 懒加载
- CDN资源部署

#### 节流

滚动事件中会发起网络请求，但是我们并不希望用户在滚动过程中一直发起请求，而是隔一段时间发起一次，对于这种情况我们就可以使用节流。

#### 防抖

有一个按钮点击会触发网络请求，但是我们并不希望每次点击都发起网络请求，而是当用户点击按钮一段时间后没有再次点击的情况才去发起网络请求，对于这种情况我们就可以使用防抖。

### Webpack 性能优化

#### Vue常考基础知识点

​	在 `beforeCreate` 钩子函数调用的时候，是获取不到 `props` 或者 `data` 中的数据的，因为这些数据的初始化都在 `initState` 中。

​	然后会执行 `created` 钩子函数，在这一步的时候已经可以访问到之前不能访问到的数据，但是这时候组件还没被挂载，所以是看不到的。

​	接下来会先执行 `beforeMount` 钩子函数，开始创建 VDOM，最后执行 `mounted` 钩子，并将 VDOM 渲染为真实 DOM 并且渲染数据。组件中如果有子组件的话，会递归挂载子组件，只有当所有子组件全部挂载完毕，才会执行根组件的挂载钩子。

​	接下来是数据更新时会调用的钩子函数 `beforeUpdate` 和 `updated`，这两个钩子函数没什么好说的，就是分别在数据更新前和更新后会调用。

​	另外还有 `keep-alive` 独有的生命周期，分别为 `activated` 和 `deactivated` 。用 `keep-alive` 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 `deactivated` 钩子函数，命中缓存渲染后会执行 `actived` 钩子函数。

​	最后就是销毁组件的钩子函数 `beforeDestroy` 和 `destroyed`。前者适合移除事件、定时器等等，否则可能会引起内存泄露的问题。然后进行一系列的销毁操作，如果有子组件的话，也会递归销毁子组件，所有子组件都销毁完毕后才会执行根组件的 `destroyed` 钩子函数。