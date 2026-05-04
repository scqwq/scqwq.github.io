---
title: 'CSS'
draft: false
categories: ["前端"]
date: 2026-05-03T11:00:00+08:00
lastmod: 2026-05-03T11:00:00+08:00
---

# CSS

CSS:层叠样式表，用于定义网页样式和布局的样式表语言,与HTML一起构筑Web页面

HTML:结构和内容 CSS:外观和样式

CSS -> 各个元素的颜色、字体、大小、间距、边框、背景 -> 更精确的页面设计

房子:HTML 结构 大纲 位置  CSS:装修



## 1.CSS组成:

通常由选择器、属性和属性值组成，多个规则可以组合在一起,每个属性分号结尾(C++)

```
选择器{
 属性1:属性值1;
 属性2:属性值2;
}
```

例如:p1标签选择器，此时所有p1标签都被选中,此时可以作为样式放在<head>部分

```css
<style>
p1{
    color: blue;
    font-size: 16px;
}
</style>
```

此后在body中该样式可以被调用,例如在body中:

```html
<p1>这是一个应用了css样式的文本</p1>
```

## 2.CSS的导入方式:

1.内联样式:直接放在HTML元素标签中 因为都有style属性(也就是直接写)
2.内联样式表:如上例，在head标签中定义
3.外部样式表:把CSS样式单独放在CSS文件中，再在head中使用另一个标签把这个CSS文件链接进来

优先级:内敛  > 内部 >  外部

1.例如:

```html
<h1 style = "color: red;font-size: 27">
    标题
</h1>
```

2.略

3.例如:
css文件在根目录的相对目录的"CSS_data"文件夹中,名字叫"test.css",则在head中:

```html
<link rel = "stylesheet" herf = "./CSS_data/test.css">
```

补充:link的属性有:

| rel值        | 用途                     | 示例                                                     |
| ------------ | ------------------------ | -------------------------------------------------------- |
| `stylesheet` | **主样式表**，立即应用   | `<link rel="stylesheet" href="main.css">`                |
| `preload`    | 预加载资源，不立即执行   | `<link rel="preload" href="font.woff2" as="font">`       |
| `prefetch`   | 预获取未来可能需要的资源 | `<link rel="prefetch" href="next-page.html">`            |
| `icon`       | 网站图标                 | `<link rel="icon" href="favicon.ico">`                   |
| `canonical`  | 规范URL                  | `<link rel="canonical" href="https://example.com/page">` |

## 3.CSS选择器

​		是CSS的关键部分，允许你针对特定元素或一组元素定义样式

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260116163921752.png" alt="image-20260116163921752" style="zoom: 33%;" />

优先级: ID > 类 > 标签名 

1.元素选择器上面就是，此处略

2.类选择器,使用.xxxx,例如:

```html
.highlight{
	background-color: yellow;
}
--------
<p2 class = "highlight">114514</p2>
```

3.ID选择器,和类选择器类似，但是使用的是#,如:

```html
#bluelight{
	background-color: yellow;
}
--------
<p2 id = "bluelight">114514</p2>
```

4.通用选择器：选择所有元素，一个*即可

```html
*{
font-famili:'kaiTi';
}
```

5.子元素选择器:选择父元素内的子元素
例如:已知 div< class = father ,p class = son>

```html
.father > .son{
	font-weight: bolder;
}
```

6.后代选择器：在5基础上div中还有一个div<>，有一个p2 class = grandson在第二个div中(更深一些)

```html
.father p2{
	color:brown;
}
```

7.兄弟选择器：会选择在我们选择元素的紧跟着的下一个某个元素的标签,即此时显示"3"变为红色

```html
h3 + p{
	color : red;
}
-----------
<p>1</p>
<h3>2</h3>
<p>3</p>
```

8.伪类选择器:选择html文档中具有特定状态或位置的元素

```html
#element:hover{
 coler:purple;
}

-------------
<h id = "element">鼠标放上来会显示紫色</h>
```

PS:伪元素选择器: 尝使用双冒号开头,如::after

## 4.CSS常用属性

①复合属性:可以通过一个属性设置多个样式,如:

```html
<hi style = "font: bolder 50px 'kaiTi'>
    
</hi>
```

即不需要写类似于:font-family等多次定义，一个font即可做到,内联样式表同理，
注意颜色需要额外定义color,不能直接改变字体颜色

②关于行内块元素：典型的就是img标签

###### ③常用属性:

