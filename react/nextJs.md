# next.js 踩坑记录

## getInitialProps

1. 当服务渲染时，getInitialProps将会把数据序列化，就像JSON.stringify。所以确保getInitialProps返回的是一个普通 JS 对象

2. `getInitialProps()`能够在服务的运行，也能够在client运行
    - 当页面初始化加载时，getInitialProps只会加载在服务端。
    - 当页面是用户通过路由（Link组件跳转或 API 方法跳转）跳转时，而不是用户输入网址或刷新来访问的，这时候是纯客户端的行为, 和React行为一致
   
3. `getInitialProps`是优先于 `constructor` 执行的，所以 `getInitialProps` 返回的 `props` 可以在 `constructor` 里初始化成 `state`
 
 > - 注意：getInitialProps将不能使用在子组件中。只能使用在pages页面中。
   - 只有服务端用到的模块放在getInitialProps里，请确保正确的导入了它们，否则会拖慢你的应用速度。
