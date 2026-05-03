title: 'GIN'

date: 2026-05-04T11:00:00-07:00
lastmod: 2026-05-04T11:00:00-07:00

# GIN





GIN是一个用go语言编写的Web框架

### 小知识提要

##### 0.基本路线:

1.前端静态页面的制作

2.前端的动态部分的制作和往后端传入的api和结构体

3.后端构建需要接收前端（客户端）请求的结构体



4.构建数据库实例DB，连接数据库

5.用err检查数据库连接的返回值，不为nil则panic，并记得延迟注册关闭defer DB.Close()

6.模型绑定 -> DB.AutoMigrate(&Todo{})   Todo{}是一个结构类型，若手动创建表，则表名一定要和后端的一致

7.r.Static引入静态文件,r.LoadHTMLGlob("template/*")渲染模板

8.写入URL最基本的函数r.GET

9.补全URI参数，并使用c.BindJSON等获取从前端返回的数据，并使用DB.Create、DB.Find等与数据库交互，并使用以下语法：

```go
if err = DB.Create(&todo).Error; err != nil {
    c.JSON(http.StatusOK, gin.H{"error": err.Error()})//错误时候返回信息
} else {
    c.JSON(http.StatusOK, todo) //返回什么格式和前端说好，用JSON就用JSON，或者用gin.H
}
```

10.r.RUN

###### 1.

```go
map[string][]string
```

map是键值字符串类型 string为键的类型 []string为值的类型

###### 2.http响应:

**HTTP 响应（HTTP Response）** 是服务器收到客户端的 HTTP 请求后，返回给客户端的一组数据，用来告诉客户端**请求是否成功、返回什么内容、以及相关元数据**

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260224190641470.png" alt="image-20260224190641470" style="zoom: 67%;" />

###### 3.GIN的安装

```go
go get -u github.com/gin-gonic/gin
```

在终端中执行

###### 4.请求、含义与作用(RESTFUL风格下)

Gin框架天生只支持restful风格的api

- GET 获取信息
- POST 新建资源（用户上传文件专用）
- PUT 更新资源
- DELETE 删除资源

###### 5.GO语言的模板引擎

GO内置了文本模板引擎 text/template和用于HTML文档的html/template

模板可以理解为事先定义好的HTML文档文件，模板渲染的作用机制可以简单理解为文本替换操作->
即使用相应的数据去替换HTML文档中事先准备好的标记

模板文件用{{  ... }}表示需要传输的数据

###### 6.通常以 "."表示当前路径

例如: filename:  "**./helloworld/test**"

如果数据是复杂的类型，可以通过{{.fieldName}}来访问

###### 7.在Go中变量定义的首字母

首字母是否大写作为一个是否对外暴露的标识
要是允许别人访问，则首字母一定要大写
反之，若小写，则同级目录无法访问

但是对于map键值对，若键string的首字母为小写同样可以被访问

###### 8.go mod tidy

在终端中输入即可

###### 9.`*gin.Context`是 Gin 框架最核心的结构体，它封装了 HTTP 请求和响应的所有信息，是每个处理函数的第一个参数。

所有信息包括：
**1.请求信息**

```go
// 请求行
c.Request.Method      // HTTP 方法: GET, POST, PUT, DELETE
c.Request.URL         // 请求URL
c.Request.Proto       // HTTP 协议版本

// 请求头
c.Request.Header      // 所有请求头
c.GetHeader("Content-Type")  // 获取特定头

// 请求体
c.Request.Body        // 原始请求体（可读取）

// 请求参数
c.Query("name")       // URL查询参数: ?name=value
c.DefaultQuery("page", "1")  // 带默认值的查询参数
c.PostForm("username")  // POST表单参数
c.FormFile("avatar")   // 上传的文件
```

2. **路径参数（URL Parameters）**

3. **绑定和验证（Binding & Validation）**

4. **响应控制（Response Control）**:

   ```go
   // 返回JSON
   c.JSON(200, gin.H{"message": "成功"})
   
   // 其他响应格式
   c.XML(200, data)        // XML响应
   c.YAML(200, data)       // YAML响应
   c.String(200, "文本")   // 纯文本
   c.HTML(200, "index.html", data)  // HTML模板
   
   // 设置响应头
   c.Header("X-Custom-Header", "value")
   c.Writer.Header().Set("Content-Type", "application/json")
   
   // 重定向
   c.Redirect(302, "/new-path")
   ```

   **5.中间件数据传递**

   **6.状态和错误处理**

   **7.Cookie 和 Session**

