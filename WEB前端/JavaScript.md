## JavaScript 基础教程

### 更改HTML

#### 改变 HTML 内容

```javascript
<script>
document.getElementById("demo").innerHTML = "Hello JavaScript";
</script>
// “查找” id="demo" 的 HTML 元素，并把元素内容（innerHTML）更改为 "Hello JavaScript"
```

#### 改变 HTML 属性

```javascript
<img id="myImage" border="0" src="/i/eg_bulboff.gif" style="text-align:center;">
<button onclick="document.getElementById('myImage').src='/i/eg_bulbon.gif'">开灯</button>
```

#### 改变 HTML 样式 (CSS)

```javascript
<script>
document.getElementById("demo").style.fontSize = "25px";
</script>
```

#### 隐藏/显示 HTML 元素

```javascript
<script>
document.getElementById("demo").style.display="none";
</script>
// display="block"时显示HTML元素
```

### JS的使用

1. JS脚本可放置于<body> 或 <head> ，把脚本置于<body>  元素的底部，可改善显示速度，因为脚本编译会拖慢显示。

2. 如使用外部脚本：

   ```javascript
   <script src="/js/myScript1.js"></script>
   ```

   同样在<body> 或 <head>放置，`外部脚本不能包含<script>`

   **优势**：已缓存的 JavaScript 文件可加速页面加载

### JS 输出

- 使用 `window.alert()` 写入警告框
- 使用 `document.write()` 写入 HTML 输出
- 使用 `innerHTML` 写入 HTML 元素
- 使用 `console.log()` 写入浏览器控制台

```javascript
// innerHTML 属性是在 HTML 中显示数据的常用方法
document.getElementById("demo").innerHTML = 5 + 6;
// 仅用于测试，使用 document.write() 比较方便,在 HTML 文档完全加载后使用 document.write() 将删除所有已有的 HTML
document.write(5 + 6);
// 使用警告框来显示数据
window.alert(5 + 6);
// 在浏览器中，您可使用 console.log() 方法来显示数据。
console.log(5 + 6);
```

### JS数据类型

JavaScript 从左向右计算表达式。不同的次序会产生不同的结果：

```javascript
var x = 911 + 7 + "Porsche";
// 918Porsche
var x = "Porsche" + 911 + 7;
// Porsche9117
```

空值与 undefined 不是一回事。

```javascript
var car = "";                // 值是 ""，类型是 "string"
```

`null` 的数据类型是对象，Undefined 与 null 的值相等，但类型不相等：

```javascript
typeof undefined              // undefined
typeof null                   // object
null === undefined            // false
null == undefined             // true
```

`typeof` 运算符把数组返回为 "object"，因为在 JavaScript 中数组即对象。

```javascript
typeof {name:'Bill', age:62} // 返回 "object"
typeof [1,2,3,4]             // 返回 "object" (并非 "array"，参见下面的注释)
typeof null                  // 返回 "object"
typeof function myFunc(){}   // 返回 "function"
```

### 事件

| 事件        | 描述                         |
| ----------- | ---------------------------- |
| onchange    | HTML 元素已被改变            |
| onclick     | 用户点击了 HTML 元素         |
| onmouseover | 用户把鼠标移动到 HTML 元素上 |
| onmouseout  | 用户把鼠标移开 HTML 元素     |
| onkeydown   | 用户按下键盘按键             |
| onload      | 浏览器已经完成页面加载       |

### 字符串

| 转义字符 | 结果     |
| -------- | -------- |
| \'       | 等同于 ' |
| \"       | 等同于 " |
| \\\      | 等同于 \ |

```javascript
var x = new String();        // 把 x 声明为 String 对象
var y = new Number();        // 把 y 声明为 Number 对象
var z = new Boolean();       //	把 z 声明为 Boolean 对象
```

**避免字符串、数值或逻辑对象。他们会增加代码的复杂性并降低执行速度。**

`indexOf()` 与 `search()`，是*相等的*，`search()` 方法无法设置第二个开始位置参数。

#### 提取部分字符串

