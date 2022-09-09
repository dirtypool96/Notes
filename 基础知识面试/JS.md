## 何为变量提升

1. var let const 区别

   - var有变量提升 （如果没有赋值，但使用了，会先把这个变量定义为undefined）

   - let const 有块级作用域 （var在for循环定义的变量，在外部也能访问）

   - const 定义的是常量（引用类型可以改）

2. 强制类型转换和隐式类型转换
   - 强制： parseInt parseFloat toString等
   - 隐式：if、逻辑运算、==、+ 拼接字符串

## 手写深度比较 `loadash` 的 `isEqual`

使用递归，原理和手写深拷贝相同

```js
// 判断是否是对象或数组
function isObject(obj) {
  return typeof obj === 'object' && obj !== null
}
// 全相等（深度）
function isEqual(obj1, obj2) {
  if (!isObject(obj1) || !isObject(obj2)) {
    // 值类型（注意，参与 equal 的一般不会是函数）
    return obj1 === obj2
  }
  if (obj1 === obj2) {
    return true
  }
  // 两个都是对象或数组，而且不相等
  // 1. 先取出 obj1 和 obj2 的 keys ，比较个数
  const obj1Keys = Object.keys(obj1)
  const obj2Keys = Object.keys(obj2)
  if (obj1Keys.length !== obj2Keys.length) {
    return false
  }
  // 2. 以 obj1 为基准，和 obj2 一次递归比较
  for (let key in obj1) {
    // 比较当前 key 的 val —— 递归！！！
    const res = isEqual(obj1[key], obj2[key])
    if (!res) {
      return false
    }
  }
  // 3. 全相等
  return true
}
```

## split和join的区别

```js
'1-2-3'.split('-') // [1,2,3]
[1,2,3].join('-') // '1-2-3'
```

## 数组的pop、push、unshift、shift分别是什么

- pop：去除最后一项，并返回，原数组变成去除后最后一项的数组。
- shift：和pop相同
- push：最后插入，返回数组的长度，原数组受影响。
- unshift：最前插入，返回数组的长度，原数组受影响。

**纯函数API**：不改变原数组，返回一个新数组

1. concat：最后插入，返回新的数组
2. map：arr.map((num) => num+1 ) ，返回新数组
3. filter：arr.filter((num) => num>1 )
4. slice：返回新数组（不受原数组影响）

## 数组slice和splice区别

1. slice 加key则去除key对应的值后，返回新数组
2. splice 返回剪去的值为一个数组，原数组变成把后面的值替换剪去的

注意：splice的key是从0开始的

```js
const arr = [10, 20, 30, 40, 50]

// slice 纯函数
const arr1 = arr.slice()
const arr2 = arr.slice(1, 4) //[20, 30, 50]
const arr3 = arr.slice(2) //[30, 40, 50]
const arr4 = arr.slice(-3) //[30, 40, 50]

// splice 非纯函数
const spliceRes = arr.splice(1, 2, 'a', 'b', 'c') 
//[20, 30] 原数组 [10, "a", "b", "c", 40, 50]
const spliceRes1 = arr.splice(1, 2)
//[20, 30] 原数组 [10, 40, 50]
const spliceRes2 = arr.splice(1, 0, 'a', 'b', 'c')
//只增加，从1（20）开始
//[] 原数组 [10, "a", "b", "c", 20, 30, 40, 50]
```

## [10,20,30].map(parseInt)

等同于

```js
 [10,20,30].map((num,index) => {
   return parseInt(num,index)
   //parseInt(10,0) 第二个参数为0 等同于10，返回10
   //parseInt(20,1) 第二个参数为1 不符合进制规则（2-36），返回NaN
   //parseInt(30,2) 第二个参数为2 但是3>2，返回NaN
 })
```

## ajax请求get和post的区别

- get 一般用于查询，post一般用于提交
- get 参数拼接在url上，post放在请求体内（数据体积可更大）
- 安全性：post易于防止CSRF

## 闭包是什么？有何特性？有何影响

- 闭包一般用于隐藏数据，需注意自由变量（它的查找要在函数**定义**的地方）

- 特性：函数作为参数传入，或者函数作为参数被返回

- 影响：变量会常驻内存，得不到释放，闭包不要乱用

## JSONP原理，不是Ajax

利用 `<script>` 标签没有跨域限制的漏洞，网页可以得到从其他来源动态产生的 JSON 数据。JSONP请求一定需要对方的服务器做支持才可以。

AJAX属于同源策略，JSONP属于非同源策略（跨域请求）

定义一个全局回调函数，然后去访问一个JS，JS返回的是这个函数的执行，再调用函数，对数据进行操作

## document load和 ready的区别

也就是load和DOMContentLoaded（ready）的区别

DOMContentLoaded DOM渲染完就可执行，load要等全部资源加载完

## 函数声明和函数表达式的区别

函数声明 function fn() {} 

函数表达式 const fn = function() {}

1. 函数声明会在执行前预加载（类似var的变量提升，但不会为undefined）

## new Object() 和 Object.create() 区别

- 字面量 {} 等同于new Object()，原型Object.prototype
- Object.create(null) 没有原型
- Object.create({...}) 可以指定原型

