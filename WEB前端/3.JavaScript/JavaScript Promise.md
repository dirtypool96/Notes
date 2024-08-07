## Promise

### 解释

> 由于JavaScript中，所有代码都是**单线程执行**的，导致JavaScript的所有网络操作，浏览器事件，都必须是**异步执行**。异步执行可以用**回调函数**实现

例子：

```js
// 成功的回调函数
function successCallback(result) {
  console.log("音频文件创建成功: " + result);
}

// 失败的回调函数
function failureCallback(error) {
  console.log("音频文件创建失败: " + error);
}

createAudioFileAsync(audioSettings, successCallback, failureCallback)
```

如果函数 `createAudioFileAsync()` 被重写为返回 Promise 对象的形式，就可以像这样简单地使用：

```js
const promise = createAudioFileAsync(audioSettings); 
promise.then(successCallback, failureCallback);

//可简写为
createAudioFileAsync(audioSettings).then(successCallback, failureCallback);
```

这个称为**异步函数调用**

### 约定

- 在 [本轮 Javascript event loop（事件循环）运行完成](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop#执行至完成) 之前，回调函数是不会被调用的。
- 通过 `then()`添加的回调函数总会被调用，即便它是在异步操作完成之后才被添加的函数。
- 通过多次调用 `then()`，可以添加多个回调函数，它们会按照插入顺序一个接一个独立执行。

因此，Promise 最直接的好处就是**链式调用**（**chaining**）

### 链式调用

连续执行多个异步操作是一个常见的需求，在上一个操作执行成功之后，开始下一个的操作，并带着上一步操作所返回的结果。我们可以通过创造一个 **Promise 链**来实现这种需求。

```js
const promise = doSomething();
const promise2 = promise.then(successCallback, failureCallback);
```

第二个对象（`promise2`）不仅表示 `doSomething()` 函数的完成，也代表了你传入的 `successCallback` 或者 `failureCallback` 的完成，`successCallback` 或 `failureCallback` 有可能返回一个 Promise 对象，从而形成另一个异步操作。这样的话，任何一个 `promise2` 新增的回调函数，都会被依次排在由上一个`successCallback` 或 `failureCallback` 执行后所返回的 Promise 对象的后面。

基本上，每一个 Promise 都代表了链中另一个异步过程的完成。

在过去，要想做多重的异步操作，会导致经典的回调地狱：

```js
doSomething(function(result) {
  doSomethingElse(result, function(newResult) {
    doThirdThing(newResult, function(finalResult) {
      console.log('Got the final result: ' + finalResult);
    }, failureCallback);
  }, failureCallback);
}, failureCallback);
```

通过新的功能方法，我们把回调绑定到被返回的 Promise 上代替以往的做法，形成一个 Promise 链：

```js
doSomething().then(function(result) {
  return doSomethingElse(result);
})
.then(function(newResult) {
  return doThirdThing(newResult);
})
.then(function(finalResult) {
  console.log('Got the final result: ' + finalResult);
})
.catch(failureCallback);
```

then里的参数是可选的，`catch(failureCallback)` 是 `then(null, failureCallback)` 的缩略形式。

如下所示，我们也可以用箭头函数来表示：

```js
doSomething()
.then(result => doSomethingElse(result))
.then(newResult => doThirdThing(newResult))
.then(finalResult => {
  console.log(`Got the final result: ${finalResult}`);
})
.catch(failureCallback);
```

**注意：** **一定要有返回值**，否则，callback 将无法获取上一个 Promise 的结果。(如果使用箭头函数，`() => x` 比 `() => { return x; }` 更简洁一些，但后一种保留 `return` 的写法才支持使用多个语句。）。

#### Catch 的后续链式操作

使用一个 `catch`，这对于在链式操作中抛出一个失败之后，再次进行新的操作很有用。

```js
new Promise((resolve, reject) => {
    console.log('初始化');

    resolve();
})
.then(() => {
    throw new Error('有哪里不对了');
        
    console.log('执行「这个」”');
})
.catch(() => {
    console.log('执行「那个」');
})
.then(() => {
    console.log('执行「这个」，无论前面发生了什么');
});
```

输出结果如下：

```html
初始化
执行“那个”
执行“这个”，无论前面发生了什么
```

**注意：**因为抛出了错误 有哪里不对了，所以前一个 执行「这个」 没有被输出。

### Promise 拒绝事件

当 Promise 被拒绝时，会有下文所述的两个事件之一被派发到全局作用域

1. **rejectionhandled**：

   当 Promise 被拒绝、并且在 `reject` 函数处理该 rejection 之后会派发此事件。

2. **unhandledrejection**：

   当 Promise 被拒绝，但没有提供 `reject` 函数来处理该 rejection 时，会派发此事件。

以上两种情况中，`PromiseRejectionEvent` 事件都有两个属性，一个是 `promise` 属性，该属性指向被驳回的 Promise，另一个是 `reason` 属性，该属性用来说明 Promise 被驳回的原因。该处理都是**全局**的，因此不管源码如何，所有的错误都会在**同一个handler**中被捕捉处理

### 在旧式回调 API 中创建 Promise

理想状态下，所有的异步函数都已经返回 Promise 了。但有一些 API 仍然使用旧方式来传入的成功（或者失败）的回调。典型的例子就是 `setTimeout()` 函数：

```js
setTimeout(() => saySomething("10 seconds passed"), 10000);
```

混用旧式回调和 Promise 可能会造成运行时序问题。如果 `saySomething` 函数失败了，或者包含了编程错误，那就没有办法捕获它了。

最好的做法是，将这些有问题的函数包装起来，留在底层，并且永远不要再直接调用它们：

```js
const wait = ms => new Promise(resolve => setTimeout(resolve, ms));

wait(10000).then(() => saySomething("10 seconds")).catch(failureCallback);
```

### 组合

1. `Promise.resolve()` 和 `Promise.reject()` 是手动创建一个已经 resolve 或者 reject 的 Promise 快捷方法。它们有时很有用。  

2. `Promise.all()` 和 `Promise.race()` 是并行运行异步操作的两个组合式工具。