---
title: 'GoLang'
draft: false
cover: "images/covers/miku.webp"
categories: ["GoLang"]
date: 2026-05-04T11:00:00+08:00
lastmod: 2026-05-03T11:00:00+08:00
---



# GOLANG

GO会自动帮你把没用到的东西报错、删除，主打一个简洁。



## 标准开局:

```go
package main

import (
	"fmt"
)

func main(){		//主函数
    fmt.printkn("Hello world")
}
```

##### -------------------------------------------------------------------------------------------------------

- ## 变量的定义:

可使用const \ var  \:=定义 ,可以用( )多个定义，可以不用输入数据类型让机器自行判断（也可以输）

```go
const(  
	v1 = iota //此时iota = 0
	_        //可以使用 _ 下划线跳过某些值
	v5 = 100 //中间设置新变量也会跳
	v2 = iota //此时iota = 3
)

var number int = 114
num := int 514
num2 := 5461313
```

使用 := 短声明符号可以便捷地定义并赋值，让机器来默认数据类型

定义结构体变量如下:

```go
type books struct {
		Name    string
		Address string
	}

hundred_alone := books{"hundred_alone", "191810"}

fmt.Println(hundred_alone)                             //OK but have { }
fmt.Println(hundred_alone.Name, hundred_alone.Address) //OK

```

对于结构体变量的输出多试几次即可

对了 赋值时可以在右侧运算 与c相同

```go
r := c[2] > 0
```

同时 可以使用 "_"来接收数据（比如函数中的），而这个数据会被抛弃，这个下划线就是匿名变量

此外，"_"还可以在编译时检查接口的实现，这里不做详细介绍。

-------------------------------------------------------------------------------------------------------

- ## 数据类型

指针：*int(或其他数据类型,后面主要用int举例子)
数组：b :=  [...]int{1,2,3}  / c:= [4]uint8{1,2,3}   /  var d = [3]int{1,2,3}
布尔：var b bool = true / c := false
一般整形：int
浮点型：float32/64

```
//unitx  无符号的x为整形(0 ~ 2^x)
//intx  有符号的x位整形 x = 32 时 -> +-2147483647
//还有float32 /64 只有这两个类型 没有float与double类型
//complex64/128 -> 32/64位实数与虚数  虚数部分用 + xi（和数学一样）就行
//unitptr 无符号整形 用于存放一个指针
//八进制0开头 16进制0x开头  占位符%b表示二进制
//byte ASCII码中的字符
```

-------------------------------------------------------------------------------------------------------

- ## 条件语句

注意：Go的条件语句有个很申必的地方：if else不能随便换行

```go
if c[2] >= 18 { //没有括号 当然你加了你会给你删掉
		fmt.Printf(" 2 > 18 bro")
	} else if c[1] >= 18 {
		fmt.Printf(" 1 > 18 bro")
	} else { //很神奇但是else不能换行
		fmt.Printf(" 丨😠\n") //emoji can output!
	}
```

-------------------------------------------------------------------------------------------------------

- ## 循环语句

下面是非常经典的for循环，相比C没有括号。而在循环中break和continue的用法和C一样

```go
var i int = 0
	j := 0
	for i = 1; i < 100; i++ { //不能加括号！
		j++
	}
```

除了for循环还有dowhile循环，只不过依然是for为载体

```go
var sum int //dowhile模式
	for {
		sum += i
		i++
		if i > 150 {
			break
		} //注意此时i = 100 ,for里面的循环对i有影响
	}
fmt.Println("sum: ", sum)

```

此时要注意，经过第一个for循环后再经 过dowhile时，i = 100,因为for的循环条件中的i会改变

###### 循环语句的特殊用法:

可以用range将数组与切片或者map打印出来,关于map在下方

```go
var list = map[string]string{
		"001": "xixi",
		"010": "haha",
	}
	for index, data := range list { //若是某个数据不用可以用下划线_来代替
		fmt.Println(index, data)
	}
```

-------------------------------------------------------------------------------------------------------

- ## 关于数组、切片和字符串

①可以用make函数创造指定长度的数组

```go
		namelist2 := make([]int, 5)
		namelist2[0] = 3
		namelist2[1] = 2
```