###### 10.路由和路由组

什么是路由？

**路由**就是定义 **URL 路径** 和 **处理函数** 之间的映射关系。  ->可以理解为一个请求对应一个函数

什么是路由组？

**路由组**是将**具有相同前缀的路由**进行分组管理，可以统一添加中间件、统一文档注释等。

![image-20260309165917095](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260309165917095.png)

例如:

```go
 // 创建路由组，所有路由都有 /api/v1 前缀
    v1 := r.Group("/api/v1")
    {
        v1.GET("/users", getUsers)      // 实际路径: /api/v1/users
        v1.POST("/users", createUser)   // 实际路径: /api/v1/users
        v1.GET("/users/:id", getUser)   // 实际路径: 	/api/v1/users/123
    }
```



###### 11.

`*http.Response`是 Go 语言 `net/http`包中最重要的结构体之一，代表**HTTP 响应**的所有信息。这其中包括:

```go
type Response struct {
    Status     string    // 状态行，如 "200 OK"
    StatusCode int       // 状态码，如 200、404、500
    Proto      string    // 协议版本，如 "HTTP/1.1"
    ProtoMajor int       // 主版本号，如 1
    ProtoMinor int       // 次版本号，如 1
    Header     Header    // 响应头（键值对）
    Body       io.ReadCloser // 响应体（核心数据）
    ContentLength int64  // 内容长度，-1 表示未知
    TransferEncoding []string // 传输编码，如 ["chunked"]
    Close      bool      // 是否应在响应后关闭连接
    Uncompressed bool    // 是否已解压缩
    Trailer    Header    // 尾部头字段（延迟发送）
    Request    *Request  // 触发此响应的请求
    TLS        *tls.ConnectionState // TLS 连接信息
}
```

对于Header,本质 `map[string][]string`
对于Body,类型是 `io.ReadCloser`，需要手动关闭

```go
body, err := ioutil.ReadAll(resp.Body) //读取内容\
// 记得关闭！
defer resp.Body.Close()
```

对于request，可以查看这个响应是由哪个请求触发的
对于TLS,仅 HTTPS 请求有值,可获取加密协议版本、证书等信息

###### 12.序列化

![image-20260319202640594](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260319202640594.png)

###### 13.查询参数和路劲参数

![image-20260319211409157](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260319211409157.png)



### ------------------重要函数与结构体------------------

###### 1.对于http.ResponseWriter，gIN扩展了标准库的功能:

功能是将要返回给浏览器的内容，则往参数里面写

```go
type ResponseWriter interface {
    http.ResponseWriter    // 嵌入 http.ResponseWriter，获得其所有方法
    http.Hijacker          // 嵌入 http.Hijacker，获得 Hijack() 方法
    http.Flusher           // 嵌入 http.Flusher，获得 Flush() 方法
    http.CloseNotifier     // 嵌入 http.CloseNotifier，获得CloseNotify() 方法
	// Gin 扩展方法
    // 返回当前写入的状态码
    Status() int
    // 返回已写入的字节数
    Size() int  
    // 写入字符串
    WriteString(string) (int, error)   
    // 是否启用了写入
    Written() bool  
    // 强制设置状态码
    WriteHeaderNow() 
    // 暂停写入
    PauseOutput() <-chan struct{}
    // 恢复写入
    ResumeOutput()
    // 设置响应头
    SetHeader(key, value string)
}
```

###### 2.*http.Request表示一个 HTTP 请求。它是服务器端处理客户端请求时最重要的数据结构

也就是在“请求 -  回复”中的“请求”部分，而response为“回复部分”

