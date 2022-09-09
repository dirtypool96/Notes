## Ajax

### Ajax 的概念

在浏览器中，我们可以在不刷新页面的情况下，通过ajax的方式去获取一些新的内容。

Ajax：Asynchronous Javascript And XML（异步 JavaScript 和 XML）。它并不是凭空出现的新技术，而是对于现有技术的结合。Ajax 的核心是 js 对象：**XMLHttpRequest**。


### 发送 Ajax 请求的五个步骤

> 其实也就是 使用 XMLHttpRequest 对象的五个步骤。

我们先回忆一下，一个完整的HTTP请求需要的是：

- 请求的网址、请求方法get/post。

- 提交请求的内容数据、请求主体等。

- 接收响应回来的内容。

发送 Ajax 请求的五个步骤：

（1）创建异步对象。即 XMLHttpRequest 对象。

（2）使用open方法设置请求的参数。open(method, url, async)。参数解释：请求的方法、请求的url、是否异步。

（3）发送请求。

（4）注册事件。 注册onreadystatechange事件，状态改变时就会调用。

如果要在数据完整请求回来的时候才调用，我们需要手动写一些判断的逻辑。

（5）获取返回的数据。

###  Ajax 请求：get 请求举例

（1）index.html：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<h1>Ajax 发送 get 请求</h1>
<input type="button" value="发送get_ajax请求" id='btnAjax'>

<script type="text/javascript">
    // 绑定点击事件
    document.querySelector('#btnAjax').onclick = function () {
        // 发送ajax 请求 需要 五步

        // （1）创建异步对象
        var ajaxObj = new XMLHttpRequest();

        // （2）设置请求的参数。包括：请求的方法、请求的url。
        ajaxObj.open('get', '02-ajax.php');

        // （3）发送请求
        ajaxObj.send();

        //（4）注册事件。 onreadystatechange事件，状态改变时就会调用。
        //如果要在数据完整请求回来的时候才调用，我们需要手动写一些判断的逻辑。
        ajaxObj.onreadystatechange = function () {
            // 为了保证 数据 完整返回，我们一般会判断 两个值
            if (ajaxObj.readyState == 4 && ajaxObj.status == 200) {
                // 如果能够进到这个判断 说明 数据 完美的回来了,并且请求的页面是存在的
                // 5.在注册的事件中 获取 返回的 内容 并修改页面的显示
                console.log('数据返回成功');

                // 数据是保存在 异步对象的 属性中
                console.log(ajaxObj.responseText);

                // 修改页面的显示
                document.querySelector('h1').innerHTML = ajaxObj.responseText;
            }
        }
    }
</script>
</body>
</html>
```

（2）02-ajax.php：

```php
<?php
	echo 'smyhvae';
 ?>
```

效果如下：

![](res/20180228_1605.gif)

### Ajax 请求：post 请求举例

index.html：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<h1>Ajax 发送 get 请求</h1>
<input type="button" value="发送put_ajax请求" id='btnAjax'>
<script type="text/javascript">

    // 异步对象
    var xhr = new XMLHttpRequest();

    // 设置属性
    xhr.open('post', '02.post.php');

    // 如果想要使用post提交数据,必须添加此行
    xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");

    // 将数据通过send方法传递
    xhr.send('name=fox&age=18');

    // 发送并接受返回值
    xhr.onreadystatechange = function () {
        // 这步为判断服务器是否正确响应
        if (xhr.readyState == 4 && xhr.status == 200) {
            alert(xhr.responseText);
        }
    };
</script>
</body>
</html>
```

## XMLHttpRequest 对象详解

我们在上一段讲解了使用 XMLHttpRequest 对象的五个步骤。本段，我们讲一下注意事项。

### 发送请求

发送请求的方法：

```javascript
	open(method, url, async);
```

参数解释：

- method：请求的类型；GET 或 POST

- url：文件在服务器上的位置

- async：true（异步）或 false（同步）

另外还有个方法：（仅用于 POST 请求）

```javascript
	send(string);
```

### POST请求时注意

如果想让 像form 表单提交数据那样使用POST请求，就需要使用 XMLHttpRequest 对象的 setRequestHeader()方法 来添加 HTTP 头。然后在 send() 方法中添加想要发送的数据：

```javascript
	xmlhttp.open("POST","ajax_test.php", true);

	xmlhttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded");

	xmlhttp.send("name=smyhvae&age=27");
```

### onreadystatechange 事件

注册 onreadystatechange 事件后，每当 readyState 属性改变时，就会调用 onreadystatechange 函数。

readyState：（存有 XMLHttpRequest 的状态。从 0 到 4 发生变化）

- 0: 请求未初始化

- 1: 服务器连接已建立

- 2: 请求已接收

- 3: 请求处理中

- 4: 请求已完成，且响应已就绪

status：

- 200: "OK"。

- 404: 未找到页面。

在 onreadystatechange 事件中，**当 readyState 等于 4，且状态码为200时，表示响应已就绪**。

### 服务器响应的内容

- responseText：获得字符串形式的响应数据。

- responseXML：获得 XML 形式的响应数据。

如果响应的是普通字符串，就使用responseText；如果响应的是XML，使用responseXML。