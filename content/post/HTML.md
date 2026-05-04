---
title: 'HTML'
draft: false
categories: ["前端"]
date: 2026-05-03T11:00:00+08:00
lastmod: 2026-05-03T11:00:00+08:00
---



# **HTML：**

## 标签:

标签列表功能参考:https://www.w3cschool.cn/htmltags/html-reference.html

双标签：用于有内容的元素

单标签：用于没内容的元素



#### 开头:

```html
<!DOCTYPE html>  //告诉浏览器这是一个HTML文件
<html>  //文档起始点 or 最外层容器
    <head>   //表示文档头部 包含一些文件原信息(如标题 编码格式)
        <title>Document</title> //标题
        <meta charset="UTF-8">  //编码格式
    	<meta name="viewport" content="width=device-width, initial-scale=1.0">
    	
    </head>
    
    <body>   //body标签对
        //实际显示在浏览器的内容 比如文本图片与链接
    </body>
</html>

```

//多提一嘴 如果你直接在根目录下命令框打code .可以直接在vscode里打开

//同时 直接输入!补齐上述需要标签就行

##### 标题标签与段落标签:

```html
<body>
    <h1>一级标签</h1> //标题标签 h后面可跟1~6
    <h2>二级标签</h2>
    
    <p>
        114514  //段落标签
        <b>加粗标签</b> //Bold == strong
        <i>斜体</i>
        <u>下划线</u>
        <s>删除线</s>
    </p>
</body>
	
```



#####  有序列表&&无序列表

```html
<body>
    <ul>  //ul表示无序列表  在外部显示·
        <li>元素1</li>
        <li>元素2</li>
        <li>元素3</li>
    </ul>
    
    <ol> //ol表示有序列表  在外部显示数字
        <li>元素1</li>
        <li>元素2</li>
        <li>元素3</li>
    </ol>
</body>
```



##### 表格标签

```html
<table border = "1"> //有行有列 border表格边框
    <tr>
    	<th>表格标题 table header</th>  //表头
    </tr>
    
    <tr> //table row 表格行 //table data 表格列
        <td>元素1</td>
        <td>元素2</td>
        <td>元素3</td>
    </tr>
    
     <tr>
    	<td>元素21</td>
        <td>元素22</td>
        <td>元素23</td>
    </tr>
</table>
```

##### 换行分割标签:

```html
<br> 换行标签
<hr> 水平分割线标签
```

有一些元素默认独占一行 有一些可以共用一行 想共用一行换行则br

## 属性:

##### 基本语法 :

<开始标签 属性名 = "value属性值">

每个元素可具有多个不同属性 :**<开始标签 属性名 = "value属性值"  属性名 ="value属性值">**

属性名称不区分大小写但属性值对大小写敏感

大部分元素都有的属性:class id style

class : 为HTML定义一个或多个类名  之后类名从样式文件引入

id:定义元素唯一id

style:规定元素行内样式

##### 相关重要标签:

##### a标签:

```html
<body>
    <a href = "scqwq.lilys.top">  <!a标签常用于创建链接 herf指向链接目标>
        这是一个超链接
    </a>
    <a href = "scqwq.lilys.top" target = "">  <!target定义链接打开方式>
        _self -> 默认值 在当前窗口打开
        _blank -> 新窗口/标签页打开
        _parent -> 父窗口打开
        _top -> 顶层窗口打开
    </a>
    
</body>
```

##### img标签:

```html
<body>
    <img src = "" alt = "该图片无法显示" width = "" height = "">
    //src打开图片的路径  相对绝对路径或URL
    //alt图像无法加载的文本 -> 若图片无法加载则显示alt中内容
    //width height 宽高
</body>

```



## 元素:

块元素(block):通常从新行开始占据整行的宽度,因此会在页面上显示为一块独立的内容块

行元素：如下

行内块元素:css讲

可包含其他块级元素和行内元素

###### 常见的块级元素:(整行宽度)

```
<div> <p> <h1~6> <ul> <ol> <li> <table> <form>等
```

######  行内元素:(不会独占一行)

```
<span> <a> <strong> <em> <img> <br> <input
```

###### 1.※div标签:无任何语义 仅组织内容 创建布局结构

```html
<body>
    <div class="test">
        <a href="">链接1</a>
        <a href="">链接2</a>
        <a href="">链接3</a>
        <a href="">链接4</a>
    </div>

 	
</body>
```

若需要直接创建class 可以div.class

若需要附带id 可以div#ID 会自动帮你补上

###### 2.span标签：缩小版a标签 不会独占一行 没有属性



## 表单:

![image-20260116122117497](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260116122117497.png)

**比如这种就是一个表单** 看得出来应用很丰富

###### form标签:表单的容器 所有元素都放在里面

###### label标签:与form搭配使用 在输入框前表注释(如上图中的账号名)

###### input标签:请输入文本

```html
<body>
    <form action = "">  //action用于提交时候提交到的地方的URL
        <label for = "username">  用户名: </label>
        <input type = "text" id = "username" placeholder = "请输入文本"> 
        type 决定input数据的类型
        placeholder 空白时文本框显示的内容(如上图中的请输入)
        value 自动填写 需要手动删除 填写后不会消失
        
    </form>
    
</body>
```

对于input的type的属性:

text:什么都行
radio :单选/多选框模式 需要多次input type = radio显示不同的选项 
若需要变成单选，则只需将属性name设置为相同的变量即可( 如三个都是name = gender)
password:让输入变得不可看
checkbox:和radio类似，但是选择框为方框（多选），使用相同name同样可以多选
submit:提交 value:改变显示内容

对于其他属性:

action: 用于提交时候提交到的地方的URL，此时需要后端提供给我们的API

for:用于关联标签与其对应的表单控件,与label搭配使用,如上则点击“用户名”时跳转到id相同的即用户名输入界面,后面可以下拉选择框。