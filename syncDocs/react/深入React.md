# ① 初探

## HMTL的渲染过程

​	这个结构化文本就是 HTML 文本， HTML 中的每个元素都对应 DOM中某个节点，这样，因为 HTML 元素的逐级包含关系， DOM 节点自然就构成了一个树形结构，称为 DOM 树 。
​	浏览器为了渲染 HTML 格式的网页，会先将 HTML 文本解析以构建 DOM 树，然后根据 DOM 树渲染出用户看到的界面，当要改变界面内容的时候，就去改变 DOM 树上的节点 。 

## 纯函数

React 的理念 ，归结为一个公式，就像下面这样 ：
​	`UI=render(data)`
​	让我们来看看这个公式表达的含义，用户看到的界面（ UI)，应该是一个函数（在这里叫 render）的执行结果，只接受数据（ data）作为参数 。

​	 这个函数是一个纯函数，所谓**纯函数**，指的是没有任何副作用，输出完全依赖于输入的函数，两次函数调用如果输人
​	相同，得到的结果也绝对相同 。 如此一来，最终的用户界面，在 render 函数确定的情况下完全取决于输入数据 。 

## React初解

​	react的功能其实很单一，主要负责渲染的功能，现有的框架，比如angular是一个大而全的框架，用了angular几乎就不需要用其他工具辅助配合.

>  PS: react感觉类似VScode或sublime需要装各种插件来写代码,而angular就像webstorm一样集成了很多功能

## React 是什么

​	用脚本进行DOM操作的代价很昂贵。把DOM和JavaScript各自想象为一个岛屿，它们之间用收费桥梁连接，js每次访问DOM，都要途径这座桥，并交纳“过桥费”,访问DOM的次数越多，费用也就越高。

​	 因此，推荐的做法是尽量减少过桥的次数，努力待在ECMAScript岛上。因为这个原因react的虚拟dom就显得难能可贵了，它创造了虚拟dom并且将它们储存起来，每当状态发生变化的时候就会创造新的虚拟节点和以前的进行对比，让变化的部分进行渲染。

整个过程没有对dom进行获取和操作，只有一个渲染的过程，所以react说是一个ui框架。

## 组件的生命周期

