#  你不知道的JavaScript(中)

## ① 类型和语法

### 一. 类型

JS有七种内置类型:`null`,`undefined`,`boolean`,`number`,`string`,`object`和`symbol`,可以使用`typeof`运算符来查看

变量没有类型,但它们持有的值有类型,类型定义了值的行为特征

很多开发人员将`undefined`和`undeclared`混为一谈,但在Js中它们是两码事.`undefined`是值的一种,而`undeclared`则表示变量还没有被声明过

遗憾的是,JS将它们混为一谈,在我们试图访问"undeclared"变量时这样报错:`ReferenceErroeL a is not defined`, 并且typeof对`undefined`和`undeclared`变量都返回"undefined"

然而,通过`typeof`的安全防范机制(阻止报错)来检查`undeclared`变量,有时是个不错的办法

### 二. 值

#### 2.1 数组

如果字符串键值能够被强制类型转换为十进制数字的话,它就会被当作数字的索引来处理

```js
var a = []
a['13'] = 42
a.length; // 14
```

#### 2.2 字符串

JS中字符串是不可变的,而数组是可变的

字符串不可变是指字符串的成员函数不会改变其原始值,而是创建并返回一个新的字符串. 而数组的成员函数都是在其原始值上进行操作的

许多数组函数用来处理字符串很方便. 虽然字符串没有这些函数,但可以通过"借用"数组的非变更方法来处理字符串

>  可惜我们无法"借用"数组的可变更成员函数,因为字符串是不可变的,`变通办法`是将字符串转换成数组待处理完再转换成字符串

#### 2.3 数字

`JavaScript`数字类型是基于IEEE754标准来实现的,该标准通常也被称为"浮点数",JS使用的是"双精度"格式(即64位二进制)

##### 0.1+0.2 === 0.3

```js
0.1+0.2 === 0.3 //false
```

简单的来说,二进制浮点数中的0.1和0.2并不是十分精准,它们相加的结果并非刚好等于0.3,而是一个比较接近的数字0.30000000000000004,所以条件判断的结果是false

##### 整数检测

要检测一个值是否是整数,可以使用ES6中的`Number.isInteger(...)`方法

```js
Number.isInteger(42) //true
Number.isInteger(42.000) //true
Number.isInteger(42.3) //false
```

#### 2.4 特殊的数值

`undefined`类型只有一个值,即是`undefined`

`null`类型也只有一个值,即是`null`

- `null`指空值
- `undefined`指没有值

或者

- `undefined`指从未赋值
- `null`指曾赋过值,但是目前没有值

`null`是一个特殊关键字,不是标识符,我们不能将其当作变量来使用和赋值. 然而`undefined`却是一个标识符,可以当作变量来使用和赋值

##### void运算符

表达式 `void __`没有返回值,因此返回结果是`undefined`,`void`并不改变表达式的结果,只是让表达式不返回值

```js
var a = 42
console.log(void a , a) // undefined 42
```

如果要将代码中的值设为`undefined`,就可以使用`viod`

##### 特殊的数字(NaN)

`NaN`是一个"警戒值",用于指出数字类型中错误情况,即"执行数学运算没有成功,这是失败后返回的结果"

##### NaN唯一一个非自反

`NaN`是一个特殊值,是唯一一个非自反(自反,即X===X不成立)

##### 特殊的等式

由于NaN和自身不相等,所以必须使用ES6的`Number.isNaN(...)`

ES6中新加入了一个工具Object.is(...)来判断两个值是否绝对相等

```js
var a = 2 / "foo"
var b = -3 * 0

Object.is(a, NaN) // true
Object.is(0, -0)  // true
Object.is(b, 0) // true
```

#### 2.5 值和引用

简单值(基本类型值)总是通过值的方式来赋值/传递,包括`null`,`undefined`,`字符串`,`数字`,`布尔值`和`ES6中的symbol`

复合值--对象(包括数组和封装对象)和函数,则总是通过引用复制的方式来赋值/传递

我们无法自行决定使用值复制还是引用复制,一切由值的类型来决定

#### 小结

`JS`中的数组是通过数字索引的一组任意类型的值. 字符串和数组类似,但它们的行为特征不同,在将字符作为数组来处理需要特别小心. `JS`中的数字包括"整数"和"浮点数"

基本类型中定义了几个特殊的值

`null`类型只有一个值`null`,`undefined`类型也只有一个值`undefined` . 所有变量在赋值之前默认值都是`undefined`. 

`void运算符`返回`undefined`

数字类型有几个特殊值,包括`NaN`(invalid number),`+Infinity`,`-Infinity`和 -0

简单标量基本类型值(字符串和数字等)通过值复制来赋值/传递,而复合值(对象等)通过值引用来赋值/传递. JS中的引用和其他语言的引用/指针不同,它们不能指向别的变量/引用,只能指向值

### 三. 原生函数

- String()
- Number()
- Boolean()
- Array()
- Object()
- Function()
- RegExp()
- Date()
- Error()
- Symbol()

#### 内部属性[[Class]] - 精准检查值类型

`Object.prototype.toString(...)`来查看一个复合值的类型 

```js
Object.prototype.toString.call([1,2,3]) // "[object Array]"
Object.prototype.toString.call(/regx-literal/i) // "[object RegExp]"
```

由于基本类型值没有`.length`和`.toString()`这样的属性和方法,需要通过封装对象才能访问,此时`JavaScript`会自动为基本类型值包装成一个封装对象

#### 原生函数作为构造函数

`Array`构造函数只带一个数字参数的时候,该参数会作为数组的预设长度,而非只充当数组中一个元素

#### Date(...)和Error(...)

Date(...)主要用来获取当前的Unix时间戳(从1970年1月1日开始计算),该值可以通过日期对象的`getTime()`来获得

Es5引入一个静态函数`Date.now()`来获取当前时间戳

所有的函数(包括内置函数`Number,Array等`)都可以调用Function.prototype中的`apply(...)`,`call(...)`和`bind(...)`

#### 小结

`JavaScript`为基本数据类型值提供了封装对象,称为原生函数(如`String`,`Number`,`Boolean`等)

