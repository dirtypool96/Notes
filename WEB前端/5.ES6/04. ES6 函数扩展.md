## 函数扩展前言

ES6在**函数扩展**方面，新增了很多特性。例如：

- 箭头函数
- 参数默认值
- 参数结构赋值
- 扩展运算符
- rest参数
- this绑定
- 尾调用

## 箭头函数

定义和调用函数：（传统写法）

```js
function fn1(a, b) {
    return a + b;
}

console.log(fn1(1, 2));  //输出结果：3
```

定义和调用函数：（ES6中的写法）

```js
	var fn2 = (a, b) => a + b;

	console.log(fn2(1, 2));  //输出结果：3
```

二者的效果是一样的。

在箭头函数中，如果方法体内有两句话，那就需要在方法体外边加上{}括号。如下：

```js
	var fn2 = (a, b) => {
	    console.log('haha');
	    return a + b;
	};
	console.log(fn2(1, 2));  //输出结果：3
```

从上面的箭头函数中，我们可以很清晰地找到函数名、参数名、方法体。

上方代码中：

- 如果有且仅有1个参数，则`()`可以省略
- 如果方法体内有且仅有1条语句，则`{}`可以省略，但前提是，这条语句必须是 return。

### this的指向

> 箭头函数只是为了让函数写起来更优雅吗？当然不是，还有一个很大的作用是与this的指向有关。

ES5 中，this指向的是函数被调用的对象；而 ES6 的箭头函数中，this指向的是函数被定义时。

简单来说，箭头函数中的this，是不会变的，是永远绑定在当前的环境下。

## 参数默认值

**传统写法**：

```js
    function fn(param) {
        let p = param || 'hello';
        console.log(p);
    }
```

上方代码中，函数体内的写法是：如果 param 不存在，就用 `hello`字符串做兜底。这样写比较啰嗦。

**ES6 写法**：（参数默认值的写法，很简洁）

```js
    function fn(param = 'hello') {
        console.log(param);
    }
```

在 ES6 中定义方法时，我们可以给方法里的参数加一个**默认值**（缺省值）：

- 方法被调用时，如果没有给参数赋值，那就是用默认值；
- 方法被调用时，如果给参数赋值了新的值，那就用新的值。

如下：

```js
	var fn2 = (a, b = 5) => {
	    console.log('haha');
	    return a + b;
	};
	console.log(fn2(1));     //第二个参数使用默认值 5。输出结果：6

	console.log(fn2(1, 8));  //输出结果：9
```

==**提醒1**==：默认值的后面，不能再有**没有默认值的变量**。比如`(a,b,c)`这三个参数，如果我给b设置了默认值，那么就一定要给c设置默认值。

**提醒2**：

我们来看下面这段代码：

```js
    let x = 'smyh';
    function fn(x, y = x) {
        console.log(x, y);
    }
    fn('vae');
```

注意第二行代码，我们给y赋值为`x`，这里的`x`是括号里的第一个参数，并不是第一行代码里定义的`x`。打印结果：`vae vae`。

如果我把第一个参数改一下，改成：

```js
    let x = "smyh";
    function fn(z, y = x) {
        console.log(z, y);
    }
    fn("vae");
```

此时打印结果是：`vae smyh`。

## 扩展运算符

注意区分：

- 扩展运算符的格式为`...`
- rest运算符的格式为`...变量名`

有了ES6，当我们在定义一个方法，但是不确定其参数的个数时，我们就可以用**扩展运算符**作为参数。

以前，我们在定义方法时，参数要确定个数，如下：（程序会报错）

```js
    function fn(a, b, c) {
        console.log(a);
        console.log(b);
        console.log(c);
        console.log(d);
    }

    fn(1, 2, 3);
```

上方代码中，因为方法的参数是三个，但使用时是用到了四个参数，所以会报错。

现在，我们有了扩展运算符，就不用担心报错的问题了。代码可以这样写：

```js
function fn(...arg) {   //当不确定方法的参数时，可以使用扩展运算符
    console.log(arg[0]);
    console.log(arg[1]);
    console.log(arg[2]);
    console.log(arg[3]);
}

fn(1, 2, 3); //方法中定义了四个参数，但只引用了三个参数，ES6 中并不会报错。
```