- line-height:40px ; 设置行高
- width height:200px 100px用于给块元素/行内块元素定义宽高 图片被可以拉伸
- display属性:使元素属性(行、块、行内块)相互转化:display:inline block inline-block

## 5.CSS盒子模型

CSS盒子模型为CSS中很常用的模型

相关属性:(以下全是复合属性)

内容content:包含的实际内容，文本图片等

内边距Padding:围绕在内容内壁，内容与边框的空间

边框Border:内边距的外部，是盒子的边界

外边距Margin:围绕在边框的外部，盒子和其他元素的空间

例:

```html
<style>
	.demo{
            background-color: aqua;			//颜色支持RGB与16进制
            display: inline-block;
        	border: 5px solid yellowgreen;  
//soild实线 dashed虚线 dotted点线 double双实线 当然也可以border-style:xx border-width:xx
//width可以输入至多四个数字，不清楚代表什么可以一个个试 style、color之类的也可以
    		border-left:10px solid yellow;  //单独一边的边框也可以
        
        padding:50px; //内边距
        margin:40px;	//外边距
        
        }
</style>
```

## 6.浮动

布局方式:1.标准流：按照元素书写顺序依次排列 **2.浮动** 3.定位 4.自适应布局

->本质都是摆盒子

注意:浮动是相对父元素进行浮动，只会在父元素的内部移动。

{float: left：左浮动 right：右浮动 none：不浮动  }

特性:1.脱离标准流  2.一行显示，顶部对齐  3.具备行内块元素的特性

```html
<style>
     .father{
            background-color: aqua;
            /*height: 200px;可以设置高度*/
            overflow: hidden;/*在不设置高度的情况下清除浮动*/
        }
        .leftson{
            width: 100px;
            height: 100px;
            background-color: red;
            float: left;
        }
        .rightson{
            width: 100px;
            height: 100px;
            background-color: yellow;
            float: right;
        }
</style>
```

浮动清除方法还有伪元素清除法，不多赘述，以下为body测试部分:

```html
<body>
    <div class="father">
        <div class="leftson">左浮动</div>
        <div class="rightson">右浮动</div>
        /*多复制几遍测试*/
        <div class="leftson">左浮动</div>
        <div class="leftson">左浮动</div>
        <div class="leftson">左浮动</div>
        <div class="rightson">右浮动</div>
        <div class="rightson">右浮动</div>
    </div>

</body>
```

## 7.定位

承接6.中的布局方式，以下是三种定位方式:

- 相对定位：相对于元素在文档流中的正常位置进行定位（相当于元素存在的位置依然留空）
- 绝对定位：与最近的已经定义的父级元素进行定位，不占据文档流位置(相当于从原文档中把这个元素的位置删除)
- 固定定位：相对于浏览器窗口定位，固定在屏幕上的位置，不随滚动而移动，不占据文档流位置。

```html
 <style>  
        .boxnormal{
            background-color: purple;
            width: 100px;
            height: 100px;

        }

        .box-relative{
            background-color: pink;
            width: 100px;
            height: 100px;
            position: relative;/*相对定位*/ 
            left: 30px;
            right: 10px;/*距离中心什么方向多少像素*/
            bottom: 40px;/*所以左移要用right，其他同理*/
        }

        .box-absolute{
            background-color: green;
            width: 100px;
            height: 100px;
            position: absolute;/*相对定位*/ 
            left: 120px;/*脱离文本流*/
            margin-bottom: 100px;
        }

        .box-fixed{
            width: 100px;
            height: 100px;
            background-color: brown;
            position: fixed;
            right: 0;
            top:100px;/*固定距离右边0，距离上边100px*/
        }
     
     	.box1{
            height: 50px;
            background-color: aqua;
        }
        .box2{
            height: 50px;
            background-color: yellow;
            margin-bottom: 100px;
        }
    </style>
```

position后:

relative -> 相对定位
absolute -> 绝对定位
fixed -> 固定定位

body测试:

```html
<body>
    <h1>相对定位</h1>
    <div class="box1">
        <div class="box-normal">11</div>
        <div class="box-relative">12</div>
        <div class="box-normal" >13</div>
    </div>
    ----------------
    <h2>绝对定位</h2>
    <div class="box2"> 
        <div class="box-normal">21</div>
        <div class="box-absolute">22</div>
        <div class="box-normal">23</div>
    </div>
    <h3>固定定位</h3>
    <div class=" box-fixed"> </div>
</body>
```

## 8.自适应布局

Flexbox 和 Grid：

更轻松的实现复杂布局，提供更灵活的布局选项，使其在不同设备上更容易适应。





