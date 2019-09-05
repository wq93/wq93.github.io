# 你不知道的JavaScript(上)

-----

## ①  作用域和闭包

### 一.  作用域是什么?

作用域是一套规则,用于在何处以及如何查找变量(标识符).如果查找的目的是对变量进行赋值,那么就行使用`LHS`查询;如果目的是获取变量的值,就会使用`RHS`查询.赋值操作会导致`LHS`查询. `=`操作符或调用函数时传入参数的操作都会导致关联作用域的赋值操作.

 * PS: 对变量赋值`LHS`,为变量取值`RHS`

` JavaScript`引擎首先会在代码执行前对其编译,在这个过程中,像`var a = 2`这样的声明被分解成两个独立的步骤:
1)  首先,`var a `在其作用域中声明新变量.这会在最开始的阶段,也就是代码执行前进行.
2)  接下来,`a=2`会查询(LHS查询)变量a并对其进行赋值
`LHS`和`RHS`查询都会在当前执行作用域中开始,如果有需要(没有找到所需的标识符),就会向上级作用域继续查找目标标识符,这样每次上升一级,最后抵达全局作用域,无论找到或没找到都将停止.

- PS: 把作用域链比喻成一栋建筑

 不成功的`RHS`引用会导致抛出`ReferenceError`异常. 不成功的`LHS`引用会导致自动隐式地创建一个全局变量(非严格模式下),该变量使用`LHS`引用的目标作为标识符,或者抛出`ReferenceError`(严格模式下)

对变量赋值`LHS`,为变量取值`RHS`

#### LHS与RHS

	对变量赋值`LHS`,为变量取值`RHS`

### 二.  词法作用域

词法作用域意味着作用域是由书写代码时函数声明的位置决定. 编译的词法分析阶段基本能够知道全部标识符在那里以及如何声明的,从而能够预测在执行过程中如何对它们进行查找.

JavaScript中有两个机制可以"欺骗"词法作用域: `eval(...)`和`with`. 前者可以对一段包含一个或多个声明的"代码"字符串进行演算,并借此来修改已存在的词法作用域. 后者本质上是通过一个对象的引用当作作用域来处理,将对象的属性当作作用域中的标识符来处理,从而创建一个新的词法作用域.

这两个机制的副作用是引擎无法在编译时对作用域查找进行优化,因为引擎只能谨慎的认为这样的优化是无效的.使用这其中一种机制都将导致代码运行变慢.~~不要使用它们~~

### 三.  函数作用域和块作用域



函数是JavaScript中最常见的作用域单元. 本质上,声明在一个函数内部的变量或者函数会在所处的作用域中被"隐藏"起来,这是有意为之的良好软件的设计原则

但函数不是唯一的作用域单元. 块作用域指的是变量和函数不仅可以属于所处的作用域也可以属于某个代码块.

- 从ES3开始,`try/catch`结构在`catch`分句中具有块作用域

在ES6中引入了`let`关键字,用来在任何代码块中声明变量,`if(..){let a = 2}`会声明一个劫持```if```的`{...}`块的变量,并将变量添加到这个块中.

有些人认为块作用域不应该完全作为函数作用域的替代方案.两种功能应该同时存在,开发者可以并且也应该根据需要选择使何种作用域,创造可读,可维护的优良代码

### 四. 变量提升

我们习惯将`var a = 2`;看作是一个声明,而实际上JS引擎并不认为. 它将`var a`和 `a=2`当作两个单独的声明,第一个是`编译阶段`的任务,而第二个则是`执行阶段`的任务

这意味着无论作用域中的声明出现在什么地方,都将在代码本身被执行前首先进行处理,可以将这个过程形象地想象成所有的声明(变量和函数)都会被"移动"到各自作用域的最顶端,这个过程称为`提升`

声明本身会被提升,而包括函数表达式的赋值在内的赋值操作并不会提升.

要注意避免重复声明,特别是当普通的`var`声明和函数声明混合在一起的时候,否则会引起很多危险的问题!

PS: 函数声明和变量声明都会被提升. 但是一个值得注意的细节(这个细节可以出现在有多个'重复'声明的代码中)是函数会首先被提升,然后才是变量.

```js
foo() //1
var foo
function foo() {
    console.log('1')
}
foo = function() {
    console.log('2')
}

```

### 五. 闭包

闭包无处不在,你只需要识别并拥抱它

- 闭包的模型

