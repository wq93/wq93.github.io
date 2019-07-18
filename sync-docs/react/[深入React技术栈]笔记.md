## *第1章 初入React世界

### 1.1 React简介

#### Virtual DOM 

​	React 把真实 DOM 树转换成 JavaScript 对象树，也就是 Virtual DOM。 

![image.png](https://upload-images.jianshu.io/upload_images/6541235-2926573a7ffe2020.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

​	每次数据更新后，重新计算 Virtual DOM，并和上一次生成的 Virtual DOM 做对比，对发生变化的部分做批量更新。 React 也提供了直观的 shouldComponentUpdate 生命周期回调，来减少数据变化后不必要的 Virtual DOM 对比过程，以保证性能。 

​	它最大的好处其实还在于方便和其他平台集成，比如 react-native 是基于 Virtual DOM 渲染出原生控件，因为 React 组件可以映射为对应的原生控件。在输出的时候，是输出 Web DOM，还是 Android 控件，还是 iOS 控件，就由平台本身决定了。因此， react-native 有一个口号——LearnOnce， Write Anywhere。

#### 函数式编程 

​	React 能充分利用很多函数式方法去减少冗余代码。此外，由于它本身就是简单函数，所以易于测试。可以说，函数式编程才是 React 的精髓。 

### 1.2 JSX

​	JSX 将 HTML 语法直接加入到 JavaScript 代码中，再通过翻译器转换到纯JavaScript 后由浏览器执行。 

### 1.3 React 组件 

​	React 组件基本上由 3 个部分组成——属性（props）、状态（state）以及生命周期方法。 

![image.png](https://upload-images.jianshu.io/upload_images/6541235-4b673de353285c07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

​	React 组件可以接收参数，也可能有自身状态。一旦接收到的参数或自身状态有所改变，React 组件就会执行相应的生命周期方法，最后渲染。整个过程完全符合传统组件所定义的组件职责。 

#### 构建方法

官方在 React 组件构建上提供了 3 种不同的方法： 

1. React.createClass(已废弃)
2.  ES6 classes 
3. 无状态函数（stateless function）。 

​	React 的所有组件都继承自顶层类 React.Component。它的定义非常简洁，只是初始化了React.Component 方法，声明了 props、 context、 refs 等，并在原型上定义了 setState 和forceUpdate 方法。内部初始化的生命周期方法与 createClass 方式使用的是同一个方法创建的。 

#### 无状态组件

使用无状态函数构建的组件称为无状态组件 

```jsx
function Button({ color = 'blue', text = 'Confirm' }) {
	return (
		<button className={`btn btn-${color}`}>
			<em>{text}</em>
		</button>
	);
}
```

​	无状态组件只传入 props 和 context 两个参数；也就是说，它不存在 state，也没有生命周期方法，组件本身即上面两种 React 组件构建方法中的 render 方法。 

### 1.4 React 数据流 

#### 单向数据流

​	在 React 中，数据是自顶向下单向流动的，即从父组件到子组件。这条原则让组件之间的关系变得简单且可预测。 

​	state 与 props 是 React 组件中最重要的概念。如果顶层组件初始化 props，那么 React 会向下遍历整棵组件树，重新尝试渲染所有相关的子组件。而 state 只关心每个组件自己内部的状态，这些状态只能在组件内改变。 

#### State

​	值得注意的是， setState **(可能)**是一个异步方法，一个生命周期内所有的 setState 方法会合并操作。 

#### Props

​	props 是 React 用来让组件之间互相联系的一种机制，通俗地说就像方法的参数一样。 

​	props 的传递过程，对于 React 组件来说是非常直观的。 React 的单向数据流，主要的流动管道就是 props。  

​	React 为 props 同样提供了默认配置，通过 defaultProps 静态变量的方式来定义。当组件被调用的时候，默认值保证渲染后始终有值。 

```jsx
static defaultProps = {
	classPrefix: 'tabs',
	onChange: () => {},
};
```

##### propTypes 

​	propTypes 用于规范 props 的类型与必需的状态。如果组件定义了propTypes，那么在开发环境下，就会对组件的 props 值的类型作检查，如果传入的 props 不能与之匹配， React 将实时在控制台里报 warning。在生产环境下，这是不会进行检查的。 

代码举例:

```jsx
static propTypes = {
  classPrefix: React.PropTypes.string,
  className: React.PropTypes.string,
  defaultActiveIndex: React.PropTypes.number,
  activeIndex: React.PropTypes.number,
  onChange: React.PropTypes.func,
  children: React.PropTypes.oneOfType([
    React.PropTypes.arrayOf(React.PropTypes.node),
    React.PropTypes.node,
  ]),
};
```

### 1.5 React 生命周期 

​	React 组件的生命周期根据广义定义描述，可以分为挂载、渲染和卸载这几个阶段。当渲染后的组件需要更新时，我们会重新去渲染组件，直至卸载。 	

##### 组件的挂载 

​	组件挂载是最基本的过程，这个过程主要做组件状态的初始化。我们推荐以下面的例子为模板写初始化组件： 

```jsx
import React, { Component, PropTypes } from 'react';

class App extends Component {
  // 类型检验
  static propTypes = {
  	// ...
  };
	// 默认类型
  static defaultProps = {
  	// ...
  };
  constructor(props) {
    super(props);
    this.state = {
    	// ...
    };
  }
  componentWillMount() {
  	// ...
  }
  componentDidMount() {
 		// ...
  }
  render() {
  	return <div>This is a demo.</div>;
  }
}
```

##### 组件的卸载 

​	组件卸载非常简单，只有` componentWillUnmount `这一个卸载前状态 

​	在 `componentWillUnmount `方法中，我们常常会执行一些清理方法，如事件回收或是清除定时器。 

##### 数据更新过程 

​	更新过程指的是父组件向下传递 props 或组件自身执行 setState 方法时发生的一系列更新动作。 

​	如果组件自身的 state 更新了，那么会依次执行 `shouldComponentUpdate`、 `componentWillUpdate` 、
`render` 和 `componentDidUpdate`。 	

​	`shouldComponentUpdate` 是一个特别的方法，它接收需要更新的 props 和 state，让开发者增加
必要的条件判断，让其在需要时更新，不需要时不更新。因此，当方法返回 false 的时候，组件
不再向下执行生命周期方法。 

​	如果组件是由父组件更新 props 而更新的，那么在 shouldComponentUpdate 之前会先执行componentWillReceiveProps 方法。此方法可以作为 React 在 props 传入后，渲染之前 setState 的机会。在此方法中调用 setState 是不会二次渲染的。 

##### 生命周期整体流程

![](https://upload-images.jianshu.io/upload_images/6541235-a397492137b82b90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第2章 漫谈 React

### 2.1 事件系统 

​	React 基于 Virtual DOM 实现了一个 SyntheticEvent （合成事件）层，我们所定义的事件处理器会接收到一个 SyntheticEvent 对象的实例，它完全符合 W3C 标准，不会存在任何 IE 标准的兼容性问题。并且与原生的浏览器事件一样拥有同样的接口，同样支持事件的冒泡机制，我们可以使用 stopPropagation() 和 preventDefault() 来中断它。 

#### 合成事件的实现机制

​	在 React 底层，主要对合成事件做了两件事：事件委派和自动绑定。 

#### 事件委派

​	在使用 React 事件前，一定要熟悉它的事件代理机制。它并不会把事件处理函数直接绑定到真实的节点上，而是把所有事件绑定到结构的最外层，使用一个统一的事件监听器，这个事件监听器上维持了一个映射来保存所有组件内部的事件监听和处理函数。 

​	当组件挂载或卸载时，只是在这个统一的事件监听器上插入或删除一些对象；当事件发生时，首先被这个统一的事件监听器处理，然后在映射里找到真正的事件处理函数并调用。这样做简化了事件处理和回收机制，效率也有很大提升。 

#### 自动绑定

​	在 React 组件中，每个方法的上下文都会指向该组件的实例，即自动绑定 this 为当前组件。而且 React 还会对这种引用进行缓存，以达到 CPU 和内存的最优化。在使用 ES6 classes 或者纯函数时，这种自动绑定就不复存在了，我们需要手动实现 this 的绑定。 

1.bind方法

```jsx
import React, { Component } from 'react';
class App extends Component {
  handleClick(e, arg) {
    console.log(e, arg);
  }
  render() {
    // 通过bind方法实现，可以传递参数
    return <button onClick={this.handleClick.bind(this, 'test')}>Test</button>;
    // 简略写法
    // return <button onClick={::this.handleClick}>Test</button>;
  }
}
```

2.构造器内声明

```jsx
import React, { Component } from 'react';
class App extends Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick(e) {
  	console.log(e);
  }
  render() {
  	return <button onClick={this.handleClick}>Test</button>;
  }
}
```

3.箭头函数

```jsx
import React, { Component } from 'react';
class App extends Component {
  handleClick(e) {
  	console.log(e);
  }
  render() {
  	return <button onClick={() => this.handleClick()}>Test</button>
  }
}
```

#### 在 React 中使用原生事件 

​	React 提供了完备的生命周期方法，其中 componentDidMount 会在组件已经完成安装并且在浏览器中存在真实的 DOM 后调用，此时我们就可以完成原生事件的绑定。 

```jsx
import React, { Component } from 'react';
class NativeEventDemo extends Component {
  componentDidMount() {
    this.refs.button.addEventListener('click', e => {
    this.handleClick(e);
  });
  }
  handleClick(e) {
  	console.log(e);
  }
  componentWillUnmount() {
  	this.refs.button.removeEventListener('click');
  }
  render() {
  	return <button ref="button">Test</button>;
  }
}
```

​	在 React 中使用 DOM 原生事件时，一定要在组件卸载时手动移除，否则很可能出现内存泄漏的问题。 

#### 合成事件与原生事件混用

例子: 我们无法在组件中将事件绑定到 body 上，因为 body 在组件范围之外，只能使用原生绑定事件来实现。 

```jsx
import React, { Component } from 'react';
class QrCode extends Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.handleClickQr = this.handleClickQr.bind(this);
    this.state = {
    active: false,
	};

  componentDidMount() {
    document.body.addEventListener('click', e => {
      this.setState({
      	active: false,
      });
    });
  }
  componentWillUnmount() {
  	document.body.removeEventListener('click');
  }
  handleClick() {
    this.setState({
    	active: !this.state.active,
    });
  }
  handleClickQr(e) {
  	e.stopPropagation();
  }
  render() {
    return (
      <div className="qr-wrapper">
        <button className="qr" onClick={this.handleClick}>二维码</button>
        <div
          className="code"
          style={{ display: this.state.active ? 'block' : 'none' }}
          onClick={this.handleClickQr}
          >
          <img src="qr.jpg" alt="qr" />
        </div>
      </div>
    );
  }
}
```

#### 对比 React 合成事件与 JavaScript 原生事件 

1. 事件传播与阻止事件传播 

   浏览器原生 DOM 事件的传播可以分为 3 个阶段：事件捕获阶段、目标对象本身的事件处理程序调用以及事件冒泡。

   - 事件捕获会优先调用结构树最外层的元素上绑定的事件监听器，然后依次向内调用，一直调用到目标元素上的事件监听器为止。 可以在将 e.addEventListener() 的第三个参数设置为 true 时，为元素 e 注册捕获事件处理程序，并且在事件传播的第一个阶段调用。 
   - 事件冒泡则与事件捕获的表现相反，它会从目标元素向外传播事件，由内而外直到最外层。 
   - 可以看出，事件捕获在程序开发中的意义并不大，更致命的是它的兼容性问题。所以， React的合成事件则并没有实现事件捕获，仅仅支持了事件冒泡机制。 

2. 事件类型 

   React 合成事件的事件类型是 JavaScript 原生事件类型的一个子集。 

3. 事件绑定方式 

   受到 DOM 标准的影响，绑定浏览器原生事件的方式也有很多种 

   - 直接在 DOM 元素中绑定：

     `<button onclick="alert(1);"></button> `

   - 在 JavaScript 中，通过为元素的事件属性赋值的方式实现绑定： 

     `el.onclick = e => { console.log(e); } `

   - 通过事件监听函数来实现绑定： 

     `el.addEventListener('click', () => {}, false);`
     `el.attachEvent('onclick', () => {}); `

   React 合成事件的绑定方式则简单得多： 

   `<button onClick={this.handleClick}>Test</button> `

4. 事件对象 

   ​	原生 DOM 事件对象在 W3C 标准和 IE 标准下存在着差异。在低版本的 IE 浏览器中，只能使用 window.event 来获取事件对象。而在 React 合成事件系统中，不存在这种兼容性问题，在事件处理函数中可以得到一个合成事件对象。 

### 2.2 表单 

#### 受控组件 

​	每当表单的状态发生变化时，都会被写入到组件的 state 中，这种组件在React 中被称为受控组件（controlled component）。在受控组件中，组件渲染出的状态与它的 value或 checked prop 相对应。 

React 受控组件更新 state 的流程： 

​	(1)  可以通过在初始 state 中设置表单的默认值。
​	(2)  每当表单的值发生变化时，调用 onChange 事件处理器。
​	(3)  事件处理器通过合成事件对象 e 拿到改变后的状态，并更新应用的 state。
​	(4)  setState 触发视图的重新渲染，完成表单组件值的更新。

​	在 React 中，数据是单向流动的。从示例中，我们能看出来表单的数据源于组件的 state，并通过 props 传入，这也称为单向数据绑定。然后，我们又通过 onChange 事件处理器将新的表单数据写回到组件的 state，完成了双向数据绑定。  

#### 非受控组件

​	如果一个表单组件没有 value props（单选按钮和复选框对应的是 checked prop）时，就可以称为非受控组件。相应地，你可以使用 defaultValue 和 defaultChecked prop 来表示组件的默认状态。 

#### 对比受控组件和非受控组件 

1. 性能上的问题 

   在受控组件中，每次表单的值发生变化时，都会调用一次 onChange 事件处理器，这确实会
   有一些性能上的损耗。虽然使用非受控组件不会出现这些问题，但仍然不提倡在 React 中使用非
   受控组件。 

2. 是否需要事件绑定 

   使用受控组件最令人头疼的就是，我们需要为每个组件绑定一个 change 事件，并且定义一个事件处理器来同步表单值和组件的状态，这是一个必要条件。 

### 2.3 CSS 模块化遇到了哪些问题？ 

- 全局污染 
- 命名混乱 
- 依赖管理不彻底 
- 无法共享变量 
- 代码压缩不彻底 

### 2.4 组件间通信 

#### 父组件向子组件通信 

React 数据流动是单向的，父组件向子组件的通信也是最常见的方式。父组件通过 props 向子组件传递需要的信息。 

#### 子组件向父组件通信 

- **利用回调函数**：这是 JavaScript 灵活方便之处，这样就可以拿到运行时状态。 

- **利用自定义事件机制**：这种方法更通用，使用也更广泛。设计组件时，考虑加入事件机
  制往往可以达到简化组件 API 的目的。 

#### 跨级组件通信 

在 React 中，我们还可以使用 context 来实现跨级父子组件间的通信：

​	Context 就像一个全局变量一样，而全局变量正是导致应用走向混乱的罪魁祸首之一，给组件带来了外部依赖的副作用。在大部分情况下，我们并不推荐使用 context 。 

#### 没有嵌套关系的组件通信

​	在 componentDidMount 事件中，如果组件挂载完成，再订阅事件；当组件卸载的时候，在 componentWillUnmount 事件中取消事件的订阅。

### 2.5 组件间抽象 

#### mixin

#### 高阶函数

高阶组件（higher-order component），类似于高阶函数，它接受 React 组件作为输入，输出一个新的 React 组件。 

### 2.6 组件性能优化

#### 纯函数 

纯函数由三大原则构成：

- 给定相同的输入，它总是返回相同的输出；

  - 比如随机函数,数组的改变原数组的方法

- 过程没有副作用

  - 就是说在纯函数中我们不能改变外部状态。 

- 没有额外的状态依赖。  

  - 指方法内的状态都只在方法的生命周期内存活，这意味着我们不能在方法内使用共享变量，因为这会给方法带来不可知因素。 

#### PureRender

​	PureRender 中的 Pure 指的就是组件满足纯函数的条件，即组件的渲染是被相同的 props 和 state 渲染进而得到相同的结果。

##### 本质

​	其原理为重新实现了 shouldComponentUpdate 生命周期方法，让当前传入的 props和 state 与之前的作浅比较，如果返回 false，那么组件就不会执行 render 方法。 

#### Immutable 

在传递数据时，可以直接使用 Immutable Data 来进一步提升组件的渲染性能。 

JavaScript 中的对象一般是可变的（mutable），因为使用了引用赋值，新的对象简单地引用了原始对象，改变新的对象将影响到原始对象。 

##### Immutable Data 

Immutable Data 就是一旦创建，就不能再更改的数据。对 Immutable 对象进行修改、添加或删除操作，都会返回一个新的 Immutable 对象。 

##### Immutable 的优点

- 降低了“可变”带来的复杂度。 
- 节省内存。  
- 撤销/重做，复制/粘贴，甚至时间旅行这些功能做起来都是小菜一碟。 
- 并发安全。 
- 拥抱函数式编程。  

##### 使用 Immutable 的缺点 

Immutable 中的 Map 和 List 虽然对应的是 JavaScript 的 Object 和 Array

##### Immutable.is 

​	两个 Immutable 对象可以使用 === 来比较，这样是直接比较内存地址，其性能最好。但是即使两个对象的值是一样的，也会返回 false： 

```js
let map1 = Immutable.Map({a:1, b:1, c:1});
let map2 = Immutable.Map({a:1, b:1, c:1});
map1 === map2; // => false
```

为了直接比较对象的值， Immutable 提供了 Immutable.is 来作“值比较”：

```js
Immutable.is(map1, map2); // => true
```

Immutable.is 比较的是两个对象的 hashCode 或 valueOf（对于 JavaScript 对象）。 

##### Immutable 与 PureRender 

​	React 做性能优化时最常用的就是 shouldComponentUpdate 方法，但它默认返回 true，即始终会执行 render 方法，然后做 Virtual DOM 比较，并得出是否需要做真实 DOM的更新，这里往往会带来很多没必要的渲染。 

#### key

​	如果每一个子组件是一个数组或迭代器的话，那么必须有一个唯一的 key prop。 

​	假如需要渲染一个有 5000 项的成绩排名榜单，而且每隔几秒就会更新一次排名，其中大部分排名只是位置变了，还有少部分的是完全更新了，少部分则是清出榜单了。
此时 key 就发挥作用了，它是用来标识当前项的唯一性的 props。 

​	我们把 key 设成了序号，这么做的确不会报警告了，但这是非常低效的做法。 

### 2.7 动画 

### 2.8 自动化测试 

---

## 第3章 解读 React 源码

### 3.1 初探React源码

​	在 Web 开发中，要将更新的数据实时反应到 UI 上，就不可避免地需要对 DOM 进行操作，而复杂频繁的 DOM 操作通常是产生性能瓶颈的原因之一。为此， React 引入了 Virtual DOM 机制。毫不夸张地说， Virtual DOM 是 React 的核心与精髓所在，而 reconciler 就是实现 Virtual DOM的主要源码。 

​	基于 React 进行开发时，所有的 DOM 树都是通过 Virtual DOM 构造的。 React 在 Virtual DOM上实现了 DOM diff 算法，当数据更新时，会通过 diff 寻找到需要变更的 DOM 节点，并只对变化的部分进行实际的浏览器的 DOM 更新，而不是重新渲染整个 DOM 树。 

​	尽管每一次都需要构造完整的 Virtual DOM 树，但由于 Virtual DOM 是 JavaScript 对象，性能极高，而对原生 DOM 进行操作的仅仅是 diff 部分，因而能达到提高性能的目的。这样，在保证性能的同时，开发者将不再需要关注某个数据的变化如何更新到具体的 DOM 元素，而只需要关心在任意数据状态下，整个界面是如何渲染的。 

### 3.2 Virtual DOM 模型 

​	Virtual DOM 模型负责 Virtual DOM 底层框架的构建工作，它拥有一整套的 VirtualDOM 标签，并负责虚拟节点及其属性的构建、更新、删除等工作。 

Virtual DOM 中的节点称为 ReactNode，它分为3种类型 ReactElement、 ReactFragment 和 ReactText。其中， ReactElement 又分为 ReactComponentElement 和 ReactDOMElement。 

#### 创建 React 元素 

通过 JSX 创建的虚拟元素最终会被编译成调用 React 的 createElement 方法。 

#### 初始化组件入口

当使用 React 创建组件时，首先会调用 `instantiateReactComponent`，这是初始化组件的入口函数，它通过判断 node 类型来区分不同组件的入口。 

- 当node为空

  说明 node 不存在，则初始化空组件 ReactEmptyComponent.create(instantiateReactComponent)。 

- 当 node 类型为对象

  当 node 类型为对象时，即是 DOM 标签组件或自定义组件，那么如果 element 类型为字符 串 时 ， 则初 始 化 DOM 标 签 组 件 ReactNativeComponent.createInternalComponent(element)，否则初始化自定义组件 ReactCompositeComponentWrapper()。 

- 当 node 类型为字符串或数字时 

  则初始化文本组件 ReactNativeComponent.createInstanceForText(node)。 

- 如果是其他情况，则不作处理。

![image.png](https://upload-images.jianshu.io/upload_images/6541235-de73805c1d9fb06d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 文本组件

​	当 node 类型为文本节点时是不算 Virtual DOM 元素的，但 React 为了保持渲染的一致性，将其封装为文本组件 ReactDOMTextComponent。 

#### DOM 标签组件 

​	Virtual DOM 模型涵盖了几乎所有的原生 DOM 标签，如 <div>、 <p>、 <span> 等。当开发者使用 React 时，此时的 `<div>` 并不是原生` <div>` 标签，它其实是 React 生成的 Virtual DOM 对象，只不过标签名称相同罢了。 React 的大部分工作都是在 Virtual DOM 中完成的，对于原生 DOM 而言， Virtual DOM 就如同一个隔离的沙盒，因此 React 的处理并不是直接操作和污染原生 DOM，这样不仅保持了性能上的高效和稳定，而且降低了直接操作原生 DOM 而导致错误的风险。 

ReactDOMComponent 针对 Virtual DOM 标签的处理主要分为以下两个部分：

- 属性的更新，包括更新样式、更新属性、处理事件等；
- 子节点的更新，包括更新内容、更新子节点，此部分涉及 diff 算法。 

### 3.3 生命周期的管理艺术 (重点)

#### 初探 React 生命周期 

- 当首次挂载组件时， 按顺序执行 getDefaultProps、 getInitialState、 componentWillMount、render 和 componentDidMount。 
- 当卸载组件时，执行 componentWillUnmount。 
- 当重新挂载组件时，此时按顺序执行 getInitialState、 componentWillMount、 render 和componentDidMount，但并不执行 getDefaultProps。 
- 当再次渲染组件时，组件接受到更新状态，此时按顺序执行 componentWillReceiveProps、shouldComponentUpdate、 componentWillUpdate、 render 和 componentDidUpdate。 

当使用 ES6 classes 构建 React 组件时， static defaultProps = {} 其实就是调用内部的 getDefaultProps 方法，constructor 中的 this.state = {} 其实就是调用内部的 getInitialState 方法。 

![](https://upload-images.jianshu.io/upload_images/6541235-38db765ab7146954.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 详解 React 生命周期 

​	自定义组件（ReactCompositeComponent）的生命周期主要通过 3 个阶段进行管理——MOUNTING、 RECEIVE_PROPS 和 UNMOUNTING，它们负责通知组件当前所处的阶段，应该执行生命周期中的哪个步骤。这 3 个阶段对应 3 种方法，分别为：**mountComponent**、**updateComponent**和 **unmountComponent**，每个方法都提供了几种处理方法，其中带 will 前缀的方法在进入状态之前调用，带 did 前缀的方法在进入状态之后调用。  

##### 阶段一： MOUNTING 

mountComponent 负责管理生命周期中的 getInitialState、 componentWillMount、 render 和 componentDidMount。 

​	若存在 componentWillMount，则执行。如果此时在 componentWillMount 中调用 setState 方法， 是不会触发 re-render的，而是会进行 state 合并， 且 inst.state = this._processPendingState(inst.props, inst.context) 是在componentWillMount 之后执行的，因此 componentWillMount 中的 this.state 并不是最新的，在 render 中才可以获取更新后的 this.state。 

![image.png](https://upload-images.jianshu.io/upload_images/6541235-4aaf6f6dd38c5557.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 阶段二： RECEIVE_PROPS 

updateComponent 负责管理生命周期中的 **componentWillReceiveProps**、 **shouldComponentUpdate**、**componentWillUpdate**、 **render** 和 **componentDidUpdate**。 

首先通过 updateComponent 更新组件，如果前后元素不一致，说明需要进行组件更新。

​	若存在 componentWillReceiveProps，则执行。如果此时在 componentWillReceiveProps 中调用 setState，是不会触发 re-render 的，而是会进行 state 合并。且在 componentWillReceiveProps、shouldComponentUpdate 和 componentWillUpdate 中也还是无法获取到更新后的 this.state，即此时访问的 this.state 仍然是未更新的数据，需要设置 inst.state = nextState 后才可以，因此只有在 render 和 componentDidUpdate 中才能获取到更新后的 this.state。  

调用 shouldComponentUpdate 判断是否需要进行组件更新，如果存在 componentWillUpdate，则执行。 

updateComponent 本质上也是通过递归渲染内容的，由于递归的特性，父组件的 componentWillUpdate 是在其子组件的 componentWillUpdate 之前调用的，而父组件的 componentDidUpdate也是在其子组件的 componentDidUpdate 之后调用的。 

![1548831228224](C:\Users\wangqi\AppData\Roaming\Typora\typora-user-images\1548831228224.png)

##### 阶段三： UNMOUNTING 

unmountComponent 负责管理生命周期中的 componentWillUnmount。 

​	如果存在 componentWillUnmount，则执行并重置所有相关参数、更新队列以及更新状态，如果此时在 componentWillUnmount 中调用 setState，是不会触发 re-render 的，这是因为所有更新队列和更新状态都被重置为 null，并清除了公共类，完成了组件卸载操作。 

#### 无状态组件 

 无状态组件也叫函数组件, 只是一个 render 方法，并没有组件类的实例化过程，也没有实例返回。 

无状态组件没有状态，没有生命周期，只是简单地接受 props 渲染生成 DOM 结构，是一个纯粹为渲染而生的组件。 

无状态组件的生命周期:

![image.png](https://upload-images.jianshu.io/upload_images/6541235-224069f9281af961.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.4 解密 setState 机制 

React 通过 this.state 来访问 state，通过 this.setState() 方法来更新 state。当this.setState() 被调用的时候， React 会重新调用 render 方法来重新渲染 UI。 

#### setState 异步更新 

setState 通过一个队列机制实现 state 更新。当执行 setState 时，会将需要更新的 state 合并后放入状态队列，而不会立刻更新 this.state，队列机制可以高效地批量更新 state。 

如果不通过setState 而直接修改 this.state 的值，那么该 state 将不会被放入状态队列中，当下次调用setState 并对状态队列进行合并时，将会忽略之前直接被修改的 state，而造成无法预知的错误。 

因此，应该使用 setState 方法来更新 state，同时 React 也正是利用状态队列机制实现了 setState
的异步更新，避免频繁地重复更新 state。 

#### setState 循环调用风险 

当调用 setState 时，实际上会执行 enqueueSetState 方法，并对 partialState 以及_pendingStateQueue 更新队列进行合并操作，最终通过 enqueueUpdate 执行 state 更新。 

而 performUpdateIfNecessary 方法会获取 _pendingElement、 _pendingStateQueue、 _pendingForceUpdate，并调用 receiveComponent 和 updateComponent 方法进行组件更新。 

如 果 在 shouldComponentUpdate 或 componentWillUpdate 方 法 中 调 用 setState ， 此 时this._pendingStateQueue != null，则 performUpdateIfNecessary 方法就会调用 updateComponent方法进行组件更新，但 updateComponent 方法又会调用 shouldComponentUpdate 和 componentWillUpdate 方法，因此造成循环调用，使得浏览器内存占满后崩溃

#### setState 调用栈

setState 最终是通过 enqueueUpdate 执行 state 更新 

一个简化的 setState 调用栈，注意其中核心的状态判断。 

![image.png](https://upload-images.jianshu.io/upload_images/6541235-00e73c470f699ca0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.5 diff 算法 

diff 作为 Virtual DOM 的加速器，其算法上的改进优化是 React 整个界面渲染的基础和性能保障，同时也是 React 源码中最神秘、最不可思议的部分。 

React 中最值得称道的部分莫过于 Virtual DOM 模型与 diff 的完美结合，特别是其高效的diff 算法， 因为 diff 会帮助我们计算出 Virtual DOM 中真正变化的部分，并只针对该部分进行原生 DOM 操作，而非重新渲染整个页面，从而保证了每次操作更新后页面的高效渲染。因此， Virtual DOM 模型与 diff 是保证 React 性能口碑的幕后推手。 

#### 详解 diff

##### diff 策略

React diff 算法的 3 个策略

- 策略一： Web UI 中 DOM 节点跨层级的移动操作特别少，可以忽略不计。 
- 策略二： 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。 
- 策略三： 对于同一层级的一组子节点，它们可以通过唯一 id 进行区分。 

React 分别对 tree diff、 component diff 以及 element diff 进行算法优化。 

##### tree diff 

基于策略一， React 对树的算法进行了简洁明了的优化，即对树进行分层比较，两棵树只会对同一层次的节点进行比较。 

既然 DOM 节点跨层级的移动操作少到可以忽略不计，针对这一现象，React 通过 updateDepth对 Virtual DOM 树进行层级控制，只会对相同层级的 DOM 节点进行比较，即同一个父节点下的所有子节点。当发现节点已经不存在时，则该节点及其子节点会被完全删除掉，不会用于进一步的比较。 

当出现节点跨层级移动时，并不会出现想象中的移动操作，而是以 A 为根节点的整个树被重新创建。这是一种影响 React 性能的操作，因此官方建议不要进行 DOM 节点跨层级的操作。 

##### component diff

- 如果是同一类型的组件，按照原策略继续比较 Virtual DOM 树即可。 
- 如果不是，则将该组件判断为 dirty component，从而替换整个组件下的所有子节点。 
- 对于同一类型的组件，有可能其 Virtual DOM 没有任何变化，如果能够确切知道这点，那么就可以节省大量的 diff 运算时间。因此， React 允许用户通过 `shouldComponentUpdate()`来判断该组件是否需要进行 diff 算法分析。 

##### element diff 

当节点处于同一层级时， diff 提供了 3 种节点操作，分别为 INSERT_MARKUP（插入）、 MOVE_EXISTING（移动）和 REMOVE_NODE（删除）。 

![](https://upload-images.jianshu.io/upload_images/6541235-af668e2ba33f41bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

React 发现这类操作烦琐冗余，因为这些都是相同的节点，但由于位置发生变化，导致需要进行繁杂低效的删除、创建操作，其实只要对这些节点进行位置移动即可。 

针对这一现象， React 提出优化策略：允许开发者对同一层级的同组子节点，添加唯一 key 进行区分， 进行 diff 差异化对比后，通过 key 发现新旧集合中的节点都是相同的节点，因此无需进行节点删除和创建，只需要将旧集合中节点的位置进行移动，更新为新集合中节点的位置，此时 React 给出的 diff 结果为： B、 D 不做任何操作， A、 C 进行移动操作即可。 

### 3.6 React Patch 方法 

React Patch 实现了关键的最后一步。所谓 Patch，简而言之就是将 tree diff 计算出来的 DOM差异队列更新到真实的 DOM 节点上，最终让浏览器能够渲染出更新的数据。 

它的实现却非常简洁明了，主要是通过遍历差异队列实现的。遍历差异队列时，通过更新类型进行相应的操作，包括：新节点的插入、已有节点的移动和移除等。 

## 第4章 认识 Flux 架构模式

## 第5章 深入 Redux 应用架构

![image.png](https://upload-images.jianshu.io/upload_images/6541235-10b5e89fa55892af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 5.1 Redux 简介 

#### Redux三大原则

1. 单一数据源 

   1. 在 Redux 的思想里，一个应用永远只有唯一的数据源。 实际上，使用单一数据源的好处在于整个应用状态都保存在一个对象中，这样我们随时可以提取出整个应用的状态进行持久化（比如实现一个针对整个应用的即时保存功能）。 至于我们担心的数据源对象过于庞大的问题， 我们可以使用combineReducers 是如何化解的。

    

2. 状态是只读的 

   1. 不同的是在 Flux 中，因为 store 没有 setter 而限制了我们直
      接修改应用状态的能力， 而在 Redux 中，这样的限制被执行得更加彻底，因为我们压根没有 store。 

   2. 在 Redux 中，我们并不会自己用代码来定义一个 store。取而代之的是，我们定义一个 reducer，
      它的功能是根据当前触发的 action 对当前应用的状态（state）进行迭代，这里我们并没有直接修改应用的状态，而是返回了一份全新的状态。 []()

   3. Redux 提供的 createStore 方法会根据 reducer 生成 store。最后，我们可以利用 store. dispatch
      方法来达到修改状态的目的。 

3. 状态修改均由纯函数完成 

   1. 这 是 Redux 与 Flux 在 表 现 上 的 最 大 不 同 。 在 Flux 中 ， 我 们 在 actionCreator 里 调 用
      AppDispatcher.dispatch 方法来触发 action，这样不仅有冗余的代码，而且因为直接修改了 store 中
      的数据，将导致无法保存每次数据变化前后的状态。 
   2. 在 Redux 里，我们通过定义 reducer 来确定状态的修改，而每一个 reducer 都是纯函数，这意
      味着它没有副作用，即接受一定的输入，必定会得到一定的输出。
   3.  这样设计的好处不仅在于 reducer 里对状态的修改变得简单、纯粹、可测试，更有意思的是，Redux 利用每次新返回的状态生成酷炫的时间旅行（time travel）调试方式，让跟踪每一次因为触发 action 而改变状态的结果成为了可能。 



   #### Redux 核心 API 

   ​	Redux 的核心是一个 store，这个 store 由 Redux 提供的 createStore(reducers[, initialState])方法生成。从函数签名看出，要想生成 store，必须要传入 reducers，同时也可以传入第二个可选参数初始化状态（initialState）。 

   ​	在 Redux 里，负责响应 action 并修改数据的角色就是 reducer。 reducer 本质上是一个函数， 其函数签名为 reducer(previousState, action) => newState。 可以看出， reducer 在处理 action 的同时，还需要接受一个 previousState 参数。所以， reducer 的职责就是根据 previousState 和action 计算出新的 newState。 

   ​	在实际应用中， reducer 在处理 previousState 时，还需要有一个特殊的非空判断。很显然，reducer 第一次执行的时候，并没有任何的 previousState，而 reducer 的最终职责是返回新的 state，因此需要在这种特殊情况下返回一个定义好的 initialState： 

   ```js
   // MyReducer.js
   const initialState = {
   	todos: [],
   };
   // 我们定义的 todos 这个 reducer 在第一次执行的时候，会返回 { todos: [] } 作为初始化状态
   function todos(previousState = initialState, action) {
   	switch (action.type) {
         case 'XXX': {
     // 具体的业务逻辑
     }
     default:
       return previousState;
       }
   } 
   ```

Redux 中最核心的 API——createStore： 

```js
import { createStore } from 'redux';
const store = createStore(reducers);
```

通过 createStore 方法创建的 store 是一个对象，它本身又包含 4 个方法。 

- **getState()**：获取 store 中当前的状态。 
- **dispatch(action) **：分发一个 action，并返回这个 action，这是唯一能改变 store 中数据的方式。 
- **subscribe(listener)**：注册一个监听者，它在 store 发生变化时被调用。 
- **replaceReducer(nextReducer)**：更新当前 store 里的 reducer，一般只会在开发模式中调用
  该方法。 

在实际使用中，我们最常用的是 getState() 和 dispatch() 这两个方法。至于 subscribe() 和replaceReducer()方法，一般会在 Redux 与某个系统（如 React）做桥接的时候使用。 

#### 与 React 绑定 

前面说到 Redux 的核心只有一个 createStore() 方法，但是仅仅使用这个方法还不足以让Redux 在我们的 React 应用中发挥作用。我们还需要 react-redux 库——Redux 官方提供的 React绑定。 

react-redux 提供了一个组件和一个 API 帮助 Redux 和 React 进行绑定，一个是 React 组件`<Provider/>` ，一个是 connect()。 

- `<Provider/>` 接受一个 store 作为props，它是整个 Redux 应用的顶层组件 
- connect() 提供了在整个 React 应用的任意组件中获取 store 中数据的功能。 

在摒弃了传统 MVC 的发布/订阅模式并通过 Redux 三大原则强化对状态的修改后，使用 Redux 可以让你的应用状态管理变得可预测、可追溯。

### 5.2 Redux middleware

在middleware 中，你可以检阅每一个流过的 action，挑选出特定类型的 action 进行相应操作，给你一次改变 action 的机会。 

#### middleware 的由来

![](https://upload-images.jianshu.io/upload_images/6541235-3dd44bfcc424de4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Redux同步数据流动的过程大致是:

1. 用户点击button触发一个dispatch
2. dispatch分发出一个action
3. reducer根据action.type返回对应的新state
4. 更新state并通知view

#### 理解 middleware 机制 

Redux 提供了 applyMiddleware 方法来加载 middleware，该方法的源码如下： 

```js
import compose from './compose';

export default function applyMiddleware(...middlewares) {
  return (next) => (reducer, initialState) => {
    let store = next(reducer, initialState);
    let dispatch = store.dispatch;
    let chain = [];
    
    var middlewareAPI = {
      getState: store.getState,
      dispatch: (action) => dispatch(action),
    };
    chain = middlewares.map(middleware => middleware(middlewareAPI));
    dispatch = compose(...chain)(store.dispatch);
    
    return {
      ...store,
      dispatch,
    };
  }
}
```

然后再来看 logger middleware 的实现： 

```js
export default store => next => action => {
  console.log('dispatch:', action);
  next(action);
  console.log('finish:', action);
}
```

#### 深入解析 middleware 的运行原理

(后续补上)

### 5.3 Redux 异步流

#### 使用 middleware 简化异步请求

1. redux-thunk 
2. redux-promise
3. redux-composable-fetch 

### 5.4 Redux 与路由 

要开发一个富客户端应用，有一样东西是必不可少的——路由（router）系统。所有 SPA 都必然会由一个路由系统作为整个系统的入口。 

#### React Router 

它提供了与 React 思想十分贴合的声明式的路由系统。我们可以通过` <Router> `、 `<Route>` 这两个标签以及一系列属性定义整个 React 应用的路由方案。 

##### 路由的基本原理 