```go
// $GOROOT/src/net/http/request.go
type Request struct {
    // 请求方法
    Method string
    
    // URL 结构体
    URL *url.URL
    
    // 协议版本
    Proto      string // "HTTP/1.0"
    ProtoMajor int    // 1
    ProtoMinor int    // 0
    
    // 请求头
    Header Header
    // Body 是请求体
    Body io.ReadCloser
    // ContentLength 记录请求体的长度
    ContentLength int64
    // TransferEncoding 传输编码
    TransferEncoding []string
    // Close 指示是否关闭连接
    Close bool
    // Host 请求的主机名
    Host string
    // Form 表单数据
    Form url.Values
    // PostForm POST 表单数据
    PostForm url.Values
    // MultipartForm 多部分表单
    MultipartForm *multipart.Form 
    // Trailer 尾部头
    Trailer Header 
    // RemoteAddr 远程地址
    RemoteAddr string
    // RequestURI 请求 URI
    RequestURI string
    // TLS 安全连接信息
    TLS *tls.ConnectionState    
    // Cancel 请求取消通道
    Cancel <-chan struct{}   
    // Response 关联的响应
    Response *Response
    // ctx 上下文
    ctx context.Context
}
```

###### 3.http.HandleFunc函数和ListenAndServe：

```go
func main() {
    func HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
    DefaultServeMux.HandleFunc(pattern, handler)
}
    
    // 启动服务器
    func ListenAndServe(addr string, handler Handler) error
}
```

对于handlefunc,用于将路径映射于函数中

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260226120017193.png" alt="image-20260226120017193" style="zoom:67%;" />

注意：该函数必须包含两个参数，如以上代码和图所示

对于ListenAndServe，调用后创建 TCP 监听器，绑定到指定地址 
![image-20260226120115790](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260226120115790.png)

```go
func helloHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, World!")
}

// 启动服务器
    http.ListenAndServe(":8080", nil)
```

流程：客户端请求 → http.ListenAndServe → DefaultServeMux → 匹配 pattern → 执行 handler

###### 读取文件内容：可以将同根目录下的hello.txt中的html文档展示

```go
b,_ := ioutil.ReadFile("./hello.txt")
_,_ = fmt.Fprintln(w,string(b))
```

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260320164349436.png" alt="image-20260320164349436" style="zoom:50%;" />

###### 4.HTML模板渲染

Gin中使用loadHTMLGlob()  or  LoadHTMLfiles() 方法进行模板解析

```go
	r.LoadHTMLFiles("template/index.tmpl")
	r.LoadHTMLGlob("template/**/*")
```

对于glob,/*代表tem目录同级下的文件，而`/**/*`代表目录下的任何文件，不需要`/***`的情况







### --------------------重要语法---------------------------

##### 1.页面响应与模板

使用http库的handlefunc

```go
func main() {
	http.HandleFunc("/hello", sayhello)
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		fmt.Printf("HTTP fail:%v\n", err)
		return
	}
}
```

则把/hello这个路径映射至sayhello函数当中，对于sayhello函数，可以看到他要求传入两个值:

```
(w http.ResponseWriter, r *http.Request)
```

###### 1.在此之前，先把模板搞定,在同级目录下创立.tmpl文件

在静态的地方，正常写入HTML代码
在需要模板替换的地方,使用{{  .name }}来访问根对象,{{  }}来标注模板替换

若是模板在解析的时候使用map+空接口，使键值对的值可以传入容易类型的变量以此传入多个结构体,则使用 .struct.name 来访问需替换的部分

另外，使用左/右中线可以移除左/右的空格

如以下代码所示：

```go
<!doctype html>
<html lang="cn ">
<head>
    <meta charset="UTF-8">
    <title>Hello world</title>
</head>
<body>
    <p>hello {{- .u1.Name }}</p> <!模板的用法 -> 传来的变量是什么这个点就表示什么>
<!而.name可以表示结构体中某个特定元素的值>
<p>年龄:{{- .u1.Age -}}</p> {{/*使用左/右中线可以移除左/右的空格*/}}
<p>性别:{{ .m1.Gender }}</p>  {{/*使用不同结构下的情况*/}}
</body>
</html>
```

也可以用with营造一块局部的作用域

```html
<p>{{with .m1}}</p>
<p>{{.name}}</p>
<p>{{.age}}</p>
<p>{{.gender}}</p>
{{end}}
```

###### 2.关于模板的嵌套和继承

使用block来定义一个特殊的区块

后面的模板想要继承根模板只要继承这块区域就行了
以下为根模板：

```http
<div class = "content center">
		{{block "content" .}}
		{{end}}