```js
const obj1 = {
  a: 10,
  b: 20,
  sum() {
      return this.a + this.b
  }
}

const obj2 = new Object({
  a: 10,
  b: 20,
  sum() {
      return this.a + this.b
  }
})

const obj21 = new Object(obj1) // obj1 === obj2
const obj3 = Object.create(null) //没有__proto__
const obj4 = new Object() // {}

//create实际是创建一个空对象，把这个空对象的原型指向传入的对象
const obj5 = Object.create({
    a: 10,
    b: 20,
    sum() {
        return this.a + this.b
    }
})
//{} 但 __proto__为{a: 10,b: 20,sum() {return this.a + this.b}}
const obj6 = Object.create(obj1)
//{} 有__proto__（为obj1），obj6的原型指向obj1(obj6.__proto__ === obj1)
```

## 作用域的应用

1. 当`setTimeout()`的毫秒数设置为0的时候，仍然是先执行完函数调用栈中的代码，然后立即调用定时器，定时器都被放在队列的数据结构中，只有等上下文的执行完后才运行。

   所以，只有将for循环中的i独立出来形成块级作用域，每次for循环都是一个独立的作用域

```js
let i
for (i=1;i<=3;i++){
    setTimeout(()=>{
        console.log(i)
    },0)
}
//4,4,4 
//在定时器的方法执行的时候，因为5个定时器所打印出来的是同一个i变量，变量i已经变成了4，所以输出的全部是4
```

2. 函数定义的时候不看，什么时候执行什么时候看里面的代码

```js
let a=100
function test() {
	console.log(a) //100
	a=10
	console.log(a) //10
}
test()
console.log(a) //10
```

## 字符串 字母开头，后面字母数字下划线，长度6-30

const reg = /^[a-zA-Z]\w{5,29}$/

> ^[a-zA-Z] 字母开头,，\w 字母数字下划线，{5,29}  长度6-30 ，$ 结尾

## 获取最大值

```js
function max() {
	const nums = [...arguments]
	let max = 0
	nums.forEach( (n) => {
		if (n > max) {
			max = n
		}
	})
	return max
}

//或者使用Math.max()
```

## JS 实现继承

- class继承
- prototype继承
- 构造函数继承

## 如何捕获JS程序中的异常

1. try catch finally

2. 自动捕获异常 

   ```js
   window.onerror = function (message,source,linenum,colnum,error) {
     // 1. 对于跨域的js，如CDN，不会有详细的报错信息
     // 2. 对于压缩的js，还要配合sourceMap反查到未压缩代码的行、列
   }
   ```

## 获取当前页面url参数

- location.search

- URLSerachParams

```js
function query(name) {
  const search = location.search
  const p = new URLSearchParams(search)
  return p.get(name)
}
```

## 数组的拍平 flatten（考虑多层级）

```js
function flat(arr) {
  // 验证 arr 中，还有没有深层数组 [1, 2, [3, 4]]
  const isDeep = arr.some(item => item instanceof Array)
  if (!isDeep) {
      return arr // 已经是 flatten [1, 2, 3, 4]
  }
	// concat方法可以拍平只有一层的数组，多层的不行
  const res = Array.prototype.concat.apply([], arr)
  return flat(res) // 递归
}

const res = flat( [1, 2, [3, 4, [10, 20, [100, 200]]], 5] )
console.log(res)
```

## 数组去重

- 遍历元素挨个比较
- ES6的set

```js
// 传统方式
function unique(arr) {
  const res = []
  arr.forEach(item => {
      if (res.indexOf(item) < 0) {
          res.push(item)
      }
  })
  return res
}

// 使用 Set （无序，不能重复）性能更好
function unique(arr) {
  const set = new Set(arr)
  return [...set]
}
```

## RAF requestAnimationFrame

![image-20200221095124200](res/image-20200221095124200.png)

```js
// 3s 把宽度从 100px 变为 640px ，即增加 540px
// 60帧/s ，3s 180 帧 ，每次变化 3px

const $div1 = $('#div1')
let curWidth = 100
const maxWidth = 640

// setTimeout
function animate() {
  curWidth = curWidth + 3
  $div1.css('width', curWidth)
  if (curWidth < maxWidth) {
      setTimeout(animate, 16.7) // 自己控制时间
  }
}
animate()

// RAF
function animate() {
  curWidth = curWidth + 3
  $div1.css('width', curWidth)
  if (curWidth < maxWidth) {
      window.requestAnimationFrame(animate) // 时间不用自己控制
  }
}
animate()
```

##  如何渲染几万条数据并不卡住界面

这道题考察了如何在不卡住页面的情况下渲染数据，也就是说不能一次性将几万条都渲染出来，而应该一次渲染部分 `DOM`，那么就可以通过 `requestAnimationFrame` 来每 `16 ms` 刷新一次

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <ul>控件</ul>
  <script>
    setTimeout(() => {
      // 插入十万条数据
      const total = 100000
      // 一次插入 20 条，如果觉得性能不好就减少
      const once = 20
      // 渲染数据总共需要几次
      const loopCount = total / once
      let countOfRender = 0
      let ul = document.querySelector("ul");
      function add() {
        // 优化性能，插入不会造成回流
        const fragment = document.createDocumentFragment();
        for (let i = 0; i < once; i++) {
          const li = document.createElement("li");
          li.innerText = Math.floor(Math.random() * total);
          fragment.appendChild(li);
        }
        ul.appendChild(fragment);
        countOfRender += 1;
        loop();
      }
      function loop() {
        if (countOfRender < loopCount) {
          window.requestAnimationFrame(add);
        }
      }
      loop();
    }, 0);
  </script>
</body>
</html>
```