它们为基本数据类型提供了该子类型所特有的方法和属性

对于简单标量基本类型值,比如`abc`,如果要访问它的`length`属性或`String.prototype`方法,JS引擎会自动对该值进行封装来实现对这些属性和方法的访问

### 四. 强制类型转换

`JS`中的强制类型转换总是返回标量基本类型值,如字符串,数字和布尔值,不会返回对象和函数

然而在JS中通常将它们统称为`强制类型转换`,分为"隐式强制类型转换"和"显式强制类型转换"

#### JSON字符串化

`undefined`,`function`和`symbol`和包含循环引用的对象都不符合JSON结构标准,其他支持JSON的语言无法处理它们

`JSON.stringify(..)`在对象中遇到`undefined`,`function`和`symbol`时会自动将其忽略,在数组中则会返回null(以保证单元位置不变)

```js
JSON.stringify(undefined) // undefined
JSON.stringify(function(){}) // undefined
JSON.stringify(
    [1,undefined,function(){},4]
)  // "[1,null,null,4]"
JSON.stringify({
    a:2,b:function(){}
}) // "{"a":2}"
```

##### 实用功能

如果replace是一个数组,那么它必须是一个字符串数组,其中包含序列化要处理的对象的属性名称,除此之外其他的属性则被忽略

```js
var a = {
    b:42,
    c:"42",
    d:[1,2,3]
}
JSON.stringify(a,["b","c"]) //"{"b":42,"c":"42"}
JSON.stringify(a,(k,v)=>{
    if(k !== "c") return v
})
// "{"b":42,"d":[1,2,3]}"
```

JSON.stringify还有一个可选参数space,用来指定输出的缩进格式. space为正整数时是指定每一级缩进的字符数,它还可以是字符串,此时最前面的十个字符串被用于每一级的缩进

`JSON.stringify(...)`并不是强制类型转换

1. 字符串,数字,布尔值和null的`JSON.stringify(...)`规则与`ToString`基本相同
2. 如果传给`JSON.stringify(...)`的对象定义了`toJSON()`方法,那么该方法会在字符串化前调用,以便转换成较为安全的JSON值 

#### 4.1 ToNumber

其中`true`转换为1,`false`转换为0.`undefined`转换为`NaN`,`null`转换为0

##### 将值转换为相应的基本类型值

首先检查该值是否有`valueOf()`方法. 如果有并且返回基本类型值,就使用该值进行强制类型转换. 如果没有就使用`toString()`的返回值来进行强制类型转换

如果`valueOf()` 和`toString()`均不返回基本类型值,会产生`TypeError`错误

从`ES5`开始,使用`Object.create(null)`创建的对象`[[Prototype]]`属性为`null`,并且没有`valueOf()`和`toString()`方法,因此无法进行强制类型转换

#### 4.2 ToBoolean

`JavaScript`中的值可以分为以下两类:

1. 可以被强制类型转换为false的值
2. 其他(被强制转换为true的值)

以下假值的布尔强制类型转换结果为false:

- undefined
- null
- false
- +0, -0 和 NaN
- ""

> 假值列表以外的都是真值

#### 4.3 显式强制类型转换

##### 字符串和数字之间的显式转换

`String(....)遵循前面讲过的`ToString规则,将值转换为字符串的基本类型. `Number(...)`遵循前面讲过的ToNumber规则,将值转换成数字的基本类型

> 一元运算 + 被普通认为是显式强制类型转换

##### 日期显示转换为数字

```js
var timestamp = +new Date()

// 不过最好还是使用ES5中新加入的静态方法Date.now()
var timestamp = Date.now()
```

##### ~运算符

~x大致等同于-(x+1)

~ 和 `indexOf( )`一起可以将结果强制类型转换

```js
var a = "Hello World"
~a.indexOf("lo") // -4 <--真值
if(~a.indexOf("lo")) {
    // 找到匹配
}
~a.indexOf("ol")  // 0 <-- 假值
```

> 由`-(x+1)`推断`~ -1`的结果应该是-0,然而实际上结果是0,因为它是字位操作而非树形运算

##### 显式解析数字字符串

```js
var a = "42"
var b = "42px"
Number(a) // 42
parseInt(a) // 42

Number(b) // NaN
parseInt(b) // 42
```

解析允许字符串中含有非数字字符,解析按从左到右的顺序,如果遇到非数字字符就停止. 而转换不允许出现非数字字符串,否则会失败并返回`NaN`

> 例外 : `parseInt(1/0, 19)` // 18
>
>  parseInt(1/0, 19)实际上是parseInt("Infinity", 19). 第一个字符是"I",以19为基数时值为18. 第二个字符"n"不是一个有效的数字字符,解析到此为止

##### 显示转换为布尔值

建议使用Boolean(a)和!!a 来进行显式强制类型转换

#### 4.4 隐式强制类型转换

隐式强制类型转换指的是那些隐蔽的强制类型转换

```js
var a = [1,2]
var b = [3,4]
a + b // "1,23,4"
```

> 因数组的`valueOf()`操作无法得到简单基本类型值,于是它转而调用`toString()`. 因此上例中的两个数组变成了"1,2"和"3,4". + 将它们拼接了

```js
var a = 42
var b = a + ""
b // "42
```

根据ToPrimitive抽象操作规则,`a + ""`会对a 调用`valueOf()`方法,然后通过`ToString`抽象操作将返回值转换为字符串. 而`String(a)`则是直接调用`ToString()`

##### 隐式强制类型转换为布尔值

下面的情况会发生布尔值隐式强制类型转换:

1. if(...)语句中的条件判断表达式
2. for(.. ; .. ; ..)语句中的条件判断表达式(第二个)
3. while(..)和do..while(..)循环中的条件判断表达式
4. ? : 中的条件判断表达式
5. 逻辑运算符 || (逻辑或) 和 && (逻辑与) 左边的操作数(作为条件判断表达式)

##### || 和 &&

&& 和 || 运算符的返回值并不一定是布尔类型,而是两个操作数其中一个的值