*字符串的计数从0开始，（）中含头不含尾。*

1. slice(*start*, *end*)
   - 参数为负的，字符串的结尾开始计数，`负值位置不适用 IE 8 及其更早版本。`
   - 如果省略第二个参数，则该方法将裁剪字符串的剩余部分：

2. substring(*start*, *end*)

   不同之处在于 substring() 无法接受负的索引。

3. substr(*start*, *length*)

   不同之处在于第二个参数规定被提取部分的*长度*。

#### 替换

如需执行大小写不敏感的替换，请使用正则表达式 /i（大小写不敏感）：

```javascript
str = "Please visit Microsoft!";
var n = str.replace(/MICROSOFT/i, "W3School");
```

如需替换所有匹配，请使用正则表达式的 g 标志（用于全局搜索）：

```javascript
str = "Please visit Microsoft and Microsoft!";
var n = str.replace(/Microsoft/g, "W3School");
```

正则表达式不带引号

#### 把字符串转换为数组

```javascript
var txt = "a,b,c,d,e";   // 字符串
txt.split(",");          // 用逗号分隔
txt.split(" ");          // 用空格分隔
txt.split("|");          // 用竖线分隔
```

- 如果省略分隔符，被返回的数组将包含 index [0] 中的整个字符串。

- 如果分隔符是 ""，被返回的数组将是间隔单个字符的数组：

### 数字

**toString()**的使用方法

1. 进制转换：num.toString(16) `10 -> 16`

2. 以字符串返回数值

   ```javascript
   var x = 123;
   x.toString();            // 从变量 x 返回 123
   (123).toString();        // 从文本 123 返回 123
   (100 + 23).toString();   // 从表达式 100 + 23 返回 123
   ```

#### 全局方法

| 方法         | 描述                                                      |
| :----------- | :-------------------------------------------------------- |
| Number()     | 返回数字，由其参数转换而来。x = true; Number(x); # 返回 1 |
| parseFloat() | 解析其参数并返回浮点数。parseFloat("10.33"); # 返回 10.33 |
| parseInt()   | 解析其参数并返回整数。允许空格，只返回首个数字            |

### 数组

**数组和对象的区别**

1. 在 JavaScript 中，数组使用数字索引。
2. 在 JavaScript 中，对象使用命名索引。
3. 数组是特殊类型的对象，具有数字索引。

**何时使用数组，何时使用对象？**

- JavaScript 不支持关联数组
- 如果希望元素名为*字符串（文本）*则应该使用*对象*。
- 如果希望元素名为*数字*则应该使用*数组*。

`toString()`和`join()`都可以将所有数组元素结合为一个字符串，但是`join()`还可以规定分隔符：`fruits.join(" * ")`

#### Popping 和 Pushing

```javascript
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.pop();              // 从 fruits 删除最后一个元素（"Mango"）
var x = fruits.pop();      // x 的值是 "Mango"
fruits.push("Kiwi");       //  向 fruits 添加一个新元素
var x =  fruits.push("Kiwi");   //  x 的值是 5
fruits.shift();            // 从 fruits 删除第一个元素 "Banana",并把所有其他元素“位移”到更低的索引。
fruits.shift();             // 返回 "Banana"
fruits.unshift("Lemon");    // 向 fruits （在开头）添加新元素 "Lemon"
fruits.unshift("Lemon");    // 返回 5
```

#### Splice  拼接数组

`splice()` 方法可用于向数组添加新项或删除：

```javascript
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.splice(2, 2, "Lemon", "Kiwi");
//变成Banana,Orange,Lemon,Kiwi 返回Apple,Mango

fruits.splice(0, 1);  // 删除 fruits 中的第一个元素
```

#### Concat 合并数组

`concat()` 方法通过合并（连接）现有数组来创建一个新数组：

```javascript
var arr1 = ["Cecilie", "Lone"];
var arr2 = ["Emil", "Tobias", "Linus"];
var arr3 = ["Robin", "Morgan"];
var myChildren = arr1.concat(arr2, arr3);   // 将arr1、arr2 与 arr3 连接在一起
```