上方代码中注意，arg参数之后，不能再加别的参数，否则编译报错。

**举例**：数组赋值的问题

我们来分析一段代码：（将数组 arr1 赋值给 arr2）

```js
	let arr1 = ['www', 'smyhvae', 'com'];
	let arr2 = arr1;          // 将 arr1 赋值给 arr2，其实是让 arr2 指向 arr1 的内存地址
	console.log('arr1:' + arr1);
	console.log('arr2:' + arr2);
	console.log('---------------------');

	arr2.push('你懂得');  //往arr2 里添加一部分内容
	console.log('arr1:' + arr1);
	console.log('arr2:' + arr2);
```

上方代码中，我们往往 arr2 里添加了`你懂的`，却发现，arr1 里也有这个内容。原因是：`let arr2 = arr1;`其实是让 arr2 指向 arr1 的地址。也就是说，二者指向的是同一个内存地址。

如果不想让 arr1 和 arr2 指向同一个内存地址，我们可以借助扩展运算符来做：

```js
	let arr1 = ['www', 'smyhvae', 'com'];
	let arr2 = [...arr1];  //arr2 会重新开辟内存地址
	console.log('arr1:' + arr1);
	console.log('arr2:' + arr2);
	console.log('---------------------');

	arr2.push('你懂得');  //往arr2 里添加一部分内容
	console.log('arr1:' + arr1);
	console.log('arr2:' + arr2);
```

我们明白了这个例子，就可以避免开发中的很多业务逻辑上的 bug。

## rest 运算符

`rest` 在英文中指的是**剩余部分**（不是指休息）。我们来举个例子，理解剩余部分的含义：

```
	function fn(first, second, ...arg) {
	    console.log(arg.length);
	}

	fn(0, 1, 2, 3, 4, 5, 6);  //调用函数后，输出结果为 5
```

上方代码的输出结果为 5。 调用`fn()`时，里面有七个参数，而`arg`指的是剩下的部分（因为除去了`first`和`second`）。

从上方例子中可以看出，`rest`运算符适用于：知道前面的一部分参数的数量，但对于后面剩余的参数数量未知的情况。

## 模块化

**模块化的意义**：

比如说，当我需要用到jQuery库时，我会把jQuery.js文件引入到我自己代码的最前面；当我需要用到vue框架时，我会把vue.js文件引入到我自己代码的最前面。

可是，如果有20个这样的文件，就会产生20次http请求。这种做法的性能，肯定是不能接受的。

如果把20个文件直接写在一个文件里，肯定是不方便**维护**的。可如果写成20个文件，这20个文件又不好排序。这就是一个很矛盾的事情，于是，模块化就诞生了。

**模块化历程**：commonJS、AMD规范（RequireJS）、CMD规范（SeaJS）；import & export

### export：

> 一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。

**静态化**：必须在顶部（如果处于块级作用域内，就会报错），不能使用条件语句，自动采用严格模式。（静态化有利于性能以及代码的稳定性）

`export`语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。

```javascript
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);
```

上面代码输出变量`foo`，值为`bar`，500 毫秒之后变成`baz`。

```javascript
// 写法三
var n = 1;
export {n as m};
```

### import:

使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过`import`命令加载这个模块。

```javascript
import { lastName as surname } from './profile.js';
```

`import`命令==输入的变量都是只读==的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里面，改写接口。

```javascript
import {a} from './xxx.js'

a = {}; // Syntax Error : 'a' is read-only;
```

但是，如果`a`是一个==对象==，改写`a`的属性是允许的。（**这种写法很难查错**）

```javascript
import {a} from './xxx.js'

a.foo = 'hello'; // 合法操作
```

### 整体加载 和 复合写法

用星号（`*`）指定一个对象，所有输出值都加载在这个对象上面

```javascript
// circle.js

export function area(radius) {
  return Math.PI * radius * radius;
}

export function circumference(radius) {
  return 2 * Math.PI * radius;
}
```

```javascript
// main.js

import * as circle from './circle';

console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```
#### 复合写法

```javascript
export { foo, bar } from 'my_module';

// 可以简单理解为
import { foo, bar } from 'my_module';
export { foo, bar };
//foo和bar实际上并没有被导入当前模块，只是相当于对外转发了这两个接口
```