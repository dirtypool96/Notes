## HTML

### HTML 基础

#### 文本格式化

1. *abbr* 元素定义缩写或首字母缩略语
2. *address* 元素定义文档或文章的联系信息（作者/拥有者）
3. *bdo* 文本反方向
4. *blockquote* 引用文本
5. *pre* 预格式，里面怎么样就显示什么样
6. *var* 定义数学变量

#### 条件注释

```html
<!--[if IE 8]>
    .... some HTML here ....
<![endif]-->
```

条件注释定义只有 Internet Explorer 执行的 HTML 标签

#### CSS

```HTML
<html>
<head>
<link rel="stylesheet" type="text/css" href="/html/csstest1.css" >
</head>
<body>
<h1>我通过外部样式表进行格式化。</h1>
</body>
</html>
```

链接到外部的样式表（CSS）

#### 链接

1. **href**

   ```HTML
   <a href="http://www.w3school.com.cn/" target="_blank">Visit W3School!</a>
   ```

   *target="_blank"*定义新窗口打开 

   *alt="sun"*定义图片丢失时显示的文本

2. **name**

```HTML
<a name="tips">基本的注意事项 - 有用的提示</a>
```

命名一个锚，然后在同一文档中创建只想改锚的链接

```html
<a href="#tips">有用的提示</a>
```

#### 列表（list）

 - 有序列表（ordered list）- ol / li
 - 无序列表（unordered list）- ul / li
 - 定义列表（definition list）- dl / dt / dd

#### 响应式Web设计

1.  能够以可变尺寸传递网页
2. 对于平板和移动设备是必需的

#### HTML 转换到 XHTML

1. 向每张页面的第一行添加 XHTML <!DOCTYPE>

2. 向每张页面的 html 元素添加 xmlns 属性

3. 把所有元素名改为小写

4. 关闭所有空元素

5. 把所有属性名改为小写

6. 为所有属性值加引号

#### 表单（form）

HTML 表单用于收集用户输入

```html
<form action="action_page.php" method="GET">
```

- 如果表单提交是被动的（比如搜索引擎查询），并且没有敏感信息。当您使用 GET 时，表单数据在页面地址栏中是可见的 

  > GET 最适合少量数据的提交，浏览器会设定容量限制。

- 如果表单正在更新数据，或者包含敏感信息（例如密码）。POST 的安全性更加，因为在页面地址栏中被提交的数据是不可见的。

##### Name 属性

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
First name:<br>
<input type="text" value="Mickey">
<br>
Last name:<br>
<input type="text" name="lastname" value="Mouse">
<br><br>
<input type="submit" value="Submit">
</form> 

<p>如果您点击提交，表单数据会被发送到名为 demo_form.asp 的页面。</p>

<p>first name 不会被提交，因为此 input 元素没有 name 属性。</p>

</body>
</html>
```

