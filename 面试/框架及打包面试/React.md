## React

### 1、 React 事件机制，React 16 和 React 17 事件机制的不同

阅读这篇文章即可：[一文吃透 react 事件系统原理](https://juejin.cn/post/6955636911214067720)。

为什么要自定义事件机制？

- 抹平浏览器差异，实现更好的跨平台。
- 避免垃圾回收，React 引入事件池，在事件池中获取或释放事件对象，避免频繁地去创建和销毁。
- 方便事件统一管理和事务机制。

### 2、class component

不排除现在还会有面试官问关于 class component 的问题。

#### 2.1 生命周期

- 初始化阶段。

发生在 `constructor` 中的内容，在 `constructor` 中进行 `state` 、`props` 的初始化，在这个阶段修改 `state`，不会执行更新阶段的生命周期，可以直接对 `state` 赋值。

- 挂载阶段。

```javascript
1. componentWillMount
   发生在 render 函数之前，还没有挂载 Dom
2. render
3. componentDidMount
   发生在 render 函数之后，已经挂载 Dom
```

- 更新阶段。

更新阶段分为由 `state` 更新引起和 `props` 更新引起。

```javascript
props 更新时：
1. componentWillReceiveProps(nextProps,nextState)
   这个生命周期主要为我们提供对 props 发生改变的监听，如果你需要在 props 发生改变后，相应改变组件的一些 state。在这个方法中改变 state 不会二次渲染，而是直接合并 state。
2. shouldComponentUpdate(nextProps,nextState)
   这个生命周期需要返回一个 Boolean 类型的值，判断是否需要更新渲染组件，优化 react 应用的主要手段之一，当返回 false 就不会再向下执行生命周期了，在这个阶段不可以 setState()，会导致循环调用。
3. componentWillUpdate(nextProps,nextState)
   这个生命周期主要是给我们一个时机能够处理一些在 Dom 发生更新之前的事情，如获得 Dom 更新前某些元素的坐标、大小等，在这个阶段不可以 setState()，会导致循环调用。
    **一直到这里 this.props 和 this.state 都还未发生更新**
4. render
5. componentDidUpdate(prevProps, prevState)
   在此时已经完成渲染，Dom 已经发生变化，state 已经发生更新，prevProps、prevState 均为上一个状态的值。

state 更新时（具体同上）
1. shouldComponentUpdate
2. componentWillUpdate
3. render
4. componentDidUpdate
```

- 卸载阶段。

```javascript
1. componentWillUnmount
   在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求或清除在 componentDidMount  中创建的订阅等。componentWillUnmount 中不应调用 setState，因为该组件将永远不会重新渲染。组件实例卸载后，将永远不会再挂载它。
```

在 React 16 中官方已经建议删除以下三个方法，非要使用必须加前缀：`UNSAVE_` 。

```javascript
componentWillMount;
componentWillReceiveProps;
componentWillUpdate;
```

取代这两三个生命周期的以下两个新的。

```javascript
1. static getDerivedStateFromProps(nextProps,nextState)
   在组件实例化、接收到新的 props 、组件状态更新时会被调用
2. getSnapshotBeforeUpdate（prevProps,prevState）
   在这个阶段我们可以拿到上一个状态 Dom 元素的坐标、大小的等相关信息。用于替代旧的生命周期中的 componentWillUpdate。
   该函数的返回值将会作为 componentDidUpdate 的第三个参数出现。
```

需要注意的是，一般都会问为什么要废弃三个生命周期，原因是什么。

#### 2.2 setState 同步还是异步

`setState` 本身代码的执行肯定是同步的，这里的异步是指是多个 state 会合成到一起进行批量更新。 同步还是异步取决于它被调用的环境。

- 如果 `setState` 在 React 能够控制的范围被调用，它就是**异步**的。比如**合成事件处理函数**，**生命周期函数**， 此时会进行批量更新，也就是将状态合并后再进行 DOM 更新。
- 如果 `setState` 在原生 JavaScript 控制的范围被调用，它就是**同步**的。比如原生事件处理函数，定时器回调函数，Ajax 回调函数中，此时 `setState` 被调用后会立即更新 DOM 。

### 3、对函数式编程的理解

这篇文章写的真的太好了，一定要读：[简明 JavaScript 函数式编程——入门篇](https://juejin.cn/post/6844903936378273799)。

总结一下： 函数式编程有两个核心概念。

- **数据不可变（无副作用）：** 它要求你所有的数据都是不可变的，这意味着如果你想修改一个对象，那你应该创建一个新的对象用来修改，而不是修改已有的对象。
- **无状态：** 主要是强调对于一个函数，不管你何时运行，它都应该像第一次运行一样，给定相同的输入，给出相同的输出，完全不依赖外部状态的变化。

纯函数带来的意义。

- **便于测试和优化**：这个意义在实际项目开发中意义非常大，由于纯函数对于相同的输入永远会返回相同的结果，因此我们可以轻松断言函数的执行结果，同时也可以保证函数的优化不会影响其他代码的执行。
- **可缓存性**：因为相同的输入总是可以返回相同的输出，因此，我们可以提前缓存函数的执行结果。
- **更少的 Bug**：使用纯函数意味着你的函数中**不存在指向不明的 this，不存在对全局变量的引用，不存在对参数的修改**，这些共享状态往往是绝大多数 bug 的源头。

### 4、react hooks

现在应该大多数面试官会问 hooks 相关的啦。这里我强烈推荐三篇文章，即使没看过源码，也能比较好地理解一些原理：
[用动画和实战打开 React Hooks（一）：useState 和 useEffect](https://juejin.cn/post/6844904127110053895)
[用动画和实战打开 React Hooks（二）：自定义 Hook 和 useCallback](https://juejin.cn/post/6844904132164190221)
[用动画和实战打开 React Hooks（三）：useReducer 和 useContext](https://juejin.cn/post/6844904149746728973)

#### 4.1 为什么不能在条件语句中写 hook

推荐这篇文章：[我打破了 React Hook 必须按顺序、不能在条件语句中调用的枷锁](https://juejin.cn/post/6939766434159394830)。

hook 在每次渲染时的查找是根据一个“全局”的下标对链表进行查找的，如果放在条件语句中使用，有一定几率会造成拿到的状态出现错乱。

#### 4.2 HOC 和 hook 的区别

hoc 能复用**逻辑和视图**，hook 只能复用**逻辑**。

#### 4.3 useEffect 和 useLayoutEffect 区别

对于 React 的函数组件来说，其更新过程大致分为以下步骤：

1. 因为某个事件 `state` 发生变化。
2. React 内部更新 `state` 变量。
3. React 处理更新组件中 return 出来的 DOM 节点（进行一系列 dom diff 、调度等流程）。
4. 将更新过后的 DOM 数据绘制到浏览器中。
5. 用户看到新的页面。

`useEffect` 在第 4 步之后执行，且是异步的，保证了不会阻塞浏览器进程。 `useLayoutEffect` 在第 3 步至第 4 步之间执行，且是同步代码，所以会阻塞后面代码的执行。

#### 4.4 useEffect 依赖为空数组与 componentDidMount 区别

在 `render` 执行之后，`componentDidMount` 会执行，如果在这个生命周期中再一次 `setState` ，会导致再次 `render` ，返回了新的值，浏览器只会渲染第二次 `render` 返回的值，这样可以避免闪屏。

但是 `useEffect` 是在真实的 DOM 渲染之后才会去执行，这会造成两次 `render` ，有可能会闪屏。

实际上 `useLayoutEffect` 会更接近 `componentDidMount` 的表现，它们都同步执行且会阻碍真实的 DOM 渲染的。

#### 4.5 React.memo() 和 React.useMemo() 的区别

- `memo` 是一个高阶组件，默认情况下会对 `props` 进行浅比较，如果相等不会重新渲染。多数情况下我们比较的都是引用类型，浅比较就会失效，所以我们可以传入第二个参数手动控制。
- `useMemo` 返回的是一个缓存值，只有依赖发生变化时才会去重新执行作为第一个参数的函数，需要记住的是，`useMemo` 是在 `render` 阶段执行的，所以不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴。

#### 4.6 React.useCallback() 和 React.useMemo() 的区别

- `useCallback` 可缓存函数，其实就是避免每次重新渲染后都去重新执行一个新的函数。
- `useMemo` 可缓存值。

有很多时候，我们在 `useEffect` 中使用某个定义的外部函数，是要添加到 `deps` 数组中的，如果不用 `useCallback` 缓存，这个函数在每次重新渲染时都是一个完全新的函数，也就是引用地址发生了变化，这就会导致 `useEffect` 总会无意义的执行。

#### 4.7 React.forwardRef 是什么及其作用

这里还是阅读官方文档来的清晰：[React.forwardRef](https://link.juejin.cn/?target=https%3A%2F%2Fzh-hans.reactjs.org%2Fdocs%2Freact-api.html%23reactforwardref)。 一般在父组件要拿到子组件的某个实际的 DOM 元素时会用到。

### 5、react hooks 与 class 组件对比

[react hooks 与 class 组件对比](https://juejin.cn/post/6844904179136200712) [函数式组件与类组件有何不同](https://link.juejin.cn/?target=https%3A%2F%2Foverreacted.io%2Fzh-hans%2Fhow-are-function-components-different-from-classes%2F)

### 6、介绍 React dom diff 算法

[让虚拟 DOM 和 DOM-diff 不再成为你的绊脚石](https://juejin.cn/post/6844903806132568072)。

### 7、对 React Fiber 的理解

关于这块儿我觉得可以好好阅读下这篇无敌的博客了：[Build your own React](https://link.juejin.cn/?target=https%3A%2F%2Fpomb.us%2Fbuild-your-own-react%2F)。 它可以教你一步步实现一个简单的基于 React Fiber 的 React，可以学到很多 React 的设计思想，毕竟为了面试我们可能大多数人是没有时间或能力去阅读源码的了。

然后我们再阅读下其它作者对于 React Fiber 的理解，再转化为我们自己的思考总结，以下是推荐文章： [这可能是最通俗的 React Fiber(时间分片) 打开方式](https://juejin.cn/post/6844903975112671239)

### 8、React 性能优化手段

推荐文章：[React 性能优化的 8 种方式了解一下？](https://juejin.cn/post/6844903924302888973)

- 使用 `React.memo` 来缓存组件。
- 使用 `React.useMemo` 缓存大量的计算。
- 避免使用匿名函数。
- 利用 `React.lazy` 和 `React.Suspense` 延迟加载不是立即需要的组件。
- 尽量使用 CSS 而不是强制加载和卸载组件。
- 使用 `React.Fragment` 避免添加额外的 DOM。

### 9、React Redux

[Redux 包教包会（一）：介绍 Redux 三大核心概念](https://juejin.cn/post/6844904021187117069)