</div>
```

"content"为需要替换的部分

以下为继承跟模板并重新定义新区块

在大部分情况下，如果跟模板没有define则默认使用文件名，如果需要改名则需define

```html
{{template "base.tmpl" .}} {{/*继承根模板*/}}
{{define "content"}} 
<h1>这是indexxxxx页面</h1h1> {{/*重新定义块模板*/}}
<p>
    hello {{ . }}   {{/*此处可传值*/}}
</p>
{{end}}
```

而在main中的解析和渲染正常传值就行

###### 之后，再在sayhello函数中解析模板并渲染模板

对于解析模板的函数:
```go
func (t *Template) Parse(text string) (*Template, error)
func ParseFiles(filenames ...string) (*Template, error)
func ParseGlob(pattern string) (*Template, error)
```

对于渲染模板的参数

```go
 func (t *Template) Execute(wr io.Writer, data interface{}) error
func (t *Template) ExecuteTemplate(wr io.Writer, name string, data interface{}) error
```

在此使用parsefiles与execute为例如下:

```go
func sayhello(w http.ResponseWriter, r *http.Request) {
    u1 := User{
       Name:   "sc",     //首字母是否大小写作为一个是否对外暴露的标识
       Gender: "female", //要是允许别人访问，则首字母一定要大写
       Age:    18,
    }

    m1 := map[string]interface{}{
       "name":   "sc",
       "gender": "female", //在map中，string键首字母小写同样可以被允许访问
       "age":    18,
    }

    //第二步，解析模板
    t, err := template.ParseFiles("./hello.tmpl")
    //解析文档 返回一个模板指针和错误
    if err != nil {
       fmt.Printf("parse fail: %v\n", err)
       return
    }
    //第三步，解析模板
    //解析之后传给谁 -> w
    //name := "scqwq"
    err = t.Execute(w, map[string]interface{}{
       "u1": u1,
       "m1": m1,
    })
    if err != nil {
       fmt.Printf("execute fail: %v\n", err)
       return
    }

}
```

###### 我们也可以在模板中定义变量,同时做一些常用的逻辑

以下为语法:

**1.定义变量**

`{{$v := 100}}`  与  `{{&age = .m1.age}}`

**2.条件判断**

```
{{if $v1}}
{{$v1}}
{{else}}
啥都没有
{{end}}
```

注意：if 与 end 成对出现

**3.range循环**

语法如下:设hobby为传进来的字符串数组(即[]string),则有两种写法:

```
{{range $idx,$hobby := .hobby}}  //设hobby为传进来的字符串数组(即[]string)
<p> {{$idx}} - {{$hobby}}  </p>
{{end}}
```

```go
{{range $idx,$hobby := .hobby}}  //设hobby为传进来的字符串数组(即[]string)
<p> {{$idx}} - {{$hobby}}  </p>
{{else}}
 没有爱好
{{end}}
```

即range语句作条件判断

##### 2.Gin返回json

###### 1.基本语法

已知数据：

```go
data := map[string]interface{}{
    "name":"huh?",
    "message":"hello",
    "age":114,
}

//而在Gin框架中，他将该map类型封装成了H,所以可以便捷地调用：