```js
function foo() {
    var a = 2
    function bar() {
        console.log(a)
    }
    return bar
}
var baz = foo()
baz() //2
```

  在这个例子中,它在自己定义的词法作用域以外的地方执行

在`foo()`执行后,通常会期待`foo()`的整个内部作用域都被销毁,因为我们知道引擎有垃圾回收器用来释放不再使用的内存空间. 由于看上去`foo()`的内容不会再被使用,所以我们会认为垃圾回收机制会将其回收

而闭包可以阻止这件事的发生. 拜`bar()`所声明的位置所赐,它拥有涵盖`foo()`内部作用域的闭包,使得该作用域一直存活,以供`bar()`在之后任何时间进行引用

> `bar()`依然持有对该作用域的引用,而这个引用就叫作闭包

当然,无论使用何种方式对函数类型的值进行传递,当函数在别处被调用时都可以观察到闭包

```js
function foo () {
    var a = 2
    function baz() {
        console.log(a) //2
    }
    bar(baz)
}
function bar(fn) {
    fn() //这就是闭包
}

```

```js
var fn
function foo() {
    var a = 2
    function baz() {
        console.log(a)
    }
    fn = baz // 将baz分配给全局变量
}
function bar() {
    fn() // 这就是闭包
}
foo()
bar() //2
```

> 无论通过何种手段将内部函数传递到所在词法作用域以外,它都会持有对原始定义作用域的引用,无论在何处执行这个函数都会使用闭包

#### 闭包的场景

PS:定时器中的闭包

```js
function wait(mes) {
    setTimeout(function timer(){
        console.log(mes) //这就是闭包
    },1000)
}
```

> 解析: 将一个内部函数传递给`setTimeout(...)`.  `timer`具有涵盖`wait(...)`作用域的闭包,因此还保有对变量message的引用.

- 在`定时器,事件监听,Ajax请求,垮窗口通信,Web Workers或者任何其的异步任务中,`只要使用了回调函数,实际上就是在使用闭包

#### 循环和闭包

PS:典型例子

```js
for(var i = 1;i<=5;i++) {
    setTimeout(function timer(){
        console.log(i) // 每秒一次的频率输出五次6
    },i*1000)
}
```

> 解析:  
>
> 1. 6从哪儿来?
>
>    循环终止条件是i不再`<=5`,条件首次成立时i的值是6
>
> 2. 运行机制
>
>    根据作用域的工作原理,实际情况是尽管循环中五个函数是在各个迭代中分别定义的,但是它们都被封闭在一个共享的全局作用域中,因此实际上是同一个并仅有一个i

改进: 

```js
for(var i=1;i<=5;i++) {
    (function(j){
        setTimeout(function timer(){
            console.log(j)
        },j*1000)
    })(i)
}
```

> 解析:
>
> 在迭代中使用IIFE会为每次迭代都生成一个新的作用域,使得延迟函数的回调可以将新的作用域封闭在每个迭代内部,每个迭代中都会含有一个具有正确值的变量提供我们访问

进一步改进:

```js
for(var i=1;i<=5;i++){
    let j=i // 闭包的块作用域
    setTimeout(function timer(){
    	console.log(j)
    },j*1000)
}
```

终极模式:

```js
for(let i=1;i<=5;i++){
    setTimeout(function timer(){
    	console.log(i)
    },i*1000)
}
```

> 解析:
>
> `let`声明变量有一个特殊行为,指的是变量在循环过程中不止声明一次,每次迭代都会声明. 随后的每个迭代都会使用上一个迭代结束的值来初始化这个变量

#### 模块

```js
function CoolModule(){
    var something = 'cool'
    var another = [1,2,3]
    function doSomething() {
        console.log(something)
    }
    
    function doAnother() {
        console.log(another.join('!'))
    }
    return {
        doSomething:doSomething,
        doAnother:doAnother
    }
}
```

`CoolModule()`只是一个函数,必须要通过调用它来创建一个模块实例. 如果不执行外部函数,内部作用域和闭包都无法创建.

> 1. 必须有外部的封闭函数,该函数必须至少调用一次(每次调用都会创建一个新的模块实例)
> 2. 封闭函数必须返回至少一个内部函数,这样内部函数才能在私有作用域中形成闭包,并且可以访问或者修改私有的状态

#### 小结

闭包实际上是一个普通且明显的事实,那就是我们在词法作用域的环境下写代码,而其中的函数也是值,我们可以开心的传来传去