这边的输出会显示 len = 5,其他则默认为0

②可以直接用上文方法定义或用单引号内多个元素定义:

```go
s4 := `"line1"
		"line2"
	`
```

③对于字符串，可以直接使用多个字符串相加得到

```go
		s1 := "hello"
		s2 := "world"
		s3 := s1 + s2 + s4
```

④而也可以创建可以自由增加长度的数组（或是切片？反正那个意思）

```go
		nameLIST := []string{} //[]中不填数字即可
		nameLIST = append(nameLIST, "awd", "awd2")
		nameLIST = append(nameLIST, "wada", "wada2")
		fmt.Println(nameLIST[2])
```

⑤关于切片:

```go
	fmt.Println(namelist2[1:3])
```

拿一个变量等于切片也行，切片的位置如下:

原数组0 a 1 b 2 c 3 d 4 ->0~3即拿取0~3空中的数据(即a,b,c)

-------------------------------------------------------------------------------------------------------

- ## 对于map,是无序的键值对集合

  说人话就是自己弄了个数据与索引的对应集
  
  ```go
  number := map[int]string{ //var number = map[int]string //its OK
  		1: "114",
  		2: "514",
  		4: "1919",
  	}
  fmt.Printf("num[3]:%#v\n", number[3]) //%#v输出
  ```
  
  那么number[1]就是 "114" 其他同理 检索的时候可用
  
  寻找某个键的值：
  
  ```go
  myMap := map[string]string{
      "role": "114514",  
  }
  // 获取值
  value := myMap["role"]
  fmt.Println(value)  // 输出: 114514
  ```

顺便 %#v 可以见以数做输出 

而与接口联动，则有

```go
m := map[string]interface{}{
	"xxx":xxx
}
```

interface{}：值的类型是空接口，可以存放任意类型的值（int、string、struct、slice、map 等）。
就相当于创建了一个“**键是字符串，值什么都能放**”的空 map

-------------------------------------------------------------------------------------------------------

- ## 关于函数

当没有返回值时:
```go
func say(a, b int) { //若两数据类型则可以只用写一个
	fmt.Println("hello world:", a, b)
}
```

当有返回值时，go支持多个返回值

注意：当只有一个返回值时，括号可以省略

```go
func num(a, b int) (int, int) {
	and := a + b
	cha := a - b
	return and, cha
}
```

main中测试：用法与C相近

而有多个返回值时，用多个变量接住即可

```go
	say(5, 6) //用法和c几乎一样
	a, b := num(5, 6) //接住函数的返回值
```

函数里定义函数、函数闭包与函数更多写法:

***①函数里定义函数***

```go
var getnumber = func(num int) int { //此时这里不加括号
		return num
	}
```

这里本质上是将匿名函数赋值给了getnumber,此时getnum =  func( 输入值)(返回值 ){函数体}

***②闭包***

在上面的基础上有闭包，闭包是匿名函数的一个强大特性，它可以**捕获外部函数的变量**，

```go
func counter() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}

func main(){
    c1 := counter()
    fmt.Println(c1())  // 1
    fmt.Println(c1())  // 2
    fmt.Println(c1())  // 3
}
```

此时counter是一个函数，其返回的也是一个函数，即func( ) int{ }

闭包使得语言的垃圾回收机制不会收回a()（或者其他匿名函数）所占用的资源

*在给定函数被多次调用的过程中，这些私有变量能够保持其持久性。变量的作用域仅限于包含它们的函数，因此无法从其它程序代码部分进行访问。不过，变量的生存期是可以很长，在一次函数调用期间所创建所生成的值在下次函数调用时仍然存在。正因为这一特点，闭包可以用来完成信息隐藏，并进而应用于需要状态表达的某些编程范型中。* 

**③结合*map***

fine 结合前面的map可以达成更高阶的函数写法

```go
number := map[string] func() int{
		"a": func() int { return 11 },
		"b": func() int { return 22 },
		"c": func() int { return 33 },
	}

d := number["a"]();
```

通过map即可调用同一函数名下的不同函数，也可以理解为“函数数组”

----------------------------------------------------------------