data := gin.H{
    "name":"huh?",
    "message":"hello",
    "age":114,
}
```

返回使用:

```go
r.GET("/index",func(c *gin.context){
    c.JSON(http.StatusOK,data)
})
```

注意，返回的第一个(OK)是有对应状态码的，直接写200也可以

###### 2.进阶

可以使用结构体记录信息

```go
type msg struct{
    Name string `json:"name"`
    Message string
    Age int
}
//之后，可以用data直接:=即可
data =: msg{
    "huh"
    //...
}
//但是需要注意的是,当name为小写的，私有的，则无法被json化
//而可以用tag给结构体做一些自定义的操作,当用json包作反射取值操作,
//当我们想展现小写时，用如上的标准即可
```

##### 3.附加请求:querystring

通常来说，“？”前是URL,“？”后为querystring的参数

###### 1.c.Query

c.Query是 *gin.Context中用于获取 URL 查询参数（Query Parameters）的方法，也就是 URL 中 ?后面的键值对。

当URL: ***/search?keyword=golang&page=1&size=10*** 时，键值对为:

keyword -> golang
page -> 1
size -> 10

而使用`c.Query(key)`则可以将value返回（则可以用变量接住

```go
//基本获取:
func searchHandler(c *gin.Context) {
    // 获取单个查询参数
    keyword := c.Query("keyword")  // "golang"
    page := c.Query("page")        // "1"
    size := c.Query("size")        // "10"
    
    c.JSON(200, gin.H{
        "keyword": keyword,
        "page":    page,
        "size":    size,
    })
}
//则此时可以返回querystring的参数
```

若是查询多个参数，即对于多个key=value则可以用&连接（如上的URL）

###### 2.c.Defaultquery

```go
name := c.DefaultQuery("query","somebody")  //此时somebody为默认值
```

当搜索不到query或者其他定义好的值时，展现somebody(取不到就用预定的默认值)

###### 3.getquery

```go
name,ok := c.GetQuery("query")
if !ok{
    name = "somebody"
}
c.JSON(200,gin.H{
    "name":name,
})
```

返回两个参数，(string ,bool),取不到则第二个参数返回false

注意，返回的JSON顺序为字典序

##### 4.gin获取form函数

form表单可用于登陆界面的账号、密码等地方

先获得登陆界面的模板文件:`r.LoadHTMLFiles(".login.html")`

```go
r.POST("login",func(c *gin.Context)){
    //获取form表单提交的数据
    username := c.PostForm("username")
    password := c.PostForm("password")
    
    c.HTML(200,"index.html",gin.H{
        "Name":username,
        "Password":password,
    })
}
```

与questr相似，form也有Default与GET

```go
    username := c.DefaultPostForm("username","somebody")
	password := c.DefaultPostForm("password","xxx")

username,ok := c.GetPostForm("username")
if !ok{
    username = "sb"
}
```

##### 5.GIn获取URI路径参数

```go
r.GET("/user/:name/:age",func(c *gin.Context){
//在路径前价格":"以便获取路劲参数，方式如下:
name := c.Param("name")
age := c.Param("age")

c.JSON(200,gin.H{
"name":name,
"age":age,
})
})
//在Get中的第二个参数可以使用Param获取路径参数
//可以使用Params获取所有路径参数（前提是有gin.Params切片）

//类型定义
type Param struct {
    Key   string
    Value string
}
type Params []Param
//则可以使用:
for _, param := range c.Params {
        fmt.Printf("  %s = %s\n", param.Key, param.Value)
    }

```

注意：URL的返回都是string类型(3、5都是)



更多的方法：

```go
params := url.Values{}  
//其本质是url库定义的values 等价于map[string][]string
params.Set("key":"Your_key") //自己定义
params.Set("id":id)	//通过Query获取的参数，用id接住

//这样就建立了映射关系，而通过Get可以读取映射,或者通过Encode映射，将内存中的键值对映射转换为URL查询字符串。

//1.拼成完整路径
func buildCompleteURL(baseURL string, queryParams map[string]string) string {
    params := url.Values{}
    
    for key, value := range queryParams {
        params.Set(key, value)
    }//建设键值对
    
    if len(params) > 0 {
        return baseURL + "?" + params.Encode()
    } //使用？拼起来
    return baseURL
}
//调用函数
url := buildCompleteURL("https://api.example.com/search", map[string]string{
    "q":     "golang tutorial",
    "lang":  "zh",
    "limit": "20",
})

//2.直接Get
resp,err := http.Get(apiUrl + "?" + params.Encode)


```



##### 6.Gin参数绑定

```go
//假设已经定义了一个结构体，且和用户传过来的请求数据类型对得上，则可以
var u Userinfo	//声明一个UserInfo类型的变量U
err := c.ShoutldBind(&u) //注意需要传地址 且注意结构体变量名大写开头
if err != nil{
    c.JSON(200,gin.H{
        "error":err.Error(),
    })
}
```

##### 7.GIN文件上传

###### 1.单个文件的上传

上传文件：把一个文件从Client端上传到Server端

```go
r.LoadHTMLFiles("./index.html")//先构建上传的前端页面,
r.GET("/index", func(c *gin.Context) {//假设前端传回的文件name = "f1"
		c.HTML(http.StatusOK, "index.html", gin.H{})
	})