`当函数可以记住并访问所在的词法作用域,即使函数是在当前词法作用域之外执行,这时就产生了闭包`

模块有两个主要的特征: (1)为创建内部作用域而调用一个包装函数 (2) 包装函数的返回值必须至少包括一个对内部函数的引用,这样就会创建涵盖整个函数内部作用域的闭包.

### 六. 附录

#### 动态作用域

动态作用域并不关心函数和作用域是如何声明以及在何处声明的,只关心它们从何处调用. 换句话是,作用域链是基于调用栈,而不是代码中的作用域嵌套. 

```js
function foo(){
    console.log(a) // 2 (不是3)
}
function bar() {
    var a = 3
    foo()
}
var a = 2
bar()
```

> 解析: 
>
> 事实上JavaScript并不具有动态作用域. 它只有词法作用域,简单明了. 但是this的动态机制某种程度上很像动态作用域

##### 主要区别

词法作用域是在写代码或者定义时确定的,而动态作用域是在运行时确定的.(this也是) 词法作用域关注函数在何处声明,而动态作用域关注函数从何处调用.

#### 块作用域的替代方案

##### foeExample:

```js
{
  let a = 2
  console.log(2) // 2
}
console.log(a) // ReferenceError

// =====> ES6之前
try{
   throw 2
 }catch (a) {
   console.log(2)
 }
 console.log(a) // ReferenceError

```

##### try/catch性能

问: 为什么不直接使用IIFE来创建作用域?

> 答:
>
> 首先,`try/catch`的性能的确糟糕,但技术层面上没有合理的理由来说明`try/catch`必须这么慢,或者会一直这么慢下去. 自从TC39支持在ES6的转换器中使用`try/catch`后,Traceur团队已经要求chrome对`try/catch`的性能进行改进,他们显然有很充分的动机来做这件事情
>
> 其次: IIFE和try/catch并不是等价的，因为如果将一段代码中的任意一部分拿出来用函数进行包裹，会改变这段代码的含义，其中this，return，break和continue都会发生变化. IIFE并不是一个普通的解决方案，它只适应在某些情况下进行手动操作

##### 匿名函数没有name标识符,会导致?

1. 调用栈更难追踪
2. 自我引用更难
3. 代码更难理解

#### this的词法

##### 箭头函数的this

箭头函数在涉及this绑定的行为和普通函数的行为完全不一致. 它放弃了所以普通this绑定的规则,取而代之的是用当前的词法作用域覆盖了this的值(箭头函数不止于少写代码)

## ② this和对象原型

### 一.  关于this

#### 它的作用域

this在任何情况下都不指向函数的词法作用域. 在`JavaScript`内部,作用域确实和对象类似,可见的标识符都是它的属性. 但是作用域"对象"无法通过`JavaScript`代码访问,它存在`JavaScript`引擎内部

每当你想要把this和词法作用域的查找混合使用时,一定要提醒自己,这是无法实现的

#### this是什么?

this是在运行时进行绑定的,并不是在编写时绑定的,它的上下文取决于函数调用时的各种条件. this的绑定和函数的声明的位置没有任何关系,只取决于函数的调用方法

> this实际上是在函数被调用时发生的绑定,它指向什么完全取决于函数在哪里被调用

### 二. this的全面解析

#### 调用位置

调用位置就是`函数在代码中被调用的位置`

调用栈就是`为了到达当前执行位置所调用的所以函数`

#### this绑定规则

##### 1. 默认绑定

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
var a = 2
(function(){
    "use strict"
    foo() //2
})()
```

##### 2.隐式绑定

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

###### 例外:

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

###### 隐式丢失

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
    a:2
    foo:foo
}
var a = 'oops,global'
doFoo(obj.foo) // oops,global

```

> 解析: 
>
> 参数传递其实就是一种隐式赋值,因此我们传入函数也会被隐式赋值

- 回调函数丢失this绑定是非常常见的,接下来学习如何通过固定this来修复这个问题



##### 3.显式绑定

`JavaScript`提供的绝大多数函数以及我们自己创建的所有函数都可以使用`call(...)`和`apply(...)`方法

它们的第一个参数是一个对象,是给this准备的,接着在调用函数时将其绑定到this. 因为你可以直接指定this的绑定对象,因此我们称之为显示绑定. 

###### 3.1 硬绑定

硬绑定是一种非常常见的模式,ES5提供了内置的方法`Function.prototype.bind(...)`