|| 和 &&首先会对第一个操作数执行条件判断,如果其不是布尔值就先进行ToBoolean强制类型转换,然后再执行条件判断

#### 4.5 宽松相等和严格相等

>  正确的解释是:" == "允许在相等比较中进行强制类型转换,而" === "不允许

* 用法: 

  如果两个值的类型不同,我们就需要考虑有没有强制类型转换的必要,有就用==,没有就用===,不用在乎性能

##### 抽象相等==

"=="在比较两个不同类型的值时会发生隐式强制类型转换,会将其中之一或者两者的转换为相同的类型后再进行比较

###### a. 字符串和数字之间的相等比较

```js
var a = 42
var b = "42"
a === b  // fasle
a == b  // true
```

> 1. 如果Type(x)是数字,Type(y)是字符串,则返回x==ToNumber(y)的结果
> 2. 如果Type(x)是字符串,Type(y)是数字,则返回ToNumber(x)==y 的结果

###### b. 其他类型和布尔类型之间的相等比较

```js
var a = '42'
var b = true
a == b //false
```

> 1. 如果Type(x)是布尔类型,则返回ToNumber(x) == y的结果
> 2. 如果Type(y)是布尔类型,则返回x == ToNumber(y)的结果

```js
var x = "42"
var y = false
x == y  // false
```

解析: Type(y)是布尔值,所以ToNumber(y)将false强制类型转换为0,然后`"42" == 0` 再变成`42 == 0`,结果是fasle

> 建议无论什么情况下不用使用 == true 和 == false

###### c.null和undefined之间的相等比较

> 1. 如果x为null,y为undefined,则结果为true
> 2. 如果x为undefined,y为null,则结果为true

在 == 中null和undefined相等,除此之外其他值都不存在这种情况

```js
var a = null
var b 
a == b // true
a == null // true
b == null // true

a == false // false
b == false // false
a == ""
b == ""
a == 0
b == 0
```

###### d. 对象和非对象之间相等比较

> 1. 如果Type(x)是字符串或者数字,Type(y)是对象,则返回x==ToPrimitive(y)的结果
> 2. 如果Type(x)是对象,Type(y)是字符串或者数字,则返回ToPrimitive(x)==y的结果

##### 比较少见的情况

见中卷P84

##### 使用建议

- 如果两边的值中有true或者false,千万不要使用"=="
- 如果两边的值中有[],""或者0,尽量不要使用"=="

#### 4.6 抽象比较

如果比较双方都是字符串,则按字母顺序来进行比较

```js
var a = ["42"]
var b = ["043"]
a < b // false
```

解析: ToPrimitive返回的是字符串,所以这里比较的是"42"和"043"两个字符串,它们分别以"4"和"0"开头.

```js
var a = {b:42} 
var b = {b:43}
```

解析: 因为a是[object object],b是[object object],所以按字母顺序进行比较

#### 小结

JS的数据类型之间的转换,即强制类型转换: 包括显式和隐式

显式强制类型转换明确告诉我们哪里发生了类型转换,有助于提高代码可读性和可维护性

隐式强制类型转换则没有那么明显,是其他操作的副作用

### 五. 语法

#### 5.1 语句和表达式

- 语句 : 语句相当于句子,完整表达某个意思的一组词
- 表达式: 表达式相对于短语,JS中表达式可以返回一个结果值

```js
var a,b
a = if(true) {
    b = 4 + 38
}
```

上面这段代码无法运行,因为语法不允许我们获得语句的结果值并将其赋值给另一个变量

ES7规范有一项"do表达式":

```js
var a,b
a = do {
	if(true) {
    	b = 4 + 38
	}
}
a // 42
```

其目的是将语句当作表达式来处理(语句中可以包含其他语句),从而不需要将语句封装为函数再调用return来返回值

##### 表达式的副作用

###### 一元运算符

++在前面时,如++a,它的副作用产生在表达式返回结果值之前,而a++的副作用则产生在之后

###### delete运算符

delete用来删除对象中的属性和数组中的单元

如果操作成功,delete返回true,否则返回false. 其副作用是属性被从对象中删除(或单元从array中删除)

###### 上下文规则

```js
if(a) {
    //...
}
else if(b) {
	//...        
}
else {
    //...
}
```

事实上JS没有`else if`,但`if`和`else`只包含单条语句的时候可以省略代码块的 { }

#### 5.2 运算符优先级

| 运算符                             | 说明                                                   |
| ---------------------------------- | ------------------------------------------------------ |
| .[ ] ( )                           | 字段访问、数组索引、函数调用和表达式分组               |
| ++ -- - ~ ! delete new typeof void | 一元运算符、返回数据类型、对象创建、未定义的值         |
| * / %                              | 相乘、相除、求余数                                     |
| + - +                              | 相加、相减、字符串串联                                 |
| << >> >>>                          | 移位                                                   |
| < <= > >= instanceof               | 小于、小于或等于、大于、大于或等于、是否为特定类的实例 |
| == != === !==                      | 相等、不相等、全等，不全等                             |
| &                                  | 按位“与”                                               |
| ^                                  | 按位“异或”                                             |
| \|                                 | 按位“或”                                               |
| &&                                 | 逻辑“与”                                               |
| \|\|                               | 逻辑“或”                                               |
| ?:                                 | 条件运算                                               |
| = *OP*=                            | 赋值、赋值运算（如 += 和 &=）                          |
| ,                                  | 多个计算                                               |

#### 5.5 函数参数

```js
function foo(a = 42,b = a + 1) {
    console.log(
    	arguments.length, a , b,
        arguments[0], arguments[1]
    )
}
foo()    // 0 42 43 undefined undefined
foo(10)  // 1 10 11 10 undefined
foo(10,undefined )  // 2 10 11 10 undefined
foo(10,null )  // 2 10 11 10 undefined
```

虽然参数a和b都有默认值,但是函数不带参数时, arguments数组为空

相反,如果向函数传递undefined值,则arguments数组中会出现一个值为undefined的单元,而不是默认值

#### 5.6 try..finally