- ## 关于结构体:

基本语法示例：

```go
type User struct {
	Name     string `json:"name"`
	Age      int    `json:"age"`
	Password string `json:"password"`
}

//JSON标签高级属性举例 omitempty选项指示当字段值为空或其零值时，应省略该字段
type t struct{
    Content string `json:"content,omitempty"` 
    // 当Content为空字符串时，省略该字段
}


func main() {
	User := User{Name: "scqwq", Age: 18, Password: "123456"}
	Data, _ := json.Marshal(User)
	fmt.Println(string(Data))
    
    // 序列化为JSON
data, _ := json.Marshal(user)
fmt.Println(string(data)) // 输出 {"id":1,"username":"Alice"}
}
```

对于json部分，`json.Marshal`是 Go 语言中将 Go 数据结构序列化为 JSON 字符串的标准方法。一般测试情况下不需要加json

Marshal -> 序列化JSON   Ummarshal -> 反序列化

- ## switch

与C的主要区别是没有break；也不会自动往下掉，需要往下运行反倒需要加代码:

```go
switch age { //正常用法  并且没有break也可以
	case 18:
		fmt.Println("u r 18")
		fallthrough //如果需要往下 则需要加这行
	case 19:
		fmt.Println("u r 19")
	default:
		fmt.Println("u r not 18 or 19")
	}
```

此外，switch和case也可当成条件来用

```go
switch { //当然也可以当成条件来用
	case age >= 18:
		fmt.Println("u r adult")
	case age < 18 && age >= 0:
		fmt.Println("u r a kid")
	default:
		fmt.Println("R U born??")
	}
```

- ## input

关于输入，使用fmt.Scan(&x)即可(对的，也需要加&)

```go
var age int
	fmt.Scan(&age)
```

## 方法

方法是带有接收者（Receiver）的函数，语法如下

```go
func (接收者 类型) 方法名(参数列表) 返回值列表 {
    // 方法体
}
```

类型大部分情况下为自定义的类型，定义之后即可使用：
```go
person := Person{Name: "Alice", Age: 20} //先实例化

person.Birthday()  // 调用方法
    fmt.Println(person.Age)  // 输出: 20 
```

而与C相同，非指针接收者不影响原对象，而指针接收者操作的是原对象

##### 结构体中的方法继承：

在golang中，一个类型A中包含一个已经实现I接口的类型B，此时A可以使用接口类型I，给A实现I接口中的方法会把B中实现的接口覆盖

**也就是说，类型A会自动继承类型B的接口实现，但A可以重写（覆盖）B的方法实现。**

原理：方法集规则：

```go
// 类型A的方法集 = A的方法 + 嵌入类型的方法
type RobotDog struct {
    Dog  // 嵌入Dog
}

// RobotDog的方法集包含：
// 1. RobotDog.Speak()  ← 自己的方法
// 2. Dog.Speak()       ← 被覆盖，实际调用RobotDog.Speak()
// 3. Dog.Listen()      ← 继承的方法
```

此时总的方法决定了它实现哪些接口

### 方法集

决定了类型是否实现了某个接口：

## 接口（Interface）

接口是一组方法签名的集合，定义行为而非实现：(Go 采用隐式实现，无需显式声明)

遵循“鸭子准则”：如果一个生物叫得像鸭子，长得像鸭子，那他就是鸭子

```go
// 定义接口
type Animal interface {
    Speak() string
    Move() string
}

```

以下用Dog实现接口
```go
type Dog struct {
    Name string
}

func (d Dog) Speak() string {
    return d.Name + " says: Woof!"
}

func (d Dog) Move() string {
    return d.Name + " runs"
}

func describe(s Speaker) {
    fmt.Printf("(%v, %T)\n", s, s)
}
```

我们通过使用接口类型，把所有会叫会跑的生物当成动物来处理

```go
var s Animal
    
    s = Dog{Name: "Buddy"}
    describe(s)  // (Buddy, main.Dog
s.Speak()//dog says: Woof!
```

##### 结构体中的方法继承

----

---

![image-20260131140651370](/note_images/others/image-20260131140651370.png)