//使用POST请求，因为长传文件只能用POST请求
//当用户试图上传文件时,URI回自动变为/upload
	r.POST("/upload", func(c *gin.Context) {
		//1.从请求中读取文件
		f, err := c.FormFile("f1") //从请求中获取携带参数一样的文件
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		} else {
			//dst:目的地，保存文件的路径
			dst := fmt.Sprintf("./%s", f.Filename)
			//可以用Sprintf拼接，也可以用库中的path
			_ = c.SaveUploadedFile(f, dst)
			//将已经上传的文件保存的本地
			c.JSON(http.StatusOK, gin.H{
				"status": "ok",
				"data":   f})
		}

```

即:使用`c.Formfile,func`从POST请求中读取文件

之后用`c.SaveUploadedFile`选择文件和位置并保存

c.JSON用于输出信息

###### 2.多个文件的上传

多个文件上传时，前端的action和method的同级标识下的**enctype**依旧选择
***"multipart/form-data">***

处理multipart forms提交文件时默认的内存限制是32 MiB
可以通过下面的方式修改
router.MaxMultipartMemory = 8 << 20  // 8 MiB

```go
	r.POST("/upload", func(c *gin.Context) {
		// Multipart form
		form, _ := c.MultipartForm()
        //c.multi...的作用是解析 HTTP 请求中的 multipart/form-data数据，返回一//个包含所有表单字段和文件的结构体。
		files := form.File["file"]
        //获取所有的name="file"的文件 返回一个string数组
	for index, file := range files {
		log.Println(file.Filename)
		dst := fmt.Sprintf("./%s_%d", file.Filename, index)
        //使用fmt.Sprintf连接,其作用是根据指定的格式将变量转换为字符串并返回
		// 上传文件到指定的目录
		c.SaveUploadedFile(file, dst)
	}
	c.JSON(http.StatusOK, gin.H{
		"message": fmt.Sprintf("%d files uploaded!", len(files)),
	})
})
r.Run()
}
```
##### 8.Gin的重定向

重定向：简单来说就是一个请求访问到服务器之后，将这个请求转给别的服务器，可以是别人的网站，也可以是当前网站的另外一个地址

###### HTTp重定向

HTTP重定向很容易。内外部重定向均支持

```go
r.GET("/test",func(c *gin.context){
    c.Redirect(http.StatusMovePermanently,"http://www.sogo.com/")
    //StatusMovedPermanently = 301
})
```

###### 路由重定向

其实就是重写URI

```go
r.GET("/a", func(c *gin.Context) {
		c.Request.URL.Path = "/b" //把请求的URL修改
		r.HandleContext(c)        //继续后续的处理
	})

	r.GET("/b", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "hello b",
		})
	})
```

常用于登陆界面的跳转

##### 9.GIN路由和路由组

###### 1.简单路由

路由的定义在前文，此处介绍使用Any偷懒的写法

```go
r.Any("/user", func(c *gin.Context) {
		switch c.Request.Method {
		case "GET":
			c.JSON(200, gin.H{"method": "GET"})
		case http.MethodPost: //可以使用http软件包中的字符串define
			c.JSON(http.StatusOK, gin.H{"method": "POST"})
		}
		//此处为样例就不往下写了
	})
```

而当别人访问不存在的地址的时候，使用noroute

```go
r.NoRoute(func(c *gin.Context) {
		c.JSON(http.StatusNotFound, gin.H{
			"JSON": "no index,rewrite plz",
		})
	})
```

###### 2.路由组

可以将拥有共同URL前缀的路由划分为一个路由组，习惯用一对{ }包裹同组的路由
但是这只是为了看着清晰，用不用{ }包裹实际上没啥区别

注：这并不会减少很多的工作量，但是能在代码层面美观，减少阅读难度

```go
	//将公用的前缀提取出来，创建一个路由组
	videoGroup := r.Group("/video")
	{
		videoGroup.GET("index", func(c *gin.Context) {
			c.JSON(200, gin.H{
				"msg": "/video/index",
			})
		})
		videoGroup.GET("xx", func(c *gin.Context) {...})
		videoGroup.GET("oo", func(c *gin.Context) {...})
	}
	//好处是代码层面更为美观