####  Slice 裁剪数组

```javascript
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"];
var citrus = fruits.slice(1); 
//slice() 方法创建新数组。它不会从源数组中删除任何元素。
```

#### 数组排序

1. 比值函数

   当 `sort()` 函数比较两个值时，会将值发送到比较函数，并根据所返回的值（负、零或正值）对这些值进行排序。

   ```javascript
   var points = [40, 100, 1, 5, 25, 10];
   points.sort(function(a, b){return a - b}); //升序
   points.sort(function(a, b){return b - a}); //降序
   points.sort(function(a, b){return 0.5 - Math.random()}); //随机
   
   //排序后points[0] 包含最低值，points[points.length-1] 包含最高值
   ```

2. 最大最小

   Math.max.apply 来查找数组中的最高值，Math.min.apply 来查找数组中的最低值：

   ```javascript
   function myArrayMax(arr) {
       return Math.max.apply(null, arr);
   }
   
   function myArrayMin(arr) {
       return Math.min.apply(null, arr);
   }
   ```

3. 排序对象数组

   ```javascript
   var cars = [
   {type:"Volvo", year:2016},
   {type:"Saab", year:2001},
   {type:"BMW", year:2010}];
   
   cars.sort(function(a, b){return a.year - b.year});
   
   cars.sort(function(a, b){
   	  var x = a.type.toLowerCase();
   	  var y = b.type.toLowerCase();
   	  if (x < y) {return -1;}
   	  if (x > y) {return 1;}
   	  return 0;
   });
   ```

#### 数组迭代

**（项目值,项目索引,数组本身）**

1. Array.forEach() 方法为每个数组元素调用一次函数（回调函数）。

   ```javascript
   var txt = "";
   var numbers = [45, 4, 9, 16, 25];
   numbers.forEach(myFunction);
   //只用value，也可以写为function myFunction(value)
   function myFunction(value, index, array) {
     txt = txt + value + "<br>"; 
   }
   
   ```

2. Array.map() 方法通过对每个数组元素执行函数来创建新数组。

   ```javascript
   var numbers2 = numbers1.map(myFunction);
   
   function myFunction(value, index, array) {
     return value * 2;
   }
   //map() 方法不会对没有值的数组元素执行函数。
   //map() 方法不会更改原始数组。
   ```

3. Array.filter() 方法创建一个包含通过测试的数组元素的新数组。

   ```javascript
   var numbers = [45, 4, 9, 16, 25];
   var over18 = numbers.filter(myFunction);
   //用值大于 18 的元素创建一个新数组
   function myFunction(value, index, array) {
     return value > 18;
   }
   ```

4. Array.reduce() 方法在每个数组元素上运行函数，以生成（减少它）单个值。

   ```javascript
   var numbers1 = [45, 4, 9, 16, 25];
   var sum = numbers1.reduce(myFunction);
   //确定数组中所有数字的总和,方法在数组中从左到右工作,另请参见reduceRight（）
   function myFunction(total, value, index, array) {
     return total + value;
   }
   //总数（初始值/先前返回的值）,项目值,项目索引,数组本身
   ```

5. Array.every() 方法检查所有数组值是否通过测试。

   ```javascript
   var numbers = [45, 4, 9, 16, 25];
   var allOver18 = numbers.every(myFunction);//返回的是布尔值
   
   function myFunction(value, index, array) {
     return value > 18;
   }
   ```

6. Array.some() 方法检查某些数组值是否通过了测试

   ```javascript
   var numbers = [45, 4, 9, 16, 25];
   var someOver18 = numbers.some(myFunction);//返回 布尔值
   
   function myFunction(value, index, array) {
     return value > 18;
   }
   ```

7. Array.find() 方法返回通过测试函数的第一个数组元素的值。

   ```javascript
   var numbers = [4, 9, 16, 25, 29];
   var first = numbers.find(myFunction);//数值
   var first = numbers.findIndex(myFunction);//数值的索引
   
   function myFunction(value, index, array) {
     return value > 18;
   }
   ```