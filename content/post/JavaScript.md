# JavaScript

## 1.导入方式:

### 	同样有三种:

​	1.头部添加 👇

​	2.body添加 👇

```html
<script>   </script>
```

​	3.外部添加,设根目录下的JS文件夹下有一个data.js文件: (路径为相对路径)

```html
<script src = "./JS/data.js"> </script>
```

## 2.变量、数据类型、控制语句

```html
<script>
        var x;            //var具有函数作用域
        let y = 5;			//let的变量具有块级作用域 let更安全灵活 尽量使用let
        const PI = 3.14;
    	console.log(x,y,114514);//数字可以直接输出，文字不行，文字可定义变量
    	let name = "scqwq"
        console.log(name);
    	
    	let i = 0;
    	if(i < 0){//条件语句 
            console("i < 0");
        }	else if(i > 0){
            console("i > 0");
        } 	else {
            console.log("i = 0");
        }
    
    	for(i = 0;i < 5;i++){		//循环语句
            console.log("i = ",i);
        }
    //for与while循环都与C同理
    
</script>
```

## 3.函数

```javascript
function name(参数1,参数2){
	
	return xx;//可选
}
```

参数前不需要加类型声明

需要改变原变量推荐将原变量作为数字之类的储存，之后传参可理解为传进了数组的指针

## ※4.事件

能与用户交互做出相应的重要部分,常用的如下图：

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260117104859425.png" alt="image-20260117104859425" style="zoom:33%;" />

###### 通过事件属性和函数调用实现交互功能如下：

```html
<body>
    <button onclick = "click_event()">点击事件</button>
    <input type= "text" onfocus="focus_event()" onblur="blur_event()">

    <script>
        function click_event(){
            alert("点击事件触发了");
        }
        function focus_event(){
            console.log("获取焦点");
        }
        function blur_event(){
            console.log("失去焦点");
        }
    </script>

</body>
```

## 5.DOM

网页被加载时，浏览器会创建页面的文档对象模型，也就是DOM

DOM为文档树提供了一个变成接口，开发者可以使用JS来操作这个树状结构

已知：

```javascript
let element_id = document.getElementById('box1');
        console.log(element_id);

        var element_class = document.getElementsByClassName('box2')[0];
        console.log(element_class);

        let element_tag = document.getElementsByTagName('div')[2];
        console.log(element_tag);
```

修改其中的值，可以有：

```javascript
		element_id.innerHTML = "修改后的ID选择器";
        element_class.innerHTML = "修改后的class选择器";
        element_tag.innerHTML = "修改后的tag选择器";
```

需要在其中添加标签，可以有：

```javascript
		element_id.innerHTML = '<a href = "#">修改后的ID选择器</a>';
        element_class.innerText = '<a href = "#">修改后的class选择器</a>';
		element_tag.style.fontSize = '20px';
		element_tag.style.color = 'red';
```

注意：innerHTML可以把其中的标签作用出来（比如链接、标题），
但是Text会把其中内容当作纯文本处理

###### 以下为用DOM属性绑定事件：

```javascript
		//DOM属性绑定事件
        let button_element = document.getElementsByTagName('button')[0];
        console.log(button_element);

        button_element.onclick = function(){
            alert("DOM属性按键触发");
        }//匿名函数

        button_element.addEventListener('click',function(){
            alert("通过addEventListener触发");
        })
```

其中的function为匿名函数，因为只用一次，所以仅起到形式的作用

如上，可使用onclick监听事件，也可以使用add...监听事件

# 最后：

做个表格的增删查改检验一下吧