```

当需要给某些接口加权限认证时路由组也可派上用场

##### 10.中间件(Use Next Abort)

###### 注意：Gin中的中间件必须是一个`gin.HandlerFunc`类型。

Gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如**登录认证**、**权限校验**、数据分页、记录日志、耗时统计等。

比如：一个网站有500个路由，每一次都去查看是否登录则十分麻烦，这时候则需要中间件

以下中间件完成了一个计时的功能

```go
// 定义一个中间件
func m1(c *gin.Context) {
    fmt.Println("m1 in...")
    //计时
    start := time.Now()
    //next调用该请求剩余的程序 即调用后续的处理函数
    c.Next()
    //c.Abort为阻止调用后续的处理函数,即后续的处理函数不会生效，
    //处理完自己的函数即返回
    //而return表示立即终止函数，这个不用多说
    cost := time.Since(start)
    fmt.Printf("m1 cost:%v\n", cost)
    fmt.Println("m1 end...")
}

//main中调用:
r.GET("/index",m1,func(c *gin.context){...})
```

但是，可以使用**Use**批量注册中间件,也可以用***路由和路由组***注册中间件,也就是往中间件中传参

```go
	//而当想全局注册中间件m1时，使用Use即可
	r.Use(m1,m2)
	r.GET("/shop", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"msg": "shop is fine",
		})
	})
    //用路由组注册中间件，传参
	xxGroup:=r.Group("/xx",loginmiddle(true))//设login函数中需要传bool参
{
    xxGroup.GET("/index"),func(c *gin.context){
       // c.JSON...
    }
}

```

此时m1中用next会先执行m2,m2中用next则会执行匿名函数

->则我们可以利用Abort与Next写出简单登录中间件（伪代码)

```go
func loginmiddle(check bool)gin.HandlerFunc{
    //连接数据库
    //一系列工作
    
    return c *gin.context{
    //存放具体的逻辑 可以利用check干一些事情,同时实现一些灵活的开关控制
        
    //是否登录的判断:
    //if 是登录的用户
    //c.Next
    //else
    //c.Abort 滚去登录
    }
}
```



##### 11.Gin接收从客户端返回的JSON信息

已知:`c *gin.context`

->此时，c包含



##### 12.c.Set和get

这段代码是 Gin 框架中 `Context`对象的 `Set`方法实现，它的作用是**在请求上下文中存储键值对**。

```go
// 认证中间件
func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        // 验证用户...
        userID := 123
        userName := "张三"
        
        // 将用户信息存储到 Context
        c.Set("user_id", userID)
        c.Set("user_name", userName)
        c.Set("user_role", "admin")
        
        c.Next()
    }
}

// 业务处理器
func GetUserProfile(c *gin.Context) {
    // 从 Context 获取数据
    userID, _ := c.Get("user_id")
    userName, _ := c.Get("user_name")
    role, _ := c.Get("user_role")
    
    c.JSON(200, gin.H{
        "id":   userID,
        "name": userName,
        "role": role,
    })
}

// 路由配置
r.GET("/profile", AuthMiddleware(), GetUserProfile)
```

##### 13.

### GORM

#### ORM:对象关系映射

可以理解为用在代码中不用进行连接数据库、编写数据库语法的繁琐操作，直接以代码风格的语言使用数据库的工具

需要import和get的库：

`"github.com/jinzhu/gorm"`

##### 1.连接数据库

连接不同的数据库都需要导入对应数据库的驱动程序，GORM已经帮我们准备，只需按照以下方式导入需要的数据库即可

```go
import "github.com/jinzhu/gorm"
import _ "github.com/jinzhu/gorm/dialects/sql"
```

“sql”代表任一关系型数据库的名字

##### 2.数据库连接实例

`DB *gorm.DB`

**DB** 是一个全局的 **Gorm 数据库连接实例**，它是应用程序与数据库进行交互的主要入口。

我们先假设有一个结构体:

```go
type Todo struct {
	ID     int    `json:"id"`
	Title  string `json:"title"`
	Status bool   `json:"status"` `gorm:"primaryKey"
}
```

要说明的是,json和gorm都是Go结构体的标签，用于用于为字段添加元数据，供不同的库/框架使用。

json字段意为：JSON字段名为"status"    gorm字段标签意为：主键，gorm字段

json字段仅为改名，无太多限制，但gorm的tag有提前订好的特定要求，用于表示数据库中的信息

常用指令：

```go
DB *gorm.DB
//映射：
DB.Find(&todolist)  //todolist是一个List类型的结构体
//从数据库表中查询多条记录，并将结果映射到 Go 的切片（slice）中


```