`bind(...)`会返回一个硬编码的新函数,它会把你指定的参数设置为this的上下文并调用原始函数

##### 4.new绑定

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

#### this的绑定例外

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

#### 箭头函数当作对象的属性的值

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



#### 小结

如果要判定一个运行函数的this绑定,就需要找到这个函数的直接调用位置. 找到之后可以顺序应用下面这四条规则来判断this的绑定函数

1. 由new调用? 绑定到新创建的对象 `(new绑定)`
2. 由call或apply(或者bind)调用? 绑定到指定的对象 `(显示绑定)`
3. 由上下文调用? 绑定到那个上下文对象 `(隐式绑定)`
4. 默认绑定: 在严格模式下绑定到undefined,否则会绑定到全局 `(默认绑定)`

> 注意: 有些调用可能在无意中使用默认绑定规则. 如果想'更安全'地忽略this绑定,你可以使用一个DMZ对象,
>
> 比如 `var Ø = Object.create(null)`,以保护全局对象

ES6中的箭头函数并不会使用四条标准的绑定规则,而是根据当前的词法作用域来决定this,具体说,箭头函数会继承外层函数调用的this绑定(无论this绑定到什么). 这其实和ES6之前的var self = this的机制一样


### 三. 对象

#### 基本类型

1. string
2. number
3. boolean
4. null
5. undefined
6. object

> 注意:
>
> `null`有时会被当作一种对象类型,但是这其实只是语言本身的一个bug,即对`null`执行`typeof null`时返回字符串"object". 实际上,`null`本身是基本类型
>
> 黑科技: 原理是这样的,不同的对象在底层都表示为二进制,在`JavaScript`中二进制前三位都为0的话会被判断为`object`类型,`null`的二进制表示是全0,自然前三位也是0,所以`typerof `时会返回"object"

#### 内置对象

1. String
2. Number
3. Boolean
4. Object
5. Function
6. Array
7. Date
8. RegExp
9. Error

必要时语言会自动把字符串字面量转换成一个String对象

在对象中，属性名永远都是字符串。如果使用string以外的其他值作为属性名，它首先会被转换为一个字符串。

#### 复制对象（待解决）

##### 浅拷贝



##### 深拷贝（`JSON.stringify(obj)`）



##### 属性描述符

###### 1. writable

是否可以修改属性的值

###### 2.configurable

属性是否可配置,把configurable修改成false是单向操作,无法撤销

除了无法修改,`configurable:false`还会禁止这个属性

###### 3.Enumerable

属性是否会出现在对象的属性枚举中

###### 4.不变性

所有的方法创建的都是浅不变性,它们只会影响目标对象和它的直接属性.如果目标对象引用了其他对象,其他对象的内容不受影响,仍然是可变的

4.1 对象常量

结合`writable:false`和`configurable:flase`就可以创建一个真正的常量属性(不可修改,不可重新定义或删除)

```js
var object1 = {}
Object.defineProperty(object1,"FAVORITE_NUMBER",{
    value:42,
    writable:false,
    configurable:false
})
```

4.2 禁止扩展

如果想禁止一个对象添加新属性并且保留已有属性,可以使用`Object.preventExtensions()`

```js
var myObj = { a : 2 }
Object.preventExtensions(myObj)
myObj.a = 3
myObj.a = undefined
```

在非严格模式下,创建属性a会静默失败. 在严格模式下,将会抛出`TypeError`错误

4.3 密封

`Object.seal(...)`会创建一个密封的对象,这个方法实际上会在一个现有对象上调用`Object.preventExtensions(...)`并将所有现有的属性标记为`configurable:flase`

所以,密封后的对象不能添加属性,不能重新配置属性或者删除现有属性(`只能修改属性的值`)

4.4  冻结

`Object.freeze(...)`会创建一个冻结对象,这个方法实际上会在一个现有对象上调用`Object.seal(...)`并把所有"数据访问"属性标记为`writable:fasle`,这样就无法修改它们的值

> 这个方法是可以应用在对象上的级别最高的不可变性



###### 5.[[Get]]

在语言规范中,`obj.a`在obj上实际上是实现了[[Get]]操作. 对象默认的内置[[Get]]操作首先在对象上查找是否有名称相同的属性,如果找到就会返回这个属性的值

如果无论如何都没有找到名称相同的属性,那么[[Get]]操作会返回值undefined

```js
var myObj = {
    a: 2
}
myObj.b // undefined
```