```js
function foo() {
    try {
        return 42
    }
    finally {
        console.log("hello")
    }
    console.log("never runs")
}
console.log(foo())
// hello
// 42
```

这里return 42先执行,并将foo()函数的返回值设置为42. 然后try执行完毕,接着执行finally. 最后foo()函数执行完毕.

```js
function foo() {
    try {
        throw 42
    }
    finally {
        console.log("hello")
    }
    console.log("never runs")
}
console.log(foo())
// hello
// Uncaught Exception: 42
```

如果finally中抛出异常,函数就会在此终止. 如果此前try中已经有return设置了返回值,则该值会被丢弃

#### 小结

语句和表达式在英语中都能找到类比---语句就像英文中的句子,而表达式就像短语. 表达式可以是简单独立的,否则可能会产生副作用

JS在语法规则上是语义规则. 例如, { } 在不同情况下的意思不尽相同, 可以是语句块,对象常量,解构赋值(ES6)或者命名函数参数 (ES6)

ASI(自动分号插入)是JS引擎的代码解析纠错机制,它会在需要的地方自动插入分号来纠正解析错误. 问题在于这是否意味着大多数的分号都不是必要的,或者由于分号缺失导致的错误是否都可以交给JS引擎来处理

### 混合环境

`JavaScript`程序几乎总是在宿主环境中运行

>  在创建带有id属性的DOM元素时也会创建同名的全局变量

---

## ② 异步和性能

### 一. 异步: 现在与将来

#### 1.1 异步控制台

并没有什么规范或一组需求指定console.*方法族如何工作--它们并不是`JavaScript`的一部分,而是由`宿主环境`添加到`JavaScript`中的

在某些条件下,某些浏览器的`console.log(....)`并不会把传入的内容立即输出,在许多程序中,`I/O`是非常低速的阻塞部分

如果在调试的过程中遇到对象在`console.log(....)`语句之后被修改,可你却看到了意料之外的结果,要意识到这可能是这种`I/O`的异步化造成的

#### 1.2 事件循环

程序通常分成很多小块,在事件循环队列中一个接一个地执行. 严格地说,和你的程序不直接相关的其他事件也可能会插入到队列中

#### 1.3 并行线程

异步是关于现在和未来的时间限制,而并行是关于能够同时发生的事情

多线程编程是非常复杂的. 因为如果不通过特殊的步骤来防止这种中断和交错运行的话,可能会得到出乎意料的,不确定的行为.

> `JavaScript`从不跨线程共享数据,这一味着不需要考虑这一层次的不确定性. 但是这并不意味着`JavaScript`总是确定性的

```js
`示例代码`
var a = 20
function foo() {
    a = a + 1 
}
function bar() {
    a = a * 2
}
// ajax异步请求的回调
ajax('/get',foo)
ajax('/get2',bar)
```

在JS的特性中,这种函数顺序的不确定性就是通常所说的`竞态条件`,`foo()`和`bar()`相互竞争,看谁先运行.

完整性,由于JS的单线程特性,`foo()`(`以及bar()`)中的代码具有原子性. 一旦`foo()`开始进行,它的所有代码都会在`bar()`中的任意代码进行之前完成,或者相反,这称为`完整运行`特性

#### 1.4 并发

##### `setTimeout(..0)`

基本的意思是: 把这个函数插入到当前事件循环队列的结尾处

严格来说,`setTimeout(..0)`并不直接把项目插入到事件循环队列. 定时器会在有机会的时候插入事件. 两个连续的`setTimeout(..0)`调用不能保证会按照调用顺序处理

....

#### 小结

实际上,JavaScript程序总是至少分为两个块: 第一个块现在运行;下一个块将来运行,以响应某个事件. 尽管程序是一块一块执行的. 但是所有这些块共享对程序作用域和状态的访问,所以对状态的修改都是在之前累积的修改之上进行的. 

一旦有事件需要运行,事件循环就会运行,直到队列清空. 事件循环的每一轮称为一个tick. 用户交互,IO和定时器会向事件队列中加入事件

任何时刻,一次只能从队列中处理一个事件. 执行事件的时候,可能直接或间接地引发一个或多个后续事件

并发是指两个或多个事件链随时间发展交替执行,以至于从更高的层次来看,就像是同时在运行(尽管在任意时刻只处理一个事件)

通常需要对这些并发执行的"进程"进行某种形式的交互协调,比如需要确保执行或者需要防止竞态出现. 这些"进程"也可以通过把自身分割为更小的块,以便其他"进程"插入进来.

### 二. 回调

回调是编写和处理JS程序异步逻辑的最常用方式

嵌套回调常常称为`回调地狱`,有时也称为毁灭金字塔

#### 2.3 回调的信任问题

- 调用回调过早(在追踪之前)
- 调用回调过晚(或者没有调用)
- 调用回调的次数太多或太少
- 没有把所需的环境/参数成功传給你的回调函数
- 吞掉可能出现的错误或异常

#### 2.5 总结

回调函数是JS异步的基本单元

第一,大脑对于事件的计划方式是线性的,阻塞的,单线程的语义,但是回调表达异步流程的方式是非线性的,非顺序的,这使得正确推导这样的代码难度很大. 难于理解的代码是坏代码,会导致坏bug

我们需要一种更同步,更顺序,更阻塞的方式来表达异步,就像我们的大脑一样

第二,也是更重要的一点,回调会受到控制反转的影响,因为回调暗中把控制权交给第三方(通常是不受你控制的第三方工具!)来调用你代码中的`continuation`. 这种控制转移导致一系列麻烦的信任问题,比如回调被调用的次数是否会超出预期

可以发明一些特定逻辑来解决这些信任问题,但是其难度高于应有水平,可能会产生更笨重,更难维护的代码,并且缺少足够的保护,其中的损害要直到你受到bug的影响才会被发现

我们需要一个通用的方案来解决这些信任问题. 不管我们创建多少回调,这一方案都应可以复用,且没有重复代码的开销

我们需要比回调更好的机制. 到目前为止,回调提供了很好的服务,但是未来的JS需要更高级,功能更强大的异步模式