![image.png](https://upload-images.jianshu.io/upload_images/4118241-d979d05af0b7d4db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1288/format/webp)

**组件在初始化时会触发5个钩子函数：**

**~~1、getDefaultProps()~~**

> ​	设置默认的props，也可以用defaultProps设置组件的默认属性。
>
> 这个函数只在 React.createClass 方法创造的组件类才会用到 。

**~~2、getInitialState()~~**

> ​	在使用es6的class语法时是没有这个钩子函数的，可以直接在constructor中定义this.state。此时可以访问this.props。
>
> ​	这个函数只在 React.createClass 方法创造的组件类才会用到 。

**3、componentWillMount()**

> ​	组件初始化时只调用，以后组件更新不调用，整个生命周期只调用一次，此时可以修改state。

**4、 render()**

> ​	react最重要的步骤，创建虚拟dom，进行diff算法，更新dom树都在此进行。此时就不能更改state了。
>
> ​	通常一个组件要发挥作用，总是要渲染一些东西， render 函数并不做实际的谊染动作，它只是返回一个 JSX 描述的结构，最终由 React 来操作渲染过程。
> ​	当然，某些特殊组件的作用不是渲染界面，或者，组件在某些情况下选择没有东西可画，那就让 render 函数返回一个 null 或者 false ，等于告诉 React，这个组件这次不需要渲染任何 DOM 元素 。
> ​	需要注意， render 函数应该是一个纯函数，完全根据 this.state 和 this.props 来决定返回的结果，而且不要产生任何副作用。在 render 函数中去调用 this.setState 毫无疑问是错误的，因为一个纯函数不应该引起状态的改变。 

**5、componentDidMount()**

> ​	Render 函数返回的东西已 经引发了渲染，组件已经被“装载”到了 DOM 树上 。 组件渲染之后调用，可以通过this.getDOMNode()获取和操作dom节点，只调用一次。

------

**在更新时也会触发5个钩子函数：**

**6、componentWillReceivePorps(nextProps)**

> 组件初始化时不调用，组件接受新的props时调用。

**7、shouldComponentUpdate(nextProps, nextState)**

> ​	React性能优化非常重要的一环。组件接受新的state或者props时调用，我们可以设置在此对比前后两个props和state是否相同，如果相同则返回false阻止更新，因为相同的属性状态一定会生成相同的dom树，这样就不需要创造新的dom树和旧的dom树进行diff算法对比，节省大量性能，尤其是在dom结构复杂的时候。不过调用this.forceUpdate会跳过此步骤。

**8、componentWillUpdate(nextProps, nextState)**

> 组件初始化时不调用，只有在组件将要更新时才调用，此时可以修改state

**9、render()**

> 当组件的state或者props发生改变的时候,render函数就会重新执行

**10、componentDidUpdate()**

> 组件初始化时不调用，组件更新完成后调用，此时可以获取dom节点。

还有一个卸载钩子函数

**11、componentWillUnmount()**

> 组件将要卸载时调用，一些事件监听和定时器需要在此时清除。

​	以上可以看出来react总共有10个周期函数（render重复一次），这个10个函数可以满足我们所有对组件操作的需求，利用的好可以提高开发效率和组件性能。

​	`render` 和 `shouldComponentUpdate `函数，也是 React 生命周期函数中唯二两个要求有返回结果的函数。			`render` 函数的返回结果将用于构造 DOM 对象，而 `shouldComponentUpdate `函数返回一个布尔值，告诉 React 库这个组件在这次更新过程中是否要继续 。 

### V16 生命周期函数用法建议

```jsx
class ExampleComponent extends React.Component {
  // 用于初始化 state
  constructor() {}
  // 用于替换 `componentWillReceiveProps` ，该函数会在初始化和 `update` 时被调用
  // 因为该函数是静态函数，所以取不到 `this`
  // 如果需要对比 `prevProps` 需要单独在 `state` 中维护
  static getDerivedStateFromProps(nextProps, prevState) {}
  // 判断是否需要更新组件，多用于组件性能优化
  shouldComponentUpdate(nextProps, nextState) {}
  // 组件挂载后调用
  // 可以在该函数中进行请求或者订阅
  componentDidMount() {}
  // 用于获得最新的 DOM 数据
  getSnapshotBeforeUpdate() {}
  // 组件即将销毁
  // 可以在此处移除订阅，定时器等等
  componentWillUnmount() {}
  // 组件销毁后调用
  componentDidUnMount() {}
  // 组件更新后调用
  componentDidUpdate() {}
  // 渲染组件函数
  render() {}
  // 以下函数不建议使用
  UNSAFE_componentWillMount() {}
  UNSAFE_componentWillUpdate(nextProps, nextState) {}
  UNSAFE_componentWillReceiveProps(nextProps) {}
}
```

## 父子组件的渲染过程

​	因为 render 函数本身并不往 DOM 树上渲染或者装载内容，它只是返回一个 JSX 表示的对象，然后由 React 库来根据返回对象决定如何渲染 。而 React 库肯定是要把所有组件返回的结果综合起来，才能知道该如何产生对应的 DOM修改 。 所以，只有 React 库调用三个 Counter 组件的 render 函数之后，才有可能完成装载，这时候才会依次调用各个组件的 componentDidMount 函数作为装载过程的收尾 。 

## React的组件化

​	react的一个组件很明显的由dom视图和state数据组成，两个部分泾渭分明。

​	state是数据中心，它的状态决定着视图的状态。这时候发现似乎和我们一直推崇的MVC开发模式有点区别，没了Controller控制器，那用户交互怎么处理，数据变化谁来管理？

​	然而这并不是react所要关心的事情，它只负责ui的渲染。与其他框架监听数据动态改变dom不同，react采用setState来控制视图的更新。

​	setState会自动调用render函数，触发视图的重新渲染，如果仅仅只是state数据的变化而没有调用setState，并不会触发更新。

​	 组件就是拥有独立功能的视图模块，许多小的组件组成一个大的组件，整个页面就是由一个个组件组合而成。它的好处是利于重复利用和维护。

## UI = render(data)

​	React 组件扮 演的是 render 函数的角色，应该是一个没有副作用的纯函数。修改
props 的值， 是一个副作用，组件应该避免。 

## 组件类别

**概念**: 所谓组件，简单说，指的是能完成某个特定功能的独立的 、 可重用的代码 。 

- 容器组件
  只关心逻辑,不负责页面渲染
- UI组件
  不关心逻辑,只负责页面渲染
- 无状态组件
  没有render()函数,只是一个函数,没有声明周期函数,效率更高

![image.png](https://upload-images.jianshu.io/upload_images/6541235-d7f21cd73ca628d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## React的 Diff算法

​	当组件更新的时候，react会创建一个新的虚拟dom树并且会和之前储存的dom树进行比较，这个比较多过程就用到了diff算法，**所以组件初始化的时候是用不到的**。

​	react提出了一种假设，相同的节点具有类似的结构，而不同的节点具有不同的结构。在这种假设之上进行逐层的比较，如果发现对应的节点是不同的，那就直接删除旧的节点以及它所包含的所有子节点然后替换成新的节点。如果是相同的节点，则只进行属性的更改。

​	对于列表的diff算法稍有不同，因为列表通常具有相同的结构，在对列表节点进行删除，插入，排序的时候，单个节点的整体操作远比一个个对比一个个替换要好得多，所以在创建列表的时候需要设置key值，这样react才能分清谁是谁。当然不写key值也可以，但这样通常会报出警告，通知我们加上key值以提高react的性能。

![image.png](https://upload-images.jianshu.io/upload_images/6541235-30533ee37b33be36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 演变过程: JSX > createElement > 虚拟dom (JS对象) > 真实dom

## 虚拟Dom的对比算法

### 不同类型的元素

​	每当根元素有不同类型，React将卸载旧树并重新构建新树。从`<a>`到`<img>`或从`<Article>`到`<Comment>`，或从`<Button>` 到 `<div>`，任何的调整都会导致全部重建。

​	当树被卸载，旧的DOM节点将被销毁。组件实例会调用`componentWillUnmount()`。当构建一棵新树，新的DOM节点被插入到DOM中。组件实例将依次调用`componentWillMount()`和`componentDidMount()`。任何与旧树有关的状态都将丢弃。

​	这个根节点下所有的组件都将会被卸载，同时他们的状态将被销毁。

### 相同类型的DOM元素

​	当比较两个相同类型的React DOM元素时，React则会观察二者的属性，保持相同的底层DOM节点，并仅更新变化的属性。

### 相同类型的组件元素

​	当组件更新时，实例仍保持一致，以让状态能够在渲染之间保留。React通过更新底层组件实例的props来产生新元素，并在底层实例上依次调用`componentWillReceiveProps()` 和 `componentWillUpdate()` 方法。

​	接下来，`render()`方法被调用，同时对比算法会递归处理之前的结果和新的结果。

## React diff算法流程图

![1545621734055](C:\Users\wangqi\AppData\Roaming\Typora\typora-user-images\1545621734055.png)

## key的作用

​	React DOM 首先会比较元素内容先后的不同，而在渲染过程中只会更新改变了的部分。

​	key的重要性: 提高对比的效率

​	Keys可以在DOM中的某些元素被增加或删除的时候帮助React识别哪些元素发生了变化。因此你应当给数组中的每一个元素赋予一个确定的标识。

​	用数组下标作为 key，看起来 key 值是唯一的，但是却不是稳定不变的，随着 todos数组值的不同，同样一个 Todoltem 实例在不同的更新过程中在数组中的下标完全可能不同，把下标当做 key 就让 React 彻底乱套了 。
​	需要注意，虽然 key 是一个 prop ，但是接受 key 的组件并不能读取到 key 的值，因为 key 和 ref 是 React 保留的两个特殊 prop ，并没有预期让组件直接访问 。 

## 为什么使用setState修改数据?

​	直接修改` this.state `的值，虽然事实上改变了组件的内部状态，但只是野蛮地修改了state ，却没有驱动组件进行重新渲染，既然组件没有重新渲染，当然不会反应 this.state值的变化；

​	而 this.setState（）函数所做的事情，首先是改变 this.state 的值，然后驱动组件经历更新过程，这样才有机会让 this.state 里新的值出现在界面上 。 

## setState 是异步函数?

​	`setState()` 排队更改组件的 `state` ，并通过更新 `state `来告诉 `React `，该组件及其子组件需要重新渲染。这是用于 响应事件处理程序 和 服务器响应 更新用户界面的主要方法。

​	记住 `setState()` 作为一个请求，而不是立即命令来更新组件。为了更好的感知性能，`React` 可能会延迟它，然后合并多个`setState()`更新多个组件。` React`不保证` state` 更新就立即应用(重新渲染)。

​	React 可以将多个`setState()` 调用合并成一个调用来提高性能。

​	因为 `this.props` 和 `this.state` 可能是异步更新的，你不应该依靠它们的值来计算下一个状态。`setState()` 并不总是立即更新组件。它可能会 **批量** 或 **延迟**到后面更新。**这使得在调用 setState() 之后立即读取 this.state 存在一个潜在的陷阱。** 而使用 componentDidUpdate 或 setState 回调（setState(updater, callback)），在应用更新后，都将被保证触发。

举个例子:

​	例如，此代码可能无法更新计数器：

```jsx
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

​	要修复它，请使用第二种形式的 `setState()` 来接受一个函数而不是一个对象。 该函数将接收先前的状态作为第一个参数，将此次更新被应用时的props做为第二个参数：

```jsx
// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

​	`setState() `总是会导致重新渲染，除非 `shouldComponentUpdate() `返回 `false` 。如果可变对象被使用，并且条件渲染逻辑不能在` shouldComponentUpdate()` 中实现，只有当新` state `与先前 `state` 不同时调用 `setState() `才能避免不必要的重新渲染。

> 不能在`render()`里面写`this.setState() `会导致循环修改

## React组件写法

![image.png](https://upload-images.jianshu.io/upload_images/6541235-002d0fcfa6d388ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

ES6的class类可以看作是构造函数的一个语法糖，可以把它当成构造函数来看，extends实现了类之间的继承 —— 定义一个类Main 继承React.Component所有的属性和方法，组件的生命周期函数就是从这来的。

constructor是构造器，在实例化对象时调用，super调用了父类的constructor创造了父类的实例对象this，然后用子类的构造函数进行修改。

## super(props) 

​	如果在构造函数中没有调用` super(props)`,那么组件实例被构造之后，类实例的所有成员函数就无法通过 this.props 访问到父组件传递过来的 props 值。很明显，给 `this.props` 赋值是 `React.Component` 构造函数的工作之一 。 

## shouldCompnentUpdate生命周期

​	在通用的 shouldCompnentUpdate 函数中做“浅层比较”，是一个被普遍接受的做法；如果需要做“深层比较”，那就是某个特定组件的行为，需要开发者自己根据组件情况去编写 。  

## PureComponent

![image.png](https://upload-images.jianshu.io/upload_images/6541235-647a9de2647dd42f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

​	`React15.3` 中新加了一个类[PureComponent](https://facebook.github.io/react/docs/react-api.html#react.purecomponent)，前身是 `PureRenderMixin` ，和 `Component` 基本一样，只不过会在 `render`之前帮组件自动执行一次[shallowEqual](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/core/shallowEqual.js)（浅比较），来决定是否更新组件，浅比较类似于浅复制，只会比较第一层。使用 `PureComponent` 相当于省去了写 `shouldComponentUpdate` 函数，当组件更新时，如果组件的 `props` 和 `state`：

1. 引用和第一层数据都没发生改变， `render` 方法就不会触发，这是我们需要达到的效果。
2. 虽然第一层数据没变，但引用变了，就会造成虚拟 `DOM` 计算的浪费。
3. 第一层数据改变，但引用没变，会造成不渲染，所以需要很小心的操作数据。

so. 为了性能,React只做了浅对比,于是就有了immutable.js

## immutable.js

![image.png](https://upload-images.jianshu.io/upload_images/6541235-3be4ba158eb5e407.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 高阶组件

​	**高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件**

```jsx
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

​	对比组件将props属性转变成UI，高阶组件则是将一个组件转换成另一个新组件。

​	高阶组件在React第三方库中很常见，比如Redux的[`connect`](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options)方法和Relay的[`createContainer`](https://facebook.github.io/relay/docs/api-reference-relay.html#createcontainer-static-method).

## Refs属性

### 创建 Refs

​	使用 `React.createRef()` 创建 refs，通过 `ref` 属性来获得 React 元素。当构造组件时，refs 通常被赋值给实例的一个属性，这样你可以在组件中任意一处使用它们.

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

ref的值取决于节点的类型:

- 当 `ref` 属性被用于一个普通的 HTML 元素时，`React.createRef()` 将接收底层 DOM 元素作为它的 `current` 属性以创建 `ref` 。
- 当 `ref` 属性被用于一个自定义类组件时，`ref` 对象将接收该组件已挂载的实例作为它的 `current` 。
- **你不能在函数式组件上使用 ref 属性**，因为它们没有实例。

## React-Router路由

​	Router就是React的一个组件，它并不会被渲染，只是一个创建内部路由规则的配置对象，根据匹配的路由地址展现相应的组件。

​	Route则对路由地址和组件进行绑定，Route具有嵌套功能，表示路由地址的包涵关系，这和组件之间的嵌套并没有直接联系。Route可以向绑定的组件传递7个属性：children，history，location，params，route，routeParams，routes，每个属性都包涵路由的相关的信息。

​	比较常用的有children（以路由的包涵关系为区分的组件），location（包括地址，参数，地址切换方式，key值，hash值）。

​	react-router提供Link标签，这只是对a标签的封装，值得注意的是，点击链接进行的跳转并不是默认的方式，react-router阻止了a标签的默认行为并用pushState进行hash值的转变。

​	切换页面的过程是在点击Link标签或者后退前进按钮时，会先发生url地址的转变，Router监听到地址的改变根据Route的path属性匹配到对应的组件，将state值改成对应的组件并调用setState触发render函数重新渲染dom。

## 路由(按需加载)

​	当页面比较多时，项目就会变得越来越大，尤其对于单页面应用来说，初次渲染的速度就会很慢，这时候就需要按需加载，只有切换到页面的时候才去加载对应的js文件。react配合webpack进行按需加载的方法很简单，Route的component改为getComponent，组件用require.ensure的方式获取，并在webpack中配置chunkFilename。

```jsx
const chooseProducts = (location, cb) => {
    require.ensure([], require => {
        cb(null, require('../Component/chooseProducts').default)
    },'chooseProducts')
}

const helpCenter = (location, cb) => {
    require.ensure([], require => {
        cb(null, require('../Component/helpCenter').default)
    },'helpCenter')
}

const saleRecord = (location, cb) => {
    require.ensure([], require => {
        cb(null, require('../Component/saleRecord').default)
    },'saleRecord')
}

const RouteConfig = (
    <Router history={history}>
        <Route path="/" component={Roots}>
            <IndexRoute component={index} />//首页
            <Route path="index" component={index} />
            <Route path="helpCenter" getComponent={helpCenter} />//帮助中心
            <Route path="saleRecord" getComponent={saleRecord} />//销售记录
            <Redirect from='*' to='/'  />
        </Route>
    </Router>
);
```

## 组件之间的通信	

​	react推崇的是单向数据流，通常被称为`自顶向下`或`单向`数据流。 任何状态始终由某些特定组件所有，并且从该状态导出的任何数据或 UI 只能影响树中`下方`的组件。

解决通信问题的方法很多:

1. 如果只是父子级关系，父级可以将一个回调函数当作属性传递给子级，子级可以直接调用函数从而和父级通信。
2. 组件层级嵌套到比较深，可以使用上下文getChildContext来传递信息，这样在不需要将函数一层层往下传，任何一层的子级都可以通过this.context直接访问。
3. 兄弟关系的组件之间无法直接通信，它们只能利用同一层的上级作为中转站。而如果兄弟组件都是最高层的组件，为了能够让它们进行通信，必须在它们外层再套一层组件，这个外层的组件起着保存数据，传递信息的作用，这其实就是redux所做的事情。
4. 组件之间的信息还可以通过全局事件来传递。不同页面可以通过参数传递数据，下个页面可以用location.param来获取。

## React的事件委托

​	我们在 JSX 中看到一个组件使用了 onClick，但并没有产生直接使用 onclick （注意是 onclick 不是 onClick）的HTML ，而是使用了事件委托（event delegation）的方式处理点击事件，无论有多少个 onClick 出现，其实最后都只在 DOM 树上添加了一个事件处理函数，挂在最顶层的 DOM 节点上。

​	所有的点击事件都被这个事件处理函数捕获，然后根据具体组件分配给特定函数，使用事件委托的性能当然要比为每个 onClick 都挂载一个事件处理函数要高 。
​	因为 React 控制了组件的生命周期，在 unmount 的时候自然能够清除相关的所有事
件处理函数，内存泄露也不再是一个问题。 

# ② 进阶

## Redux

### 基本原则

Flux 的基本原则是“单向数据流”， Redux 在此基础上强调三个基本原则：

- 唯一数据源（ Single Source of Truth);

  ​	在 Flux 中，应用可以拥有多个 Store ，往往根据功能把应用的状态
  数据划分给若干个 Store 分别存储管理 。 

  ​	Redux 对这个问题的解决方法就是，整个应用只保持一个 Store ，所有组件的数据源
  就是这个 Store 上的状态 。 

- 保持状态只读（ State is read-only);

  ​	保持状态只读，就是说不能去直接修改状态，要修改 Store 的状态，必须要通过派发
  一个 action 对象完成，这一点 ，和 Flux 的要求并没有什么区别 。 

  ​	当然，要驱动用户界面渲染，就要改变应用的状态，但是改变状态的方法不是去修
  改状态上值，而是创建一个新的状态对象返回给 Redux ，由 Redux 完成新的状态的组装 。 

- 数据改变只能通过纯函数完成（ Changes are made with pure functions ） 。 

  ​	在 Redux 中， 每个 reducer 的函数签名如下所示 ：
  ​	`reducer(state , action )`
  ​	第一个参数 state 是当前的状态，第二个参数 action 是接收到的 action 对象，而 reducer函数要做的事情，就是根据 state 和 action 的值产生一个新的对象返回，注意 reducer 必须是纯函数，也就是说函数的返回结果必须完全由参数 state 和 action 决定，而且不产生任何副作用，也不能修改参数 state 和 action 对象。 

### Redux核心API

Redux主要由三部分组成：store，reducer，action。

#### store

​	`Redux`的核心是`store`，它由`Redux`提供的 `createStore(reducer， defaultState) `这个方法生成，生成三个方法，`getState(),dispatch(),subscrible()`。

![image.png](https://upload-images.jianshu.io/upload_images/6541235-51c16a46ef8bdf1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



- getState()：存储的数据，状态树；
- dispatch(action)：分发action，并返回一个action，这是唯一能改变store中数据的方式；
- subscrible(listener)：注册一个监听者，store发生变化的时候被调用。

#### reducer

reducer是一个纯函数，它根据previousState和action计算出新的state。
`reducer(previousState,action)`

![image.png](https://upload-images.jianshu.io/upload_images/6541235-766a57d69a8ba95f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### action

action本质上是一个JavaScript对象，其中必须包含一个type字段来表示将要执行的动作，其他的字段都可以根据需求来自定义。

```js
const ADD_TODO = 'ADD_TODO'
```

```js
{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
```

#### 整合

他们三者之间的交互，可以由下图概括：

![image.png](https://upload-images.jianshu.io/upload_images/6541235-a6e857a3f123c34d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/6541235-125307939d26dedc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 概念分析:

#### redux主要由三部分组成：store，reducer，action。

**store**是一个对象，它有四个主要的方法：

**1、dispatch:**

> ​	用于action的分发——在createStore中可以用middleware中间件对dispatch进行改造，比如当action传入dispatch会立即触发reducer，有些时候我们不希望它立即触发，而是等待异步操作完成之后再触发，这时候用redux-thunk对dispatch进行改造，以前只能传入一个对象，改造完成后可以传入一个函数，在这个函数里我们手动dispatch一个action对象，这个过程是可控的，就实现了异步。

**2、subscribe：**

> ​	监听state的变化——这个函数在store调用dispatch时会注册一个listener监听state变化，当我们需要知道state是否变化时可以调用，它返回一个函数，调用这个返回的函数可以注销监听。
>
> ` let unsubscribe = store.subscribe(() => {console.log('state发生了变化')})`

**3、getState：**

> ​	获取store中的state——当我们用action触发reducer改变了state时，需要再拿到新的state里的数据，毕竟数据才是我们想要的。
>
> ​	getState主要在两个地方需要用到，一是在dispatch拿到action后store需要用它来获取state里的数据，并把这个数据传给reducer，这个过程是自动执行的，二是在我们利用subscribe监听到state发生变化后调用它来获取新的state数据，如果做到这一步，说明我们已经成功了。

**4、replaceReducer:**

> 替换reducer，改变state修改的逻辑。

​	store可以通过createStore()方法创建，接受三个参数，经过combineReducers合并的reducer和state的初始状态以及改变dispatch的中间件，后两个参数并不是必须的。store的主要作用是将action和reducer联系起来并改变state。

**action:**

> ​	action是一个对象，其中type属性是必须的，同时可以传入一些数据。action可以用actionCreactor进行创造。dispatch就是把action对象发送出去。

**reducer:**

> ​	reducer是一个函数，它接受一个state和一个action，根据action的type返回一个新的state。根据业务逻辑可以分为很多个reducer，然后通过combineReducers将它们合并，state树中有很多对象，每个state对象对应一个reducer，state对象的名字可以在合并时定义。

```js
const reducer = combineReducers({
     a: doSomethingWithA,
     b: processB,
     c: c
})
```

**combineReducers:**

> ​	其实它也是一个reducer，它接受整个state和一个action，然后将整个state拆分发送给对应的reducer进行处理，所有的reducer会收到相同的action，不过它们会根据action的type进行判断，有这个type就进行处理然后返回新的state，没有就返回默认值，然后这些分散的state又会整合在一起返回一个新的state树。

### 流程分析:

1. 首先调用`store.dispatch`将`action`作为参数传入，同时用`getState`获取当前的状态树`state`并注册`subscribe`的`listener`监听`state`变化，再调用`combineReducers`并将获取的`state`和`action`传入。
2. combineReducers会将传入的state和action传给所有reducer，并根据action的type返回新的state，触发state树的更新，我们调用subscribe监听到state发生变化后用getState获取新的state数据。

**redux的state和react的state两者完全没有关系，除了名字一样。**

---

## React-Redux

### React-redux是怎么配合的

react-redux 的两个最主要功能：

- connect ：连接容器组件和视图组件；
- Provider ：提供包含 store 的 context。 

1. react-redux提供了connect和Provider两个好基友，它们一个将组件与redux关联起来，一个将store传给组件。
2. 组件通过dispatch发出action，store根据action的type属性调用对应的reducer并传入state和这个action，reducer对state进行处理并返回一个新的state放入store，connect监听到store发生变化，调用setState更新组件，此时组件的props也就跟着变化。
3. 值得注意的是connect，Provider，mapStateToProps,mapDispatchToProps是react-redux提供的，redux本身和react没有半毛钱关系，它只是数据处理中心，没有和react产生任何耦合，是react-redux让它们联系在一起。

Redux 本身和React没有关系，只是数据处理中心，是React-Redux让他们联系在一起。

### React-Redux的两个方法

#### connect

 **掘金资料**

connect连接React组件和Redux store。connect实际上是一个高阶函数，返回一个新的已与 Redux store 连接的组件类。

```js
const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```

`TodoList`是 UI 组件，VisibleTodoList就是由 react-redux 通过connect方法自动生成的容器组件。

1. `mapStateToProps`：从Redux状态树中提取需要的部分作为props传递给当前的组件。
2. `mapDispatchToProps`：将需要绑定的响应事件（action）作为props传递到组件上。

**书籍资料 **

```js
export default connect(mapStateToProps, mapDispatchToProps) ( Counter);
```

这个 connect 函数具体做了什么工作呢？
作为容器组件，要做的工作无外乎两件事：

- 把 Store 上的状态转化为内层傻瓜组件的 prop;
- 把内层傻瓜组件中的用户动作转化为派送给 Store 的动作 。 

![image.png](https://upload-images.jianshu.io/upload_images/6541235-579f290052061e3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Provider

Provider实现store的全局访问，将store传给每个组件。

原理：使用React的context，context可以实现跨组件之间的传递。

如果只使用redux，那么流程是这样的：

> component --> dispatch(action) --> reducer --> subscribe --> getState --> component

用了react-redux之后流程是这样的：

> component --> actionCreator(data) --> reducer --> component

store的三大功能：dispatch，subscribe，getState都不需要手动来写了。

react-redux帮我们做了这些，同时它提供了两个好基友Provider和connect。

**Provider**是一个组件，它接受store作为props，然后通过context往下传，这样react中任何组件都可以通过context获取store。

​	也就意味着我们可以在任何一个组件里利用dispatch(action)来触发reducer改变state，并用subscribe监听state的变化，然后用getState获取变化后的值。但是并不推荐这样做，它会让数据流变的混乱，过度的耦合也会影响组件的复用，维护起来也更麻烦。

**`connect --connect(mapStateToProps, mapDispatchToProps, mergeProps, options)`** 是一个函数，它接受四个参数并且再返回一个函数--wrapWithConnect，wrapWithConnect接受一个组件作为参数wrapWithConnect(component)，它内部定义一个新组件Connect(容器组件)并将传入的组件(ui组件)作为Connect的子组件然后return出去。

所以它的完整写法是这样的：`connect(mapStateToProps, mapDispatchToProps, mergeProps, options)(component)

#### **mapStateToProps(state, [ownProps])：**

> mapStateToProps 接受两个参数，store的state和自定义的props，并返回一个新的对象，这个对象会作为props的一部分传入ui组件。我们可以根据组件所需要的数据自定义返回一个对象。ownProps的变化也会触发mapStateToProps

```js
function mapStateToProps(state) {
   return { todos: state.todos };
}
```

#### **mapDispatchToProps(dispatch, [ownProps])：**

> mapDispatchToProps如果是对象，那么会和store绑定作为props的一部分传入ui组件。
>
> 如果是个函数，它接受两个参数，bindActionCreators会将action和dispatch绑定并返回一个对象，这个对象会和ownProps一起作为props的一部分传入ui组件。
>
> 所以不论mapDispatchToProps是对象还是函数，它最终都会返回一个对象，如果是函数，这个对象的key值是可以自定义的

```js
function mapDispatchToProps(dispatch) {
   return {
      todoActions: bindActionCreators(todoActionCreators, dispatch),
      counterActions: bindActionCreators(counterActionCreators, dispatch)
   };
}
```

`mapDispatchToProps`返回的对象其属性其实就是一个个actionCreator，因为已经和dispatch绑定，所以当调用actionCreator时会立即发送action，而不用手动dispatch。ownProps的变化也会触发mapDispatchToProps。

#### **mergeProps(stateProps, dispatchProps, ownProps)：**

> 将mapStateToProps() 与 mapDispatchToProps()返回的对象和组件自身的props合并成新的props并传入组件。默认返回 Object.assign({}, ownProps, stateProps, dispatchProps) 的结果。

#### **options：**

> pure = true 表示Connect容器组件将在shouldComponentUpdate中对store的state和ownProps进行浅对比，判断是否发生变化，优化性能。为false则不对比。

其实connect函数并没有做什么，大部分的逻辑都是在它返回的wrapWithConnect函数内实现的，确切的说是在wrapWithConnect内定义的Connect组件里实现的。

---

在项目中我使用的大store目录结构是:

![image.png](https://upload-images.jianshu.io/upload_images/6541235-a21be89454424d60.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```js
// index.js
import {createStore, compose, applyMiddleware} from 'redux';
import thunk from 'redux-thunk';
import reducers from './reducers';

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
const store = createStore(reducers, composeEnhancers(
  applyMiddleware(thunk)
));

export default store;
```

```js
// reducers.js
// 合并小的reducer
import {combineReducers} from 'redux-immutable'; // 提供的是immutable数据
import {reducer as userReducer} from './user'
import {reducer as chatUserReducer} from './chat_user'
import {reducer as chatReducer} from './chat'

const reducer = combineReducers({
  user: userReducer,
  chatUser: chatUserReducer,
  chat: chatReducer
});

export default reducer;
```



在项目中我使用的小store(举例)目录结构是:

![image.png](https://upload-images.jianshu.io/upload_images/6541235-03a992549641bde0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

核心代码:

```js
// _reducer.js
import * as constants from './constants'
import {getRedirectPath} from '../../common/js/util'

const initState = {
  isAuth: false,
  msg: '',
  user: '',
  pwd: '',
  type: ''
}

const defaultState = (localStorage.getItem('jobUser') && JSON.parse(localStorage.getItem('jobUser'))) || initState


export default (state = defaultState, action) => {
  switch (action.type) {
    case constants.AUTH_SUCCESS:
      localStorage.setItem('jobUser', JSON.stringify({
        ...state,
        msg: '',
        redirectTo: getRedirectPath(action.payload), ...action.payload
      }))
      return {...state, msg: '', redirectTo: getRedirectPath(action.payload), ...action.payload}
    case constants.LOAD_DATA:
      return {...state, ...action.payload}
    case constants.ERROR_MSG:
      return {...state, isAuth: false, msg: action.msg}
    case constants.LOGIN_OUT:
      return {redirectTo: '/login', ...initState}
    default:
      return state
  }
}
```

```js
// actionCreators.js
import * as constants from './constants'
import axios from 'axios'
const authSuccess = (obj) => {
  const {pwd, ...data} = obj
  return {type: constants.AUTH_SUCCESS, payload: data}
}
const errorMsg = (msg) => {
  return {msg, type: constants.ERROR_MSG}
}

// 注册
export function register({user, pwd, repeatpwd, type}) {
  if (!user || !pwd || !type) {
    return errorMsg('用户名密码必须输入')
  }
  if (pwd !== repeatpwd) {
    return errorMsg('密码和确认密码不同')
  }

  return dispatch => {
    axios.post('/user/register', {user, pwd, type})
      .then(res => {
        if (res.status === 200 && res.data.code === 0) {
          dispatch(authSuccess(res.data.data))
        } else {
          dispatch(errorMsg(res.data.msg))
        }
      })
  }
}

// 登录
export function login({user, pwd}) {
  if (!user || !pwd) {
    return errorMsg('用户名密码必须输入')
  }
  return dispatch => {
    axios.post('/user/login', {user, pwd})
      .then(res => {
        if (res.status === 200 && res.data.code === 0) {
          dispatch(authSuccess(res.data.data))
        } else {
          dispatch(errorMsg(res.data.msg))
        }
      })
  }
}

// 登出
export function logoutSubmit() {
  return {type: constants.LOGIN_OUT}
}

// 修改
export function update(data) {
  return dispatch => {
    axios.post('/user/update', data)
      .then(res => {
        if (res.status === 200 && res.data.code === 0) {
          dispatch(authSuccess(res.data.data[0]))
        } else {
          dispatch(errorMsg(res.data.msg))
        }
      })
  }
}
```

```js
// constants.js
export  const AUTH_SUCCESS = 'AUTH_SUCCESS'
export const LOGIN_OUT = 'LOGIN_OUT'
export const ERROR_MSG = 'ERROR_MSG'
export const LOAD_DATA = 'LOAD_DATA'
```

```js
// index.js
import reducer from './_reducer'
import * as actionCreators from './actionCreators'
import * as constants from './constants'

export {reducer, actionCreators, constants}
```

### 完整的 react --> redux --> react 流程

一、Provider组件接受redux的store作为props，然后通过context往下传。

二、

1. connect函数在初始化的时候会将mapDispatchToProps对象绑定到store，

2. 如果mapDispatchToProps是函数则在Connect组件获得store后，根据传入的store.dispatch和action通过bindActionCreators进行绑定，再将返回的对象绑定到store，connect函数会返回一个wrapWithConnect函数，同时wrapWithConnect会被调用且传入一个ui组件，wrapWithConnect内部使用class Connect extends Component定义了一个Connect组件，传入的ui组件就是Connect的子组件，
3. 然后Connect组件会通过context获得store，并通过store.getState获得完整的state对象，将state传入mapStateToProps返回stateProps对象、mapDispatchToProps对象或mapDispatchToProps函数会返回一个dispatchProps对象，stateProps、dispatchProps以及Connect组件的props三者通过Object.assign()，或者mergeProps合并为props传入ui组件。然后在ComponentDidMount中调用store.subscribe，注册了一个回调函数handleChange监听state的变化。

三、

1. 此时ui组件就可以在props中找到actionCreator，当我们调用actionCreator时会自动调用dispatch，在dispatch中会调用getState获取整个state，同时注册一个listener监听state的变化，store将获得的state和action传给combineReducers，
2. combineReducers会将state依据state的key值分别传给子reducer，并将action传给全部子reducer，reducer会被依次执行进行action.type的判断，如果有则返回一个新的state，如果没有则返回默认。
3. combineReducers再次将子reducer返回的单个state进行合并成一个新的完整的state。此时state发生了变化。
4. dispatch在state返回新的值之后会调用所有注册的listener函数其中包括handleChange函数，handleChange函数内部首先调用getState获取新的state值并对新旧两个state进行浅对比，如果相同直接return，如果不同则调用mapStateToProps获取stateProps并将新旧两个stateProps进行浅对比，如果相同，直接return结束，不进行后续操作。
5. 如果不相同则调用this.setState()触发Connect组件的更新，传入ui组件，触发ui组件的更新，此时ui组件获得新的props，react --> redux --> react 的一次流程结束。

**上面的有点复杂，简化版的流程是：**

一、Provider组件接受redux的store作为props，然后通过context往下传。

二、connect函数收到Provider传出的store，然后接受三个参数mapStateToProps，mapDispatchToProps和组件，并将state和actionCreator以props传入组件，这时组件就可以调用actionCreator函数来触发reducer函数返回新的state，connect监听到state变化调用setState更新组件并将新的state传入组件。

connect可以写的非常简洁，mapStateToProps，mapDispatchToProps只不过是传入的回调函数，connect函数在必要的时候会调用它们，名字不是固定的，甚至可以不写名字。

简化版本：

```js
connect(state => state, action)(Component);
```

### redux以及react-redux到底是怎么实现的?

![image.png](https://upload-images.jianshu.io/upload_images/6541235-46e779705e9e9b23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 总结

下图阐述了它们三者之间的工作流程：

![image.png](https://upload-images.jianshu.io/upload_images/6541235-dadc2b5b9f9c6492.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

------

## redux-thunk 中间件 

代码示例:

```js
function createThunkMiddleware(extraArgument) {
	return ({ dispatch , getState }) => next => action=> {
	if (typeof action === ’ function ’){
		return action(dispatch , getState , extraArgument);
	}
		return next(action);
	}
}
const thunk= createThunkMiddleware();
export default thunk;
```

> ​	我们看 redux-thunk 这一串函数中最里层的函数，也就是实际处理每个 action 对象的函数。 首先检查参数 action 的类型，如果是函数类型的话，就执行这个 action 函数，把dispatch 和 getState 作为参数传递进去，否则就调用 next 让下一个中间件继续处理 action,这个处理过程和 redux-thunk 文档中描述的功能一致。 

​	`Redux`的单向数据流是同步操作，驱动 `Redux` 流程的 是 `action` 对象， 每一个 `action`对象被派发到 `Store` 上之后，同步地被分配给所有的 reducer 函数，每个 reducer 都是纯函数，纯函数不产生任何副作用，自然是完成数据操作之后立刻同步返回， reducer 返回的结果又被同步地拿去更新 Store 上的状态数据，更新状态数据的操作会立刻被同步给监听Store 状态改变的函数，从而引发作为视图的 React 组件更新过程。 

​	当我们想要让 Redux 帮忙处理一个异步操作的时候，代码一样也要派发一个 action对象，毕竟 Redux 单向数据流就是由 action 对象驱动的 。 但是这个引发异步操作的action 对象比较特殊，我们叫它们“异步 action 对象” 。
​	前面例子中的 action 构造函数返回的都是一个普通的对象，这个对象包含若干字段，其中必不可少的字段是 type ，但是“异步 action 对象”不是一个普通 JavaScript 对象，而是一个函数 。
​	如果没有 redux-thunk 中间件的存在 这样一个函数类型的 action 对象被派发出来会一路发送到各个 reducer 函数， reducer 函数从这些实际上是函数的 action 对象上是无法获得 type 字段的，所以也做不了什么实质的处理。 

​	不过，有了` redux-thunk `中间件之后，这些 action 对象根本没有机会触及到 reducer函数，在中间件一层就被 redux-thunk 截获 。 

​	`redux-thunk` 的工作是检查 action 对象是不是函数，如果不是函数就放行，完成普通action 对象的生命周期，而如果发现 action 对象是函数，那就执行这个函数，并把 Store的 dispatch 函数和 getState 函数作为参数传递到函数中去，处理过程到此为止，不会让这个异步 action 对象继续往前派发到 reducer 函数 。 

## React中间件机制

​	在 `Redux`框架中，中间件处理的是 action 对象，而派发 action 对象的就是 Store 上的dispatch 函数，之前介绍过通过 dispatch 派发的 action 对象会进入 reducer 。 在 action 对象进入 reducer 之前，会经历中间件的管道 。 

​	在这个中间件管道中，每个中间件都会接收到 action 对象，在处理完毕之后，就会把 action 对象交给下一个中间件来处理，只有所有的中间件都处理完 action 对象之后，在这个中间件管道中，每个中间件都会接收到 action 对象，在处理完毕之后，就会把 action 对象交给下一个中间件来处理，只有所有的中间件都处理完 action 对象之后，才轮到 reducer 来处理 action 对象，然而，如果某个中间件觉得没有必要继续处理这个action 对象了，就不会把 action 对象交给下一个中间件，对这个 action 对象的处理就此中止，也就轮不到 reducer 上场了 。

​	每个中间件必须要定义成一个函数，返回一个接受 next 参数的函数，而这个接受next 参数的函数又返回一个接受 action 参数的函数 。 next 参数本身也是一个函数，中间件调用这个 next 函数通知 Redux 自己的处理工作已经结束 。 	

代码举例:   

```js
// 一个实际上什么事都不做的中间件代码如下：
function doNothingMiddleware{{dispatch, getState)) {
	return function {next) {
		return function {action) {
			return next{action)
     }
	}
}
```

​	以 action 为参数的函数对传人的 action 对象进行处理，因为 JavaScript 支持闭包
( Clousure ），在这个函数里可以访问上面两层函数的参数，所以可以根据需要做很多事
情，包括以下功能：

- 调用 dispatch 派发出一个新 action 对象；
- 调用 getState 获得当前 Redux Store 上的状态；
- 调用 next 告诉 Redux 当前中间件工作完毕，让 Redux 调用下一个中间件；
- 访问 action 对象 action 上的所有数据。
  具有上面这些功能，一个中间件足够获取 Store 上的所有信息，也具有足够能力控制数据的流转 。 

中间件用于扩展 dispatch 函数的功能，多个中间件实际构成了一个处理 action 对象的管道， action 对象被这个管道中所有中间件依次处理过之后，才有机会被 reducer 处理。

# ③ 起步

上面说了react，react-router和redux的知识点。但是怎么样将它们整合起来，搭建一个完整的项目。

1、先引用 react.js，redux，react-router 等基本文件，建议用npm安装，直接在文件中引用。

2、从 react.js，redux，react-router 中引入所需要的对象和方法。

```js
import React, {Component, PropTypes} from 'react';
import ReactDOM, {render} from 'react-dom';
import {Provider, connect} from 'react-redux';
import {createStore, combineReducers, applyMiddleware} from 'redux';
import { Router, Route, Redirect, IndexRoute, browserHistory, hashHistory } from 'react-router';
```

3、根据需求创建顶层ui组件，每个顶层ui组件对应一个页面。

4、创建actionCreators和reducers，并用combineReducers将所有的reducer合并成一个大的reduer。利用createStore创建store并引入combineReducers和applyMiddleware。

5、利用connect将actionCreator，reuder和顶层的ui组件进行关联并返回一个新的组件。

6、利用connect返回的新的组件配合react-router进行路由的部署，返回一个路由组件Router。

7、将Router放入最顶层组件Provider，引入store作为Provider的属性。

8、调用render渲染Provider组件且放入页面的标签中。

可以看到顶层的ui组件其实被套了四层组件，Provider，Router，Route，Connect，这四个组件并不会在视图上改变react，它们只是功能性的。