> 这种方法和访问变量是不一样的. 如果你引用了一个当前词法作用域中不存在的变量,并不会像对象属性一样返回`undefined`,而是会抛出一个`ReferenceError`异常
>
> 故,仅通过返回值,你无法判断一个属性是存在并且持有一个`undefined`值,还是变量不存在,所以`[[Get]]`无法返回某个特定值而返回默认的`undefined`

###### 6.[[Put]]

[[Put]]被触发时,实际行为分成两种:

1. 已经存在这个属性

- 1. 属性是否是访问描述符? 如果是并且存在setter就调用setter
  2. 属性的数据描述符中`writable`是否是false? 如果是,在非严格模式下静默失败,在严格模式下抛出`TypeError`异常
  3. 如果都不是,将该值设置为属性值

2. 不存在这个属性

###### 7.Getter函数和Setter函数

在ES5中可以使用getter和setter部分改写默认操作,但是只能应用在单个属性上,无法应用在整个属性上.

getter和setter都会覆盖单个属性默认的[[Getter]]和[[Setter]]操作

###### 8.判断属性的存在性

当我们通过属性名访问某个值时可能返回`undefined`,这个值可能是对象属性中存储的`undefined`,也有可能是属性不存在返回的`undefined`,那么我们怎么区分呢?

Forexample:

```js
var obj = {
    a:2
}
('a' in obj) // true
('b' in obj) // false
obj.hasOwnProperty('a') //true
obj.hasOwnProperty('b') // fasle
```

in 操作符会检查属性是否在对象及其`[[Prototype]]`原型链中,`hasOwnProperty(...)`只会检查属性是否在对象中,不会去检查`[[Prototype]]`链

> 看起来in操作符可以检查容器是否有某个值,但是它实际上检查的是某个属性名是否存在.
>
> PS:
>
> ```js
> 4 in [1,2,4] // false
> // 该数组包含的属性名是0 1 2 并没有我们要找的4
> ```

###### 9.遍历

最好只在对象上应用for...in循环中

#### 小结

JavaScript中的对象有字面形式(`var a= {...}`)和构造形式(`var  a = new Array(...)`)

"万物皆对象"的概念是错误的. 对象是6个或者7个(null)基础类型之一. 对象有包括function在内的子类型,不同子类型具有不同的行为,比如内部标签`[object Array]`表示是对象的子类型数组

对象就是键值对的集合. 可以通过`.propName`或者`['propName']`语法来获取属性值. 访问属性时,引擎实际上会调用内部的默认`[[Get]]`操作(在设置属性值时是`[[Put]]`),`[[Get]]`操作检查对象本身是否包含这个属性,如果没找到的话还会查找`[[Prototype]]`链

属性的特性可以通过属性描述符来控制,比如`writable`和`configurable`. 还可以使用`Object.preventExtensions(...)`,`Object.seal(...)`和`Object.freeze(...)`来设置对象的不可变性级别,其中`Object.freeze(...)`是应用在对象上不可变性的最高级别.

属性不一定包含值-它们可能是具备`getter/setter`的"访问描述符". 此外属性可以是可枚举或不可枚举的,这决定了它们是否会出现在`for...in`循环中

可以使用`for...of`遍历数据结构(数组,对象等等)中的值,`for...of`会寻找内置或者定义的@@iterator对象并调用它的`next()`方法来遍历数据值

### 四. 混合对象"类"

#### 构造函数

类实例是由一个特殊的类方法构造的,这个方法名通常和类名相同,被称为构造函数. 这个方法的任务就是初始化实例需要的所有信息

#### 类的继承

##### 多态(super关键字)

在传统的面向对象的语言中`super`还有一个功能,就是从子类的构造函数中通过`super`可以直接调用父类的构造函数.通常来说这没什么问题,因为对于真正的类来说,构造函数是属于类的.

然而,在`JavaScript`中恰好相反-实际上类是属于构造函数的. 由于JavaScript中父类和子类的关系只存在于两者构造函数对应的`.prototype`对象中,因此它们的构造函数之间并不存在直接关系,从而无法简单地实现两者的相对引用.

#### 小结

类是一种设计模式. 许多语言提供了对于面向对象类软件设计的原生语法. JavaScript也有类似的语法,但是和其他语言中的类完全不一样

`类意味着复制`

传统的类实例化时,它的行为会被复制到实例中. 类被继承时,行为也会复制到子类中

多态(在继承链的不同层次名称相同但是功能不同的函数)看起来似乎是从子类引用父类,但是本质上引用的其实是复制的结果