### 三. Promise

通过回调表达程序异步和管理并发的两个主要缺陷: 缺乏顺序性和可信任性

一旦`Promise`决议,它就永远保持在这个状态.此时它就成为了不变值(immutablevalue),可以根据需求多次查看

`Promise`决议后就是外部不可变的值,我们可以安全地把这个值传递给第三方,并确信它不会被有意无意地修改.

#### 3.1 Promise"事件"

代码: 

```js
function foo(x) {
    // 开始做一些可能耗时的工作
    
    //构造并返回一个promise
    return new Promise((resolve,reject)=>{
        // 最终调用resolve(...)或者reject(...)
    })
}
```

这些是promise的决议函数. `resolve(...)`通常标识完成,而`reject(...)`则标识拒绝

#### 3.2 具有then方法的鸭子类型

识别Promise就是定义某种称为thenable的东西,将其定义为任何具有`then(...)`方法的对象和函数

对`thenable`值的鸭子类型检测就大致类似于:

```js
if(
	p !== null &&
    (
    	typeof p === 'object' ||
        typeof p === 'function'
    )&&
    typeof p.then === 'function'
){
    // 假定这是一个thenable
}else{
	// 不是thenable
}
```

如果有任何其他代码无意或者恶意地给`Object.prototype`及`Array.prototype`或者其他原生原型添加`then(..)`,

你无法控制也无法预测,并且,如果指定的是不调用起参数作为回调的函数,那么如果有Promise决议到这样的值,就会永远挂住!

#### 3.3 Promise的信任问题

##### 回调的信任问题

- 调用回调过早(在追踪之前)
- 调用回调过晚(或者没有调用)
- 调用回调的次数太多或太少
- 没有把所需的环境/参数成功传給你的回调函数
- 吞掉可能出现的错误或异常

##### 3.3.1 调用过早

在这类问题中,一个任务有时同步完成,有时异步完成,这可能会导致竞态条件

对一个`Promise`调用`then(..)`的时候,因为即使这个`Promise`已经决议,提供给`then(..)`的回调也总会被异步调用

##### 3.3.2 调用过晚

`Promise`的`then(..)`注册的观察就会被自动调度. 可以确信,这些被调度的回调在下一个异步事件点上一定会被触发. 也就是说, 一个`Promise`决议后,这个`Promise`上所有的通过`then(...)`注册的回调都会在下一个异步时机点上 依次被立刻调用.

```js
p.then(function() {
    p.then(function() {
        console.log("C")
    })
    console.log("A")
})
p.then(function() {
        console.log("B")
})
// A B C
// 这里,"C"无法打断或抢占"B",这就是Promise的运作方式
```

##### 3.3.3 回调未调用

没有任何东西能阻止`Promise`像你通知它的决议. 如果你对一个`Promise`注册了一个完成回调和一个拒绝回调,那么`Promise`在决议时总会调用其中的一个

##### 3.3.4 调用次数过少或过多

由于`Promise`只能被决议一次,所以任何通过`then(..)`注册的回调就只会调用一次

当然,如果你把同一个回调注册了不止一次(如: p.then(...); p.then(...)),那它被调用的次数就会和注册次数相同. 响应函数只会被调用一次. 

##### 3.3.5 未能传递参数/环境值

`Promise`至多只能有一个决议值(完成或拒绝)

如果使用多个参数调用`resolve(..)`或者`reject(..)`,第一个参数之后的所以参数都会被忽略.

> JS中的函数总是保持其定义所在的作用域的闭包

##### 3.3.6 吞掉错误或异常(重要)

如果`Promise`的创建过程中或在查看其决议结果过程中的任何时间点上出现一个JS异常错误,比如一个`TypeError`或`ReferenceError`,那这个异常就会被捕捉,并且会使这个`Promise`被拒绝

```js
var p = new Promise(function(resolve,reject){
    foo.bar() // foo未定义,会报错
    resolve(42) // 永远不会到这里
})
p.then(\
    function fulfilled() {
        // 永远不会到达这里
    },
    function rejected(err) {
        // err将会是一个TypeError异常对象来自foo.bar()这一行
    }
)
```

因为其有效解决了另外一个潜在的Zalgo风险,即出错可能会引起同步响应,而不出错则会是异步的. `Promise`甚至把JS异常也变成了异步行为,进而极大降低了竞态条件出现的可能.

```js
var p = new Promise(function(resolve,reject){
    resolve(42)
})
p.then(
	 function fulfilled(msg) {
        foo.bar()
        console.log(msg) // 永远不会到达这里
    },
    function rejected(err) {
        // 永远不会到达这里
    }
)
```

这看起来`foo.bar()`这一行产生的异常被吞掉了,实际上不是这样的,实际上是我们没有侦听到它

`p.then(..)`调用本身返回了另一个`Promise`,正是这个`Promise`将会因这个`TypeError`异常而拒绝



问: 为什么它不是简单地调用我们定义的错误处理函数?

答: 如果这样的话就违背了Promise的一条基本原则, `Promise`一旦决议就不可再变. p已经完成为值42,所以之后查看p的决议是,并不能因为出错就把p再变成一个拒绝

##### 3.3.7 是可信任的promise吗

如果向`Promise.resolve(..)`传递一个非`Promise`或非`thenable`的立即值,就会得到一个用这个值填充的promise. 

```js
var p1 = new Promise(function(resolve,reject){
    resolve(42)
})
var p2 = Promise.resolve(42)
// 以上两个promise的行为完全是一致的
```

而如果向`Promise.resolve(...)`传递一个真正的Promise,就会返回一个Promise

`Promise.resolve(...)`可以接受任何`thenable`,将其解封为它的非`thenable`值. 从`Promise.resolve(...)`得到的是一个真正的`Promise`,是一个可以信任的值. 如果你传入的已经是真正的`Promise`,那么你得到的就是本身,所以通过`Promise.resolve(...)`过滤来获得可信任性完全没有坏处.

