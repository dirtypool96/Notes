## JavaScript 对象&函数&DOM

### 对象

*函数参数（parameter）*指的是在函数定义中列出的*名称*。

*函数参数（argument）*指的是传递到函数或由函数接收到的真实*值*。

```javascript
x = findMax(1, 123, 500, 115, 44, 88);
//可直接使用arguments
function findMax() {
    var i;
    var max = -Infinity;
    for (i = 0; i < arguments.length; i++) {
        if (arguments[i] > max) {
            max = arguments[i];
        }
    }
    return max;
}
```

- 参数的改变在函数之外是不可见的。

- 对象属性的改变在函数之外是可见的。

### DOM

通过 HTML DOM，JavaScript 能够访问和改变 HTML 文档的所有元素，HTML DOM 是关于如何获取、更改、添加或删除 HTML 元素的标准。

*HTML DOM* 模型被结构化为*对象树*：

![DOM HTML 树](/res/ct_htmltree.gif)

#### 事件属性和分配事件

```javascript
<button onclick="displayDate()">试一试</button>
```

在上例中，名为 displayDate 的函数会在按钮被点击时执行。

```javascript
document.getElementById("myBtn").onclick = displayDate;
```

在上例中，名为 displayDate 的函数被分配到 id="myBtn" 的 HTML 元素。

##### onload 和 onunload 事件

- onload 事件可用于检测访问者的浏览器类型和浏览器版本，然后基于该信息加载网页的恰当版本。

- onload 和 onunload 事件可用于处理 cookie。

```javascript
<body onload="checkCookies()">
```

##### onchange 事件

onchange 事件经常与输入字段验证结合使用。

```javascript
<input type="text" id="fname" onchange="upperCase()">
    //当用户改变输入字段内容时，会调用 upperCase() 函数。
```

##### onmouseover 和 onmouseout 事件

onmouseover 和 onmouseout 事件可用于当用户将鼠标移至 HTML 元素上或移出时触发某个函数

##### onmousedown, onmouseup 以及 onclick 事件

- onmousedown, onmouseup 以及 onclick 事件构成了完整的鼠标点击事件。

- 首先当鼠标按钮被点击时，onmousedown 事件被触发；然后当鼠标按钮被释放时，onmouseup 事件被触发；最后，当鼠标点击完成后，onclick 事件被触发。
- onmouseover和onmouseout同理 （指针移动到元素上和离开）

```javascript
<div onmousedown="mDown(this)" onmouseup="mUp(this)"
style="background-color:#D94A38;width:90px;height:20px;padding:40px;">
点击鼠标</div>

<script>
function mDown(obj) {
  obj.style.backgroundColor = "#1ec5e5";
  obj.innerHTML = "松开鼠标";
}

function mUp(obj) {
  obj.style.backgroundColor="#D94A38";
  obj.innerHTML="谢谢您";
}
</script>
```

#### 事件监听

```javascript
element.addEventListener(event, function, useCapture);
```

第一个参数是事件的类型（比如 "click" 或 "mousedown"）。

第二个参数是当事件发生时我们需要调用的函数。

第三个参数是布尔值，指定使用事件冒泡还是事件捕获。此参数是可选的。

**注意：**请勿对事件使用 "on" 前缀；请使用 "click" 代替 "onclick"。

##### 传递参数

当传递参数值时，请以参数形式使用调用指定函数的“匿名函数”：

```javascript
element.addEventListener("click", function(){ myFunction(p1, p2); });
```

##### 事件冒泡还是事件捕获？

在 HTML DOM 中有两种事件传播的方法：冒泡和捕获。

- 在冒泡中，最内侧元素的事件会首先被处理，然后是更外侧的：首先处理 <p> 元素的点击事件，然后是 <div> 元素的点击事件。

- 在捕获中，最外侧元素的事件会首先被处理，然后是更内侧的：首先处理 <div> 元素的点击事件，然后是 <p> 元素的点击事件。

在 addEventListener() 方法中，你能够通过使用“useCapture”参数来规定传播类型：

```javascript
addEventListener(event, function, useCapture);
//默认值是 false，将使用冒泡传播，如果该值设置为 true，则事件使用捕获传播。
```

### 节点

#### 创建新HTML元素（节点）

```javascript
<div id="div1">
<p id="p1">这是一个段落。</p>
<p id="p2">这是另一个段落。</p>
</div>

<script>
var para = document.createElement("p");
//这段代码创建了一个新的 <p> 元素：
var node = document.createTextNode("这是新文本。");
//如需向 <p> 元素添加文本，则必须首先创建文本节点
para.appendChild(node);
//然后需要向 <p> 元素追加这个文本节点：
var element = document.getElementById("div1");
//最后需要向已有元素追加这个新元素
element.appendChild(para);
//向这个已有的元素追加新元素

//var child = document.getElementById("p1");
//element.insertBefore(para, child);
//appendChild是为最后一个子，这个是为p1的前一个
</script>
```

#### 删除已有 HTML 元素

```javascript
<div id="div1">
<p id="p1">这是一个段落。</p>
<p id="p2">这是另一个段落。</p>
</div>

<script>
var parent = document.getElementById("div1");
var child = document.getElementById("p1");
//方法一：从父删除子：
parent.removeChild(child);
//方法二：找到你想要删除的子，并利用其 parentNode 属性找到父：
child.parentNode.removeChild(child);
</script>
```

#### 替换 HTML 元素

```javascript
<div id="div1">
<p id="p1">这是一个段落。</p>
<p id="p2">这是另一个段落。</p>
</div>

<script>
var para = document.createElement("p");
var node = document.createTextNode("这是新文本。");
para.appendChild(node);

var parent = document.getElementById("div1");
var child = document.getElementById("p1");
parent.replaceChild(para, child);
</script>
```

### 集合

##### *HTMLCollection*

`getElementsByTagName()` 方法返回 *HTMLCollection* **对象**。

```javascript
//选取文档中的所有 <p> 元素：
var x = document.getElementsByTagName("p");
//如需访问第二个 <p> 元素
y = x[1];
```

改变所有 <p> 元素的背景色：

```javascript
var myCollection = document.getElementsByTagName("p");
var i;
for (i = 0; i < myCollection.length; i++) {
    myCollection[i].style.backgroundColor = "red";
}
```

##### *NodeList* 

*NodeList* 对象与 *HTMLCollection* 对象几乎相同。

1. 如使用 `getElementsByClassName()` 方法，某些（老的）浏览器会返回 NodeList 对象而不是 *HTMLCollection*。

2. 所有浏览器都会为 *childNodes* 属性返回 NodeList 对象。

3. 大多数浏览器会为 `querySelectorAll()` 方法返回 NodeList 对象。

```javascript
//创建所有 <p> 元素的列表
var myNodeList = document.querySelectorAll("p");
y = myNodeList[1];
```

##### HTMLCollection 与 NodeList 的区别

1. HTMLCollection是 HTML 元素的集合，NodeList 是文档节点的集合。
2. 访问 NodeList 项目，只能通过它们的索引号。
3. 只有 NodeList 对象能包含属性节点和文本节点。