`JavaScript不会(像类那样)自动创建对象的副本, 只能复制引用,无法复制被引用的对象或者函数本身`

`混入模式`(利用for...in遍历判断对象不存在的属性,不存在则添加)可以用来模拟类的复制行为,但是通常会产生丑陋并且脆弱的语法,比如显式伪多态(`Object.methidName.call(this,....)`),这会让代码更难懂并且难以维护.

显示混入实际上无法完全模拟类的复制行为,因为对象(和函数,函数也是对象)只能复制引用,无法复制被引用的对象或者函数本身. 

总地来说,在JavaScript中模拟类是得不偿失的,虽然能解决当前的问题,但是可能会埋下更多的隐患.

### 五. 原型

#### 1.[[Prototype]]

`JavaScript`中的对象有一个特殊的`[[prototype]]`内置属性,其实就是对于其他对象的引用. 几乎所有的对象在创建时`[[Prototype]]`属性都会被赋予一个非空的值

```js
var anotherObj = {
    a:2
}
// 创建一个关联到 antherObj 的对象
var myObj = Object.create(anotherObj)
```

##### 1.1 Object.prototype

所有普通的[[prototype]]链最终都会指向内置的`Object.prototype`

##### 1.2属性设置和屏蔽

在[第三部分对象中]提到过,给一个对象设置属性并不仅仅是添加一个新属性或者修改已有的属性值

```js
myObj.foo = 'bar'
```

- 如果`myObj`对象中包含名为`foo`的普通数据访问属性,这条赋值语句只会修改已有的属性值
- 如果`foo`不是直接存在于`myObj`中,`[[Prototype]]`链就会被遍历,类似`[[Get]]`操作. 如果原型链找不到`foo`,`foo`就会被直接添加到`myObj`上
- 如果`foo`存在原型链上层,赋值语句`myObj.foo = 'bar'`的行为就会有些不同,下面是详细的介绍
- 如果属性名foo既出现在`myObj`中也出现在`myObj`的`[[Prototype]]`链上层,那么就会发生屏蔽. `myObj`中包含的`foo`属性会屏蔽原型链上层的所有`foo`属性,因为`myObj.foo`总是会选择原型链中最底层的`foo`属性

>  分析如果foo不直接存在`myObj`中而是存在原型链上层时,`myObj.foo = 'bar'`会出现三种情况 >>>
>
> - 如果在`[[Prototype]]`链上层存在名为foo的普通数据访问属性`并且没有`被标记只读,那会直接在`myObj`中添加一个名为foo的新属性,它是`屏蔽属性`
> - 如果在`[[Prototype]]`链上层存在foo,但是它被标记为只读,那么无法修改已有属性或者在myObj上创建屏蔽属性. 如果运行在严格模式下,代码会抛出一个错误. 否则, 这条赋值语句会被忽略. 总之,不会发生屏蔽
> - 如果在`[[Prototype]]`链上层存在foo属性并且它是一个setter,那就一定会调用这个`setter`. `foo`不会被添加到`myObj`,也不会重新定义`foo`这个`setter`
>
> 只读属性会阻止`[[Prototype]]`链下层隐式创建(屏蔽)属. 这看起来有点奇怪,myObj对象会因为有一个只读foo就不能包含foo属性. 更奇怪的是,这个限制只存在于=赋值中,使用`object.defineProperty(...)`并不会受到影响

#### 2."类"

##### 2.1 类函数

通过调用`new Foo()`创建的每个对象将最终被`[[Prototype]]`链接到这个`Foo.prototype`对象

```js
function Foo() {
    ...
}
var a = new Foo()
Object.getPrototypeOf(a) === Foo.prototype // true
```

在面向对象的语言中,类可以被复制多次,就像模具制作东西一样.

但是在`JavaScript`中,并没有类似的复制机制. 我们不能创建一个类的多个实例,只能创建多个对象,它们的`[[Prototype]]`关联的是同一个对象. 但是在默认情况下并不会进行复制,因此这些对象之间并不完全失去联系,它们是互相关联的.

###### 关于名称

"原型继承"严重影响了大家对`JavaScript`机制真实原理的理解

继承意味着复制操作,`JavaScript`并不会复制对象属性. 相反,`JavaScript`会在两个对象之间创建一个关联,这样一个对象就可以通过委托访问到另一个对象的属性和函数

##### 2.2 "构造函数"

