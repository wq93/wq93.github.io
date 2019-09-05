## 一. this

### 它的作用域

this在任何情况下都不指向函数的词法作用域. 在`JavaScript`内部,作用域确实和对象类似,可见的标识符都是它的属性. 但是作用域"对象"无法通过`JavaScript`代码访问,它存在`JavaScript`引擎内部

每当你想要把this和词法作用域的查找混合使用时,一定要提醒自己,这是无法实现的

### this是什么?

this是在运行时进行绑定的,并不是在编写时绑定的,它的上下文取决于函数调用时的各种条件. this的绑定和函数的声明的位置没有任何关系,只取决于函数的调用方法

> this实际上是在函数被调用时发生的绑定,它指向什么完全取决于函数在哪里被调用

## 二. this的全面解析

### 调用位置

调用位置就是`函数在代码中被调用的位置`

调用栈就是`为了到达当前执行位置所调用的所以函数`

### this绑定规则

#### 1. 默认绑定

独立函数调用

```js
function foo() {
    console.log(this.a)
}
var a = 2
foo() // 2
```

> 解析: foo()是直接使用不带任何修饰的函数引用进行调用的,因此只能使用`默认绑定`,无法应用其他规则

如果使用严格模式`(strict mode)`,则不能将全局对象用于默认绑定,因此this会绑定到undefined

```js
function foo() {
    "use strict"
    console.log(this.a)
}
var a =  2
foo() // TypeError: this is undefined
```

这里有一个微妙但非常重要的细节,虽然this的绑定规则完全取决于调用位置,但是只有foo()运行在非`strict mode`

下时,默认绑定才能绑定到全局对象;在严格模式下调用`foo()`则不影响默认绑定.

```js
function foo() {
    console.log(this.a)
}
var a = 2;
(function(){
    "use strict"
    foo() //2
})()
```

#### 2.隐式绑定

forexample:

```js
function foo () {
    console.log(this.a)
} 
var obj = {
    a: 2,
    foo:foo
}
obj.foo() //2
```

当函数引用有上下文对象时,隐式绑定规则会把函数调用中的this绑定到这个上下文对象

##### 例外:

对象属性引用链中只有上一层或者说最后一层在调用位置中起作用.

```js
function foo() {
    console.log(this.a)
}
var obj2 = {
    a:42,
    foo:foo
}
var obj1 = {
    a:2,
    obj2:obj2
}
obj1.obj2.foo() // 42
```

##### 隐式丢失

一个最常见的`this`绑定的问题就是被隐式绑定的函数丢失绑定对象,也就是说它会应用默认绑定,从而把this绑定到全局对象或者`undefined`上,取决于是否是严格模式

```js
function foo() {
    console.log(this.a)
}
var obj = {
    a:2,
    foo:foo
}
var bar = obj.foo // 函数别名
var a = 'oops,global'
bar() // oops,global
```

> 解析:
>
> 虽然bar是`obj.foo`的一个引用,但是实际上,它引用的是`foo`函数本身,因此此时的`bar()`其实是一个不带任何修饰的函数调用,使用的默认绑定

```js
function foo() {
    console.log(this.a)
}
function doFoo(fn){
    // fn其实引用的是foo
    fn(); // <--调用位置
}
var obj = {
    a:2,
    foo:foo,
}
var a = 'oops,global'
doFoo(obj.foo) // oops,global

```

> 解析: 
>
> 参数传递其实就是一种隐式赋值,因此我们传入函数也会被隐式赋值

- 回调函数丢失this绑定是非常常见的,接下来学习如何通过固定this来修复这个问题



#### 3.显式绑定

`JavaScript`提供的绝大多数函数以及我们自己创建的所有函数都可以使用`call(...)`和`apply(...)`方法

它们的第一个参数是一个对象,是给this准备的,接着在调用函数时将其绑定到this. 因为你可以直接指定this的绑定对象,因此我们称之为显示绑定. 

##### 3.1 硬绑定

硬绑定是一种非常常见的模式,ES5提供了内置的方法`Function.prototype.bind(...)`

`bind(...)`会返回一个硬编码的新函数,它会把你指定的参数设置为this的上下文并调用原始函数

#### 4.new绑定

包括内置函数和自定义函数在内的所有函数都可以用new来调用,这种函数调用被称为构造函数调用. `实际上并不存在所谓的"构造函数",只有对于函数的构造调用`

使用new来调用函数,或者说发生构造函数调用时,会自动执行下面的操作

1. 创建一个全新的对象
2. 这个对象会被执行[[Prototype]]链接
3. 这个新对象会绑定到函数调用的this
4. 如果函数没有返回其他对象,那么new表达式中的函数调用会自动返回这个新对象

#### 判断this(重要!!!)

按照下面的顺序进行判断(记住特例):

1. 函数是否在new中调用(new 绑定)?如果是的话this绑定的是新创建的对象 `var bar = new foo()`
2. 函数是否通过call或apply(显示绑定)或者硬绑定调用? 如果是的话, this绑定的是指定的对象 `var bar = foo.call(obj2)`
3. 函数是否在某个上下文对象中调用(隐式绑定)?如果是的话,this绑定的是那个上下文对象 `var bar = obj.foo()`
4. 如果都不是的话,使用的是默认绑定. 如果在严格模式下,就绑定到undefined,否则绑定的全局对象 `var bar = foo()`

> 不过...凡是都有例外,接下来我们介绍例外吧

### this的绑定例外

1. 被忽略的this

如果我们把`null`或`undefined`作为`this`的绑定对象传入`call` `apply` 或者`bind`,这些值在调用时会被忽略,实际应用的是默认绑定

```js
function foo() {
    console.log(this.a)
}
var a = 2
foo.call(null) // 2
```

柯里化传入更安全的this

在JavaScript中创建一个空对象最简单的方法是`Object.create(null)`,它不会创建`Object.prototype`这个委托,所以比`{}`更空

```js
function foo(a,b) {
    console.log("a" + a + ", b :" + b)
}
// DMZ空对象
var Ø = Object.create(null)
// 把数组展开成参数
foo.apply(Ø,[2,3]) //a:2,b:3
//使用bind(...)进行柯里化
var bar = foo.bind(Ø,2)
bar(3) //a:2,b:3
```

2. 间接引用

```js
var a = 2
var o = {a:3,foo:foo}
var p = {a:4}
o.foo() // 3
(p.foo = o.foo)() // 2
```

赋值表达式`p.foo = o.foo`的返回值是目标函数的引用,因此调用位置是foo()而不是`p.foo()`或`0.foo()`,故这里会应用默认绑定

### 箭头函数当作对象的属性的值

```js
Element.prototype.hide = () => { this.style.display = 'none' }
```

会报错，查看babel解析后的代码，发现this没有绑定上：

```js
Element.prototype.hide = function() {
    undefined.style.display = 'none'    
}
```

​	箭头函数的 this 是静态的，也就是说，只需要看箭头函数在什么函数作用域下**声明**的，那么这个 this 就会绑定到这个函数的上下文中。即“穿透”箭头函数。

例子里的箭头函数并没有在哪个函数里声明，所以 this 会 fallback 到全局/undefined

"穿透"到最近的词法作用域(注意对象的{}不算外层作用域),如果外层没有被函数包裹,那么就是window

例如:

```js
 let a = {
    foo() {
      console.log(this)
    },
    foo1: () => {
      console.log(this)
    },
    foo2: function foo2() {
      console.log(this)
    }
  } 
  a.foo() // a
  a.foo1() // window
  a.foo2() // a

```



## 三. 小结

如果要判定一个运行函数的this绑定,就需要找到这个函数的直接调用位置. 找到之后可以顺序应用下面这四条规则来判断this的绑定函数

1. 由new调用? 绑定到新创建的对象 `(new绑定)`
2. 由call或apply(或者bind)调用? 绑定到指定的对象 `(显示绑定)`
3. 由上下文调用? 绑定到那个上下文对象 `(隐式绑定)`
4. 默认绑定: 在严格模式下绑定到undefined,否则会绑定到全局 `(默认绑定)`

> 注意: 有些调用可能在无意中使用默认绑定规则. 如果想'更安全'地忽略this绑定,你可以使用一个DMZ对象,
>
> 比如 `var Ø = Object.create(null)`,以保护全局对象

ES6中的箭头函数并不会使用四条标准的绑定规则,而是根据当前的词法作用域来决定this,具体说,箭头函数会继承外层函数调用的this绑定(无论this绑定到什么). 这其实和ES6之前的var self = this的机制一样