> 对于用`Promise.resolve(...)`为所有函数的返回值都封装一层. 另一个好处是,这样做容易把函数调用规范为定义良好的异步任务. 如果一个函数有时会返回一个立即值,有时会返回`Promise`,那么`Promise.resolve(...)`就能保证总会返回一个`Promose`结果

##### 3.3.8 建立信任

`Promise`这种模式通过可信任的语义把回调作为参数传递,使得这种行为更可靠更合理.通过把回调的控制反转反转回来,我们把控制权放到一个可信任的系统(Promise)中,这种系统的设计目的就是为了使异步编码更清晰

#### 3.4 链式流

链式流得于实现关键在于以下两个`Promise`固有行为特征:

1. 每次对promise调用then(..),它都会创建并返回一个新的Promise,我们可以将其链接起来
2. 不管从then(..)调用的完成回调返回值是什么,它都会自动设置为被链接Promise(第一点中的)的完成

```js
function delay(time) {
    return new Promise(function (resolve, reject) {
        setTimeout(resolve, time)
    })
}

delay(100) // 步骤1
    .then(function STEP2() {
        console.log('step2 after 100ms')
        return delay(200)
    })
    .then(function STEP3() {
        console.log('step3 after another 200ms')
    })
    .then(function STEP4() {
        console.log('step4')
        return delay(50)
    })
    .then(function STEP5() {
        console.log('step5 after another 50ms')
    })
```



> 	严格来说: 这个交互过程中有两个promise: 200ms延迟promise和第二个then(..)链接到的那个链接promise.
>
> `Promise`机制已经自动把它们的状态合并在一起,可以把`return delay(200)`看作是创建了一个`promise`,并用其替换了前面返回的链接`promise`

从本质来说,这使得错误可以继续沿着`Promise`链传播下去,直到遇到显示定义的拒绝处理函数

##### 总结

* 调用`Promise`的`then(..)`会自动创建一个新的`Promise`从调用返回
* 在完成或拒绝处理函数的内部,如果返回一个值或抛出一个异常,新返回的Promise就相应地决议
* 如果完成或拒绝处理函数返回一个Promise,它将会被展开,这样一来,不管它的决议值是什么,都会成为当前then(..)返回的链接Promise的决议值

##### 决议,完成以及拒绝

决议(`resolve`),完成(`fulfill`)和拒绝(`reject`)

#### 3.5  错误处理

错误处理最自然的形式就是同步的try...catch结构

任何Promise链的最后一步,不管是什么,总是存在着未被查看的Promise中出现未捕获错误的可能性

##### 3.5.2 处理未捕获的情况(未实现)

浏览器有一个特有的功能:

它们可以跟踪并了解所有对象被丢弃以及被垃圾回收的时机. 所以,浏览器可以追踪Promise对象. 如果在它被垃圾回收的时候其中拒绝,浏览器就能确保这是一个真正未被捕获的错误,进而可以确定应该将其报告到开发者终端.

##### 3.5.3 成功的坑

* 默认情况下,Promise在下一个任务或时间循环tick上报告所有拒绝么如果在这个时间点上该Promise上还没有注册错误处理函数
* 如果想要一个被拒绝的Promise在查看之前的某个时间段被保持被拒绝状态,可以调用`defer(..)`,这个函数优先级高于该Promise的自动错误报告

```js
var p = Promise.reject('Oops').defer()
foo(42)
.then(
    function fulfilled(){
        return p
    },
    function rejected(err) {
        // 处理foo(...)错误
    }
)
// 调用defer(),这样就不会有全局报告出现. 为了便于链接,defer()只是返回这同一个promise
```

默认情况下,所有的错误要么被处理要么被报告,调用`defer()`的危险是,如果`defer()`了一个Promise,但之后没有成功查看或处理它的拒绝结果,这样就有可能存在未被捕获的情况

#### 3.6 Promise模式

##### 3.6.1  Promise.all([..])

`Promise.all([...])`需要一个参数,是一个数组,通常由Promise实例组成. 从`Promise.all([..])`调用返回的promise会收到一个完成消息. 这是一个由所有传入promise的完成消息组成的数组,与指定的顺序一致(与完成顺序无关)

- 严格说来,传给`Promise.all([..])`的数组中的值可以是`Promise`, `thenable`,甚至是立即值.

- 就本质而言,列表中的每个值都会通过`Promise.resolve(..)`过滤,以确保要等待的是一个真正的Promise,所以立即值会规范化为为这个值构建的Promise.
- 如果数组是空的,主Promise就会立即完成

> 1. 从`Promise.all([..])`返回的主promise在且仅在所有成员promise都完成后才会完成.
> 2. 如果这些promise中有任何一个被拒绝的话,主`Promise.all([..])`promise就会立即被拒绝,并抛弃来自其他所有promise的全部结果
> 3. 永远要记住为每个promise关联一个拒绝/错误处理函数,特别是从`Promise.all([..])`返回的那一个

##### 3.6.2 Promise.race([..])

`Promise.race([..])`也接受单个数组参数. 这个数组由一个或多个Promise,thenable或 立即值组成. 但是立即值之间的竞争在实践中没有太大的意义

- 与`Promise.all([..])`类似,一旦有任何一个Promise决议为完成,`Promise.race([..])`就会完成;一旦有任何一个Promise决议为拒绝,它就会拒绝
- 如果你传入一个空数组,主`race([..])`Promise永远不会决议,而不是立即决议

#### 3.7 Promise API概述

##### 3.7.1 new Promise(..)构造器

有启示的构造器Promise(..)必须和new一起使用,并且必须提供一个函数回调. 这个回调是同步的或立即调用的

```js
var p = new Promise(function(resolve,reject){
    // resolve(..) 用于决议/完成这个promise
    // reject(..) 用于拒绝这个promise
})
```

> 1. `reject(..)`就是拒绝这个promise;但`resolve(..)`即可能完成promise,也可能拒绝,要根据传入参数而定.
> 2. 如果传给`resolve(..)`的是一个非Promise,非thenable的立即值,这个promise就会用这个值完成
> 3. 如果传给resolve(..)的是一个真正的promise或thenable值,这个值就会被递归展开,并且promise将取用其最终决议值或状态