```js
function Foo() {
    // ...
}
Foo.prototype.constructor === Foo // true
var a = new Foo()
ａ.constructor === Foo // true
```

`Foo.prototype`默认有一个公有并且不可枚举的属性`.constructor`,这个属性引用的是对象关联的函数.可以看到通过"构造函数"调用`new Foo(...)`创建的对象也有一个`.constructor`属性,指向"创建这个对象的函数"

###### 构造函数还是调用?

`new`会劫持所有普通函数并构造对象的形式来调用它

```js
function NothingSpecial() {
    console.log("Don't mind me")
}
var a = new NothingSpecial()
// Don't mind me
a // {}
```

在`JavaScript`中对于"构造函数"最准确的解释是,所有带new的函数调用

函数不是构造函数,但是当且使用new时,函数调用会变成"构造函数调用"

##### 构造函数返回值的问题

1. 没有返回值的情况像其他传统语言一样，返回实例化的对象

```js
function Person(){

    this.name="monster1935";
    this.age='24';
    this.sex="male";

}
console.log(Person());  //undefined
console.log(new Person());//Person {name: "monster1935", age: "24", sex: "male"}
```

2. 如果存在返回值则检查其返回值是否为引用类型，如果为非引用类型，如（`string`,`number`,`boolean`,`null`,`undefined`），上述几种类型的情况与没有返回值的情况相同，实际返回实例化的对象

```js
function Person(){

    this.name="monster1935";
    this.age='24';
    this.sex="male";

    return "monster1935";

}
console.log(Person());  //monster1935
console.log(new Person());//Person {name: "monster1935", age: "24", sex: "male"}
```

3. 如果存在返回值是引用类型，则实际返回该引用类型

```js
function Person(){

    this.name="monster1935";
    this.age='24';
    this.sex="male";

    return {
        name:'Object',
        age:'12',
        sex:'female'
    }

}
console.log(Person());  //Object {name: "Object", age: "12", sex: "female"}
console.log(new Person());//Object {name: "Object", age: "12", sex: "female"}
```



#### 3.(原型)继承

```js
function Foo(name) {
    this.name = name
}
Foo.prototype.myName = function() {
    return this.name
}
function Bar(name,label) {
    Foo.call(this,name)
    this.label = label
}
// 创建一个新的Bar.prototype对象并关联到Foo.prototype
Bar.prototype = Object.create(Foo.prototype)
//注意 现在没有Bar.prototype.constructor了
Bar.prototype.myLabel = function() {
    return this.label
}
var a = new Bar("a","obj.a")
a.myName() // "a"
a.myLabel() // "obj.a"
```

> 注意: 下面这两种方式是常见的错误做法,实际上它们都存在一些问题
>
> ```js
> Bar.prototype = Foo.prototype // 直接引用的是Foo.prototype对象,赋值语句会互相修改
> // 基本满足要求,但是可能会产生一些副作用
> Bar.prototype = new Foo()
> ```
>
> 因此,要创建一个合适的对象,我们必须使用`Object.create(...)`而不是使用具有副作用的`Foo(...)`
>
> 这样唯一的缺点就是创建一个新对象然后把旧对象抛弃掉,不能直接修改已有的默认对象

###### 两种关联的方式

```js
// ES6之前需要抛弃默认的Bar.prototype
Bar.prototype = Object.create(Foo.prototype)

// ES6开始可以直接修改现有的Bar.prototype
Object.setPrototypeOf(Bar.prototype,Foo.prototype)
```

###### 检查"类"关系

- `instanceOf`

  `a instanceOf  Foo`, 左边是一个普通的对象,右边是一个函数. 回答的问题是:"在a的整条[[Prototype]]链中是否有Foo.prototype指向的对象?"

- `isPrototypeOf`

  `Foo.isPrototypeOf(a)`, 回答的问题是:"在a的整条[[Prototype]]链中是否出现过Foo.prototype"

###### 获取对象的原型链

`Object.getPrototypeOf(a)`

浏览器也支持一种非标准的方法访问内部的[[Prototype]]属性

`a._proto_` (是可设置属性)

##### 4.对象关联

###### 原型链的概念

如果在对象上没有找到需要的属性或者方法引用,引擎就会继续在[[Prototype]]关联的对象上进行查找. 如果后者中也没有找到需要的引用就会继续查找它的[[Prototype]],以此类推,这一系列的链接称为"原型链"

###### `Object.create(...)`