第一段代码是**将函数作为结构体字段** 使用，调用函数时使用d.fn();

第二段代码时创建了通道，可以理解为队列，每调用一次函数都会先进先出，后进后出

###### 另外，对于空接口  interfane{}

不管什么类型，都算实现了空接口,则任意类型的变量都可以用空接口储存
则可用map[string]interface{}{}来实现string键与任意值的搭配

#### http.handler接口

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

##### 要实现的唯一方法：

```go
ServeHTTP(w http.ResponseWriter, r *http.Request)
```

![image-20260220122657648](/note_images/others/image-20260220122657648.png)

###### responsewriter:

主要功能：

设置响应状态码：`w.WriteHeader(statusCode)`

设置响应头：`w.Header().Set("Content-Type", "text/plain")`

写入响应体：`w.Write([]byte("Hello, World"))`或 `fmt.Fprint(w, "...")`

###### request:

表示客户端发起的 **HTTP 请求**

包含的信息有：

请求方法：`r.Method`（如 `"GET"`, `"POST"`）

请求路径：`r.URL.Path`

请求头：`r.Header`

请求体：`r.Body`（比如 POST 提交的数据）

查询参数：`r.URL.Query()`

客户端信息、Cookies 等

# type

作用：

- 定义新的类型别名

-  定义结构体

- 定义接口

- 为现有类型定义方法

- 定义函数类型

  ```go
  //1
  type MyInt int
  type YourInt int  
  //2
  type Person struct {
      Name string
      Age  int
  }
  //3
  type Speaker interface {
      Speak() string
  }
  //4
  type MyInt int
  func (m MyInt) Double() int {
      return int(m * 2)
  }
  //5
  // HandlerFunc是一个接收*Context参数的函数类型
  type HandlerFunc func(*Context)
  // 可以将符合签名的函数赋值给这个类型的变量
  var handler HandlerFunc = func(c *Context) {
      // 处理逻辑
  }
  
  // 作为参数传递
  func Use(middleware HandlerFunc) {
      // ...
  }
  
  ```

  `func(*Context)`
  这是一个函数签名，定义了一个函数类型。
  它表示“一个接受一个参数（类型为*Context指针），并且没有返回值”的函数。
  `type HandlerFunc`
  使用type关键字，为上面这个函数签名创建了一个新类型，名叫HandlerFunc。
  现在，HandlerFunc不再是一个普通的函数签名，而是一个具体的类型，就像int、string或MyStruct一样。

  核心关系：类型 (Type) vs 值 (Value)
  这是理解的关键：

  HandlerFunc是类型。
  符合 func(*Context)签名的任意函数，都可以作为 HandlerFunc类型的值。
  
  # MAKE
  
  ```go
  // 语法：make([]T, length, capacity)
  // length: 初始长度
  // capacity: 容量（可选，默认等于 length）
  
  // 示例：
  s1 := make([]int, 5)      // 长度=5, 容量=5, 值=[0,0,0,0,0]
  s2 := make([]int, 5, 10)  // 长度=5, 容量=10, 值=[0,0,0,0,0]
  s3 := make([]string, 3)   // ["", "", ""]
  
  // 对比直接声明
  var s4 []int            // nil 切片
  s5 := []int{}           // 空切片，非 nil
  s6 := make([]int, 0, 5) // 容量5的空切片
  ```
  
  容量  >= len 长度<容量时其他为空

# ...

```go
func NewStore(keyPairs ...[]byte) Store
```

...[]表示其中可以有任意个byte[]变量

在函数内部，`keyPairs`实际上是一个`[][]byte`类型（字节切片的切片）

# 结构体嵌套

```go
// 被嵌套的结构体
type Engine struct {
    Power int
    Type  string
}

//普通字段
type Car1 struct {
    engine Engine  
    Brand  string
}

// 嵌套结构体
type Car struct {
    Engine  // 匿名字段：嵌套 Engine
    Brand   string
    Model   string
}

//则在赋值时，嵌套结构体需要:
func main(){
myCar := Car{
        Engine: Engine{Power: 150, Type: "V6"},
        Brand:  "Toyota",
    }
}
//因为此时出现了字段提升和方法提升
```