##### 3.7.2 `Promise.resolve(...)`和`Promise.reject(...)`

`Promise.resolve(...)`创建一个**已完成**的Promise的快捷方式

`Promise.reject(...)`创建一个**已被拒绝**的Promise的快捷方式

```js
var p1 = new Promise(function(resolve,reject){
    reject('Oops')
})
var p2 = Promise.reject("Oops")
// 以上两个promise是等价的
```

##### 3.7.3 `then(...)`和`catch(...)`

1. then(..)接受一个或者两个参数;第一个用于完成回调,第二个用于拒绝回调. 如果两者中的任何一个被省略或者作为非函数值传入的话,就会替换为相应的默认回调. 默认完成回调只是把消息传递下去,而默认拒绝回调则只是重新抛出其接收到的出错原因. 
2. catch(..)只接受一个拒绝回调作为参数,并自动替换默认完成回调. 它等价于then(null,...)

```js
p.then(fulfilled)
p.then(fulfilled,rejected)
p.catch(rejected); // 等价于.then(null,rejected)
```

then(..)和catch(..)也会创建并返回一个新的promise,这个promise可以用于实现Promise链式流程控制

##### 3.7.4 `Promise.all(...)`和`Promise.race(...)`

详情见3.6

向`Promise.all([..])`传入空数组,它会立即完成,但`Promise.race([..])`会挂住,且永远不会决议

#### 3.8 Promise局限性

##### 3.8.1 顺序错误处理

由于一个Promise链仅仅是链接到一起成员Promise,没有把整个链标识为一个个个体的实体,这意味着没有外部方法可以用于观察可能发生的错误

我们可以在Promise链中注册一个拒绝错误处理函数,对于链中任何位置出现的任何错误,这个处理函数都会得到通知:`p.catch(handleErrors)`;

但是,如果链中的任何一个步骤事实上进行了自身的错误处理,那么`handleErrors(..)`就不会得到通知. 完全不能得到错误通知也是一个缺陷. 基本上,这等同于try..catch存在的局限: 

try...catch可能捕获一个异常并简单地吞掉它. 所以这不是Promise独有的局限性,但可能是我们希望绕过的陷阱

##### 3.8.2 单一值

Promise只能有一个完成值或一个拒绝理由.

##### 3.8.3 单决议

Promise最本质的特征是: 

一个promise只能被决议一次(无论完成还是拒绝)

##### 3.8.4 惯性

##### 3.8.5 无法取消的Promise

一旦创建了一个Promise并为其注册了完成和拒绝处理函数,如果出现某种情况使得这个任务挂起的话,你也没有办法从外部停止它的进程

##### 3.8.6 Promise的性能

Promise使所有一切都成为异步的了,即有一些立即完成的步骤仍然会延迟到任务的下一步. 这意味着一个Promise任务序列可能比完全通过回调连接的同样的任务序列运行的稍慢一点.

Promise稍慢一些,但作为交换,你得到的是大量内建的可信任,对Zalgo的避免及可组合性.

请使用它!

### 四. ES6生成器(generator)

生成器是一类特殊的函数,可以一次或多次启动和停止,并不一定非得要完成

```js
var x = 1
function *foo() {
    x++
    yield
    console.log('x':x)
}
function bar() {
    x++
}

// 构造一个迭代器it来控制这个生成器
var it = foo()
// 这里启动foo()
it.next()
x // 2
bar()
x // 3
it.next() // x:3
```

 解析: 

1. `it = foo()`运算并没有执行生成器*foo(),而只是构造一个迭代器,这个迭代器会控制它的执行
2. 第一个`it.next()`启动了生成器 `*foo() `,而并运行了 `*foo()`第一行的x++
3. *foo() 在yield语句处暂停,在这一点上第一个`it.next()`调用结束. 此时 *foo()仍然在运行并且是活跃的,但处于暂停状态
4. 我们查看x的值,此时是2
5. 我们调用bar(),它通过x++再次递增x
6. 我们再次查看x的值是3
7. 最后的`it.next()`调用从暂停处恢复了生成器`*foo()`的执行,并运行`console.log(..)`语句,这句语句使用当前x的值是3

#### 4.1 打破完整运行

##### 4.1.1 输入和输出

```js
function *foo(x,y) {
    return x * y
}
var it = foo(6,7)
var res = it.next()
res.valur // 42
```

我们只是创建一个迭代器对象,把它赋给一个变量it,用于控制生成器`*foo(..)`. 然后调用`it.next()`,指示生成器 `*foo(..)`从当前位置开始继续运行,停在下一个yield处或者直到生成器结束

这个`next(..)`调用的结果是一个对象,它有一个value属性,持有`*foo(..)`返回的值. 换句话说,yield会导致生成器在执行过程中发送出一个值,这有点类似于中间的`return`

> 根据你的视角不同,`yield`和`next(...)`调用有一个不匹配. 一般来说,需要的`next(..)`调用要比yield语句多一个
>
> 因为第一个next(..)总是启动一个生成器,并运行到第一个yield处. 不过,是第二个next(..)调用完第一个被暂停的`yield`表达式,第三个`next(..)`调用完成第二个yield,以此类推

消息是双向传递的---yield...作为一个表达式可以发出消息响应next(..)调用,next(..)也可以向暂停的yield表达式发送值

代码:

```js
function *foo(x) {
    var y = x * (yield "hello") // yield一个值
    return y
}
var it = foo(6)
var res = it.next() // 第一个next(),并不传入任何东西
res.value; 			// "hello"
res = it.next(7) 	// 	向等待的yield传入7
res.value			// 42
```

yield.. 和 next(..)这一对组合起来,在生成器中的执行过程中构成一个双向消息传递的系统

**注意**

```js
var res = it.next()	// 第一个next(),并不会传入任何东西
res.value; 			// "hello"
res = it.next(7) 	// 	向等待的yield传入7
res.value			// 42 
```

> ​	我们并没有向第一个next()调用和发送值,这是有意为之. 只有暂停的yield才能接受一个通过next(..)传递的值,而在生成器的起始处我们调用第一个next()时,还没有暂停的yield来接受这样一个值. 规范和所有兼容浏览器都会默默丢弃传递第一个next()的任何东西. 传值过去仍然不是个好思路,因为你创建了沉默无效代码,这会让人迷惑. 因此,启动生成器时一定要用不带参数的next()

如果你的生成器中没有return的话---在生成器中和普通函数中一样,return当然不是必需的---总有一个假定的/隐式的return(也就是return undefined),它会在默认情况下回答最后的it.next(7)提出的问题.

##### 4.1.2 多个迭代器

每次构建一个迭代器,实际上就隐式构建了生成器的一个实例,通过这个迭代器来控制的是这个生成器实例.

同一个生成器的多个实例可以同时运行,他们甚至可以彼此交互:

```js
function *foo() {
    var x = yield 2
    z++
    var y = yield(x * z)
    console.log(x,y,z)
}
var z = 1
var it1 = foo()
var it2 = foo()
var val1 = it.next().value	// 2 <--yield 2 
var val2 = it.next().value	// 2 <--yield 2
var1 = it1.next(val2 * 10 ).value	//40 <-- x:20 , z:2
var2 = it2.next(val1 * 10 ).value	//600 <-- x:200 , z:3

it1.next(val2/2) // y:300
				// 20 300 3
it2.next(val1/4) // y:10
				//200 10 3
```

> 执行流程:
>
> ​	(1) *foo()的两个实例同时启动,两个next()分别从`yield2`语句得到值2
>
> ​	(2) val2  * 10也就是2 * 10,发送到第一个生成器实例it1,因此x得到的值20. z从1增加到2,然后20 * 2通过yield发出,将val1设置40
>
> ​	(3) val1*5也就是40 * 5,发送到第二个生成器实例it2,因此x得到值200. z再次从2递增到3,然后200*3通过yield发出,将val2设置为600
>
> ​	(4) val2/2也就是600/2,发送到第一个生成器实例it1,因此y得到值300,然后打印出x y z的值分别是20 300 3
>
> ​	(5) val1/4也就是40/4,发送到第二个生成器实例it2,因此y得到值10,然后打印出x y z的值分别为200 10 3

#### 4.3 同步错误处理

`yield`暂停也使得生成器能够捕获错误

```js
function *main(){
  var x = yield "hello world"
  yield x.toLowerCase() // 引发一个异常
}
var it = main()
it.next().value // hello world
try{
  it.next(42)
}catch(err) {
  console.error(err) // TypeError
}
```

#### 4.4 生成器+Promise

ES6中最完美的世界就是生成器和Promise的结合

##### 迭代器应该对这个promise做什么呢?

它应该侦听这个promise的决议,然后要么使用完成消息恢复生成器运行,要么向生成器抛出一个带有拒绝原因的错误.

获取Promise和生成器最大效用的最自然的方法就是yield出来一个Promise,然后通过这个Promise来控制生成器的迭代器.

代码示例:

```js
function foo(x,y) {
  return request("http://some.url.1/?x="+ x + "&y="+ y)
}
function *main() {
  try{
    var text = yield foo(11,31)
    console.log(text)
  }catch(e) {
    console.log(e)
  }
}
```

在生成器内部,不管什么值yield出来,都只是一个透明的实现细节,所以我们甚至没有意识到其发生,也不需要关心,接下来实现接收和连接yield出来的promise,使它能够在决议之后恢复生成器.先从手工实现开始:

```js
var it = main();
var p = it.next().value;
// 等待promise p决议
p.then(function(text){
  it.next(text)
},function(err){
  it.throw(err)
})
```

##### async...await

生成器+promise的语法糖

如果,你await了一个Promise,async函数就会自动获知要做什么,它会暂停这个函数,直到Promise决议

##### 生成器中的Promise并发

最简单的方法:

```js
function *foo() {
  // 让两个请求"并行"
  var p1 = request("http://some.url.1")
  var p2 = request("http://some.url.2")
  
  // 等待两个Promise都决议
  var r1 = yield p1
  var r2 = yield p2
  
  var v3 = yield request(
  	"http://some.url.3/?v="+ r1 + "," + r2
  )
  console.log(r3)
}
// 工具函数run
run(foo)
```

p1和p2都会并发执行,无论完成顺序如何,两者都要全部完成,然后才会发出`r3 = yield request...Ajax请求`

当然,我们也可以使用Promise.all([...])完成

```js
function *foo() {
  // 让两个请求"并行"
  var results = yield Promise.all([
    request("http://some.url.1"),
    request("http://some.url.2")
  ])
  
  // 等待两个Promise都决议
  var r1 = results[0]
  var r2 = results[1]
  
  var v3 = yield request(
  	"http://some.url.3/?v="+ r1 + "," + r2
  )
  console.log(r3)
}
// 工具函数run
run(foo)
```

.....华丽的略过线.....

##### 小结

​	生成器是ES6的一个新的函数类型,它并不像普通函数那样总是运行到结束. 取而代之的是,生成器可以运行当中暂停,并且将来再从暂停的地方恢复运行.

​	这种交替的暂停和恢复是合作性的而不是抢战式的,这意味着生成器具有独一无二的能力来暂停自身,这是通过关键字yield实现的. 不过,只有控制生成器的迭代器具有恢复生成器的能力(通过next(..))

​	yield/next(..)这一对不只是一种控制机制,实际上也是一种双向消息机制. `yield..`表达式本质上是暂停下来等待某个值,接下来的next(...)调用会向被暂停的yield表达式传回一个值(或者是隐式的undefined)

​	在异步控制流程方面,生成器的关键优点是:

​		生成器内部的代码是自然的同步/顺序方式表达任务的一系列步骤. 其技巧在于,我们把可能的异步隐藏在了关键字yield的后面,把异步移动到控制生成器的迭代器的代码部分.

换句话说,生成器为异步代码保持了顺序,同步,阻塞的代码模式,这使大脑可以更自然地追踪代码,解决了基于回调的异步的两个关键字缺陷之一.

### 五. 程序性能

### 六.性能测试与调优