Object.create(...)会创建一个对象并把它关联到我们指定的对象

Object.create(null)会创建一个拥有空[[Prototype]]链接的对象,这个对象无法进行委托. 由于这个对象没有原型链,所以instanceOf操作符无法进行判断

#### 小结

如果要访问对象中并不存在的一个属性,[[Get]]操作就会查找对象内部[[Prototype]]关联的对象. 这个关联关系实际上定义一条"原型链",在查找属性时就会对它进行遍历

所有普通对象都有内置的Object.prototype,指向原型链的顶端,如果在原型链中找不到指定的属性就会停止. `toString()`,`valuOf()`和其他一些通用的功能都存在于Object.prototype对象上,因此语言中所有的对象都可以使用他没

关联两个对象最常用的方法是使用new关键字进行函数调用,在调用的4个步骤中会创建一个关联其他对象的新对象

1. 创建一个全新的对象
2. 这个对象会被执行[[Prototype]]链接
3. 这个新对象会绑定到函数调用的this
4. 如果函数没有返回其他对象,那么new表达式中的函数调用会自动返回这个新对象

使用new调用函数时会把新对象的.prototype属性关联到"其他对象". 带new的函数调用通常被称为"构造函数调用",尽管它们实际上和传统面向类语言中的构造函数不一样

虽然这些JavaScript机制和传统面向对象的"类初始化"和"类继承"很相似,但是`JavaScript`中的机制有一个核心区别,那就是不会进行复制,对象之间是通过内部的[[Prototype]]链关联的

出于各种原因,以"继承"结尾的术语和其它面向对象的术语都无法帮助你理解`JavaScript`的真实机制,相比之下,`"委托"是一个更合适的术语,因为对象之间的关系不是复制而是委托`

### 六.行为委托

`JavaScript`中原型链这个机制的本质就行对象之间的关联关系

Js中函数之所以可以访问`call(...)`,`apply(...)`,`bind(...)`是因为函数本身是对象

#### 1.委托理念

行为委托认为对象之间是兄弟关系,互相委托,而不是父类和子类关系.`JavaScript`的[[Prototype]]机制本质上就是行为委托节制. 也就是说,我们可以选择在Js中努力实现类机制,也可以拥抱更自然的[[Prototype]]委托机制

#### 2.类与对象

##### ES6的class语法糖

见下章

### 七. ES6中的class

传统面向类的语言中父类和子类,子类和实例之间其实是复制操作,但是在`[[Prototype]]`中没有复制,相反,它们之间只有委托关联

### 1.class

```js
class Widget{
    constructor(width,height) {
        this,width = width || 50
        this.height = height || 50
        this.$elem = null
    }
    render($where) {
        if(this.$elem) {
            this.$elem.css({
            	width:this.width + 'px',
            	height:this.height+'px'
            }).appendTo($where)
        }
    }
}
class Button extends Widget {
  constructor(width, height, label) {
    super(width, height)
    this.label = label || 'Default'
    this.$elem = $("<button>").text(this.label)
  }

  render($where) {
    super.render($where)
    this.$elem.click(this.onClick.bind(this))
  }

  onClick(evt) {
    console.log("Button" + this.label + 'clicked!')
  }
}
```

> 除了语法更好看之外,ES6还解决了什么问题?
>
> 1. 不再引用杂乱的.prototype了
> 2. Button声明直接"继承"了Widget,不再需要通过Object.create(...)来替换`.prototype`对象,也不需要设置`._proto_`或者`Object.setPrototypeOf(...)`
> 3. 可以通过`super(...)`来实现相对多态,这样任何方法都可以引用原型链上层的同名方法. 构造函数不属于类,所以无法相互引用---`super()`可以完美解决构造函数的 问题
> 4. class字面语法不能声明属性.看起来这是一种限制,但是它会排除掉许多不好的情况,如果没有这种限制的话,原型链末端的"实例"可能会意外地获取其他地方的属性
> 5. 可以通过extends很自然地扩展对象类型,甚至是内置的对象类型,比如Array或RegExp

### 2.class陷阱

class基本上只是现有[[Prototype]]机制(委托)的一种语法糖

>  也就是说`class`并不会像传统面向类的语言一样在声明时静态复制所有行为.如果修改或者替换了父"类"中的一个方法,那么子"类"和所有实例都会受到影响,因为它们在定义时并没有进行复制,只是使用基于`[[Prototype]]`的实时委托

class语法无法定义类成员属性(只能定义方法)