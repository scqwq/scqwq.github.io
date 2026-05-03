title: 'EINO'

date: 2026-05-04T11:00:00-07:00
lastmod: 2026-05-04T11:00:00-07:00

# EINO

godotenv 的实际查找逻辑:

`godotenv.Load()`使用的文件路径是**精确匹配**，不会自动递归向上搜索

也就是说，load会默认查找当前目录下的.env,想要查找父目录，则需要

```go
// 文件：/myapp/middle/deep/main.go
package main

import "github.com/joho/godotenv"

func main() {
    // 只会加载直接父目录（middle）的 .env
    godotenv.Load("../.env")
}
```

![image-20260402195859251](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260402195859251.png)

而在此图中，虽然main中在ch01/02下，可是工作目录是chatwitheino,使用的是命令

`go run ./cmd/ch01` or `go run ./cmd/ch02`

所以默认工作目录是chatwitheino,可以读取chatwitheino下的.env,所以这符合上面提到的.env的读取方法，同时，找对工作目录很重要

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260402200111774.png" alt="image-20260402200111774" style="zoom:50%;" />



### 非阻塞流式迭代器:

1. **什么是流式迭代器？**

传统迭代器：处理**有限、静态**的集合（如数组、列表）

流式迭代器：处理**无限、动态**的数据流（如网络流、事件流、传感器数据）

2. **什么是非阻塞？**

**阻塞**：等待数据时，线程被挂起，无法执行其他任务

**非阻塞**：没有数据时立即返回，线程可以处理其他任务



### 注册解析命令行参数

##### 命令行基本参数

```bash
# 示例：git commit -m "message"
git                    # 命令
commit                 # 子命令
-m                     # 参数标识
"message"              # 参数值
```

##### 注册：

告诉程序："我期望接收这些参数"

定义参数的名称、类型、默认值、描述

程序启动时执行

##### 解析:

从命令行读取并验证实际输入

将字符串转换为指定类型

验证参数合法性

通常在执行具体功能前进行

###### 例如：

```go
flag.StringVar(&sessionID, "session", "", "session ID (creates new if empty)")
```

则参数标识为 --session 默认值为"" ，最后的参数为usage

usage参数是命令行参数的帮助文本，用于向用户说明这个参数的作用、格式、注意事项等信息。当用户执行 -h或 --help时，这些信息会显示出来

也就是当程序直接进行`go run`时，会弹出来提醒用户

### 持久化

##### 对话存储

`Session` 代表一次完整的对话会话

`Store` 管理多个 Session 的持久化存储

```go
type Session struct {
    ID        string
    CreatedAt time.Time

    messages []*schema.Message  // 对话历史
    // ...
}

type Store struct {
    dir   string              // 存储目录
    cache map[string]*Session // 内存缓存
}
```

核心方法:

​	**Session核心方法**

- `Append(msg)`：追加消息到会话，并持久化

- `GetMessages()`：获取所有消息

- `Title()`：从第一条用户消息生成会话标题

  **store核心方法**

- `GetOrCreate(id)`：获取或创建 Session

- `List()`：列出所有 Session

- `Delete(id)`：删除 Session

每个Ses存储一个.jsonl文件

```go
{"type":"session","id":"083d16da-...","created_at":"2026-03-11T10:00:00Z"}
{"role":"user","content":"你好，我是谁？"}
{"role":"assistant","content":"你好！我暂时不知道你是谁..."}
{"role":"user","content":"我叫张三"}
{"role":"assistant","content":"好的，张三，很高兴认识你！"}
```

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260403150816560.png" alt="image-20260403150816560" style="zoom:50%;" />

##### scanner 

```go
// 内部实现简化
type Scanner struct {
    r io.Reader     // 底层读取器
    buf []byte      // 缓冲区
    // ...
}
// 创建时会分配默认4KB缓冲区
```

对于`scanner := bufio.NewScanner(os.Stdin)`

创建一个带缓冲的扫描器，用于高效读取输入
os.Stdin：标准输入流（键盘输入）
返回值：*Scanner对象，提供方便的文本读取方法
scanner := bufio.NewScanner(os.Stdin)

`========================================================`

对于`scanner.Scan`

**功能**：读取下一行输入

**返回值**：

`true`：成功读取一行

`false`：遇到错误或EOF（End Of File)



对于`line := scanner.Text()`

**功能**：获取最后一次 `Scan()`读取的文本

**注意**：必须在 `Scan()`返回 `true`后调用



对于`line := strings.TrimSpace(scanner.Text())`

清除两边空白字符

对于`if line == "" { ） `//空行检查



对于`schema` 可以创建不同类型的消息

userMsg := schema.UserMessage("你好")
assistantMsg := schema.AssistantMessage("你好！", nil)
systemMsg := schema.SystemMessage("你是AI助手")



对于`session.Append(userMsg)`

将对话消息加入到历史

### TOOL

对于自定义工具，必须实现tool.BaseTool接口
    

```go
type BaseTool interface {
// 获取工具信息
Info(ctx context.Context) (*schema.ToolInfo, error)
// 执行工具
//Run(ctx context.Context, input *ToolInput) (*ToolOutput, error)
// 工具类型
//Type() string
}

// 2. 可执行工具接口（最常用）
type InvokableTool interface {
    BaseTool  // 嵌入 BaseTool，必须实现 Info
    InvokableRun(ctx context.Context, argsJSON string, opts ...Option) (string, error)
}

// 3. 流式工具接口
type StreamableTool interface {
    BaseTool
    StreamableRun(...) (*StreamReader[string], error)
}

```
BaseTool只是“说明书”，不能运行。
实际干活必须实现 InvokableTool或 StreamableTool

##### 创建工具:

一般会创建一个函数直接返回InvokableTool,每个函数都是个性化函数，仅仅为了方便返回工具而非为了复用。

**示例：**

```go
func CreateTool() tool.InvokableTool {
	//utils.NewTool实际上创建了一个实现了 tool.InvokableTool接口的包装器,此时输入ToolInfo即可帮助你实现InvokableTool接口,你只需要专注于实现Getgame函数的逻辑即可
	getGameTool := utils.NewTool(
		&schema.ToolInfo{
			Name: "get_game",
			Desc: "get a game url by name",
			ParamsOneOf: schema.NewParamsOneOfByParams(
				map[string]*schema.ParameterInfo{
					"name": &schema.ParameterInfo{
						Type:     schema.String,
						Desc:     "game's name",
						Required: true,
					},
				},
			),
		}, Getgame)
	return getGameTool
}
```

此时仅需要使用utils的NewTool函数便可快捷创建Invokable工具，我们只需要输入ToolInfo即可

之后再函数末端返回工具实例







![image-20260403162827397](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260403162827397.png)

![image-20260403163149583](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260403163149583.png)

![image-20260403163201988](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260403163201988.png)

![image-20260403163212251](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260403163212251.png)

### 编排

编排的核心是 **“节点（Node） + 边（Edge）”** 的图计算模型。Eino 提供了三种不同粒度的编排方式，应场景灵活选择。

使用库：`import "github.com/cloudwego/eino/compose"`

`==================================================================`

Bind相当于将工具注册到大语言模型中，而创建ToolsNode相当于创建了在Agent中创建了一个可以运行的节点，再用Chain或者Graph方式就可以将这些节点连接起来

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260414165314091.png" alt="image-20260414165314091" style="zoom:50%;" />

不管如何编排，**注册工具**和**创建节点**总是必须的。

**注册工具:**

```go
	info, err := getGameTool.Info(ctx) //Info方法，返回工具信息，包括工具名称、描述、参数等
	//将单个工具信息包装成工具信息列表
	infos := []*schema.ToolInfo{
		info,
	}
	//将工具信息绑定到大语言模型上
	//不直接执行工具：只是告诉模型工具的存在和调用方式，实际执行在 ToolsNode 中
	err = model.BindTools(infos)
	if err != nil {
		panic(err)
	}
```

**创建节点**

```go
	ToolsNode, err := compose.NewToolNode(context.Background(), &compose.ToolsNodeConfig{
		Tools: []tool.BaseTool{
			getGameTool,
		},
	})
//注意：tool.BaseTool是一个接口，定义了工具必须实现的方法：所以理在BaseTool切片中，应填入完成接口的工具的实例
```

若是想把函数直接转化为，可以使用compase库的**InvokableLambda**

compose.InvokableLambda是 Eino 编排框架中一个非常强大和灵活的函数，它允许你将普通 Go 函数包装成 Eino 的 Invokable节点。

```go
	//InvokableLambda负责将用户提供的函数包装成一个可执行的Lambda节点，用户提供的函数需要满足特定的签名要求，
	//即输入参数为context.Context和一个字符串，输出参数为一个指向schema.Message切片的指针和一个错误。	
lambda := compose.InvokableLambda(func(ctx context.Context, input string) (output []*schema.Message, err error) {
		desuwa := input + "回答结尾加上desuwa"
    //string类型的输入被包装成了schema.Message类型的输出，也就是说输入输出的转化需要用户自行完成,这样输出的转化可以使工具调用的上下游对齐
		output = []*schema.Message{
			{
				Role:    schema.User,
				Content: desuwa,
			},
		}
		return output, nil
	})
```



##### CHain（链式编排）

1.一切的开始：创建链条

```go
chain := compose.NewChain[string , *schema.Message]() 
//后面还有一个变量option,可以给链条配置东西
//此时的两个函数用于检测链式调用的头部和尾部
//要求头部输入必须为string,结尾输出必须为*schema.Message
```

2.往链表中添加节点/模型

```go
chain.AppendLambda(lambda).AppendChatModel(model)
//AppendLambda添加节点
//AppendChatModel添加模型
//通过各类chain的方法往链条中添加各类节点
```

注意:Chain可认为是Graph的特殊情况

3.编译运行:

```go
r, err := chain.Compile(ctx) //compile对链条进行编译
// Compile 返回一个实现了 Invokable 接口的对象:
/*type Invokable[Input, Output any] interface {
    Invoke(ctx context.Context, input Input, opts ...RunOption) (Output, error)
    Type() string
}*/

answer, err := r.Invoke(ctx, "你好，我叫琉璃子" /*此处的输入类型由①决定*/)
	fmt.Printf("Answer: %s\n", answer.Content)	
//这样便可以使用chain.Invoke(ctx, input)(Output, error)运行
```

##### Graph(图式编排)

需要注意的是，Graph和链式编排大同小异，但最大的区别在于需要使用Addbranch方法

此时LLM（或其他判断逻辑）会根据具体情况选择执行路线

1.依旧第一步，先new一个Graph,并写入一个判断逻辑

```go
//Init a Graph
g := compose.NewGraph[string, string]()

//InvokableLambda创建节点
lambda0 := compose.InvokableLambda(func(ctx context.Context, input string) (output string, err error) {
    if input == "1" {
       return "毫猫", nil
    } else if input == "2" {
       return "耄耋", nil
    } else if input == "3" {
       return "device", nil
    }
    return "", nil
})
lambda1 := compose.InvokableLambda(func(ctx context.Context, input string) (output string, err error) {
		return "喵！", nil
	})
	lambda2 := compose.InvokableLambda(func(ctx context.Context, input string) (output string, err error) {
		return "哈！", nil
	})
	lambda3 := compose.InvokableLambda(func(ctx context.Context, input string) (output string, err error) {
		return "没有人类了!!!", nil
	})
```

2.添加节点

注意:AddLambdaNode和.AppendLambda相比:

AppendLambda会自动连接上一个节点，而AddLambdaNode需要显示添加边

常见的添加节点方式:

```go
// 1. Lambda 节点
graph.AddLambdaNode("preprocess", func(ctx context.Context, input string) (string, error) {
    return strings.TrimSpace(input), nil
})
// 2. 模型节点
graph.AddChatModelNode("llm", model)
// 3. 工具节点
graph.AddToolsNode("tools", toolsNode)
// 4. 分支节点
graph.AddBranch("router", compose.NewBranch(func(ctx context.Context, query string) (string, error) {
    if strings.Contains(query, "?") {
        return "question", nil
    }
    return "statement", nil
}))
// 5. 条件节点
graph.AddCondition("check_length", func(ctx context.Context, s string) (bool, error) {
    return len(s) > 10, nil
})
```



**也就是说，AddLambdaNode只是将节点添加到图中，不会自动创建边，需要你定义连接关系**

注意：在前面的是节点名，在后面的是要处理的节点实例(函数)
例如在下面的例子中，"lambda0"为节点名，lambda0为要执行的节点实例（函数）

```go
//加入Lambda节点,此时只是注册节点，将lambda0注册为"lambda0"(右注册为左)
	err := g.AddLambdaNode("lambda0", lambda0)
	if err != nil {
		panic(err)
	}
	err = g.AddLambdaNode("lambda1", lambda1)
	if err != nil {
		panic(err)
	}
	err = g.AddLambdaNode("lambda2", lambda2)
	if err != nil {
		panic(err)
	}
	err = g.AddLambdaNode("lambda3", lambda3)
	if err != nil {
		panic(err)
	}
```

3.加入分支并连接

```go
err = g.AddBranch("lambda0"/*节点名称*/, 
    compose.NewGraphBranch/*分支函数：决定下一个节点*/(func(ctx 			context.Context, in string) (endNode string, err error) {
        //in 接收上一个节点的输出  endNode 也就是output 下一个要执行的节点名称
		if in == "毫猫" {
			return "lambda1", nil
		} else if in == "耄耋" {
			return "lambda2", nil
		} else if in == "device" {
			return "lambda3", nil
		}
		// 否则，返回 compose.END，表示流程结束
		return compose.END, nil
	}, map[string]bool{"lambda1": true, //合法节点映射
                       "lambda2": true, 
                       "lambda3": true, 
                       compose.END: true}))

	if err != nil {
		panic(err)
	}
```

并将这些分支与对应的函数（Tool）连接

注意compose.START是图编排开始的部分

```go
	//分支连接
err = g.AddEdge(compose.START, "lambda0") //开始节点:lambda0
	if err != nil {
		panic(err)
	}
//0 -> 1、2、3的连接已经再branch完成 只需要将1、2、3指引到结尾
	err = g.AddEdge("lambda1", compose.END)
	if err != nil {
		panic(err)
	}
	err = g.AddEdge("lambda2", compose.END)
	if err != nil {
		panic(err)
	}
	err = g.AddEdge("lambda3", compose.END)
	if err != nil {
		panic(err)
	}
```

4.编译与执行

```go
	r, err := g.Compile(ctx)
	if err != nil {
		panic(err)
	}
	// 执行 此时的choice为输入，即选择路线，可以将上述包装成函数
	answer, err := r.Invoke(ctx, choice)
	if err != nil {
		panic(err)
	}
	fmt.Println(answer)

```







### 调用流程（Eino 内部机制

1. **注册**：将工具注册到 `ToolsNode`或 `Agent`。
2. **感知**：LLM 在生成回复前，会读取所有工具的 `Info()`，生成对应的 Function Call Schema。
3. **决策**：LLM 判断是否需要调用工具（如用户问“今天天气如何” -> 触发天气工具）。
4. **执行**：框架解析模型输出的 JSON 参数，调用 `InvokableRun`并传入参数。
5. **返回**：将 `InvokableRun`返回的字符串作为工具调用结果，再次喂给 LLM 生成最终回复（ReAct 模式）。



```go
/*
 * Copyright 2026 CloudWeGo Authors
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package main

import (
    "bufio"
    "context"
    "errors"
    "flag"
    "fmt"
    "io"
    "os"
    "strings"

    "github.com/cloudwego/eino/adk"
    "github.com/cloudwego/eino/schema"

    examplemodel "github.com/cloudwego/eino-examples/adk/common/model"
)

//examplemodel是为了防止库同名另设的库名

func main() {
    var instruction string
    flag.StringVar(&instruction, "instruction", "You are a helpful assistant.", "")
    flag.Parse()

    ctx := context.Background()
    /*
       type Agent interface {
           Name(ctx context.Context) string
           Description(ctx context.Context) string

           // Run 执行 Agent，返回事件流
           Run(ctx context.Context, input *AgentInput, options ...AgentRunOption) *AsyncIterator[*AgentEvent]
       }*/

    // 当调用其他库的函数时，环境变量一般是读取当前目录下的环境变量
    //详见笔记，此时创建了一个ChatModel实例,即：
    //ark.NewChatModel(context.Background(), &ark.ChatModelConfig{})
    cm := examplemodel.NewChatModel()

    //ChatModelAgent 是 Agent 接口的一个实现，基于 ChatModel 构建，
    //它会将输入的历史消息作为上下文传递给 ChatModel，并将 ChatModel 的输出作为 Agent 的输出。
    agent, err := adk.NewChatModelAgent(ctx, &adk.ChatModelAgentConfig{
       Name:        "Ch02ChatModelAgent",
       Description: "A minimal ChatModelAgent with in-memory multi-turn history.",
       Instruction: instruction, // 系统指令
       Model:       cm,
    })
    if err != nil {
       _, _ = fmt.Fprintln(os.Stderr, err)
       os.Exit(1)
    }

    //创建运行器runner，Runner 是执行 Agent 的入口点，负责管理 Agent 的生命周期
    /*type Runner struct {
        a Agent  // 要执行的 Agent
        enableStreaming bool
        store CheckPointStore  // 用于中断恢复的状态存储

    // 方式 1：传入消息列表
    events := runner.Run(ctx, history)

    // 方式 2：便捷方法，传入单个查询字符串
    events := runner.Query(ctx, "你好")
    }*/
    runner := adk.NewRunner(ctx, adk.RunnerConfig{
       Agent:           agent,
       EnableStreaming: true, // 启用流式输出
    })

    //主循环：交互式聊天
    // 可以在创建 history 时直接包含一个助手消息
    //history := []*schema.Message{
    // schema.UserMessage("你好！"),
    // schema.AssistantMessage("你好！有什么可以帮助你的吗？", nil),
    // schema.UserMessage("请介绍一下你自己"),
    // // 后续可以继续添加
    //}
    history := make([]*schema.Message, 0, 16) // 预分配容量16的消息历史
    scanner := bufio.NewScanner(os.Stdin)     // 创建读取标准输入的扫描器

    //初始化消息历史（用户和 AI 的对话）
    //创建扫描器读取用户输入
    for {
       _, _ = fmt.Fprint(os.Stdout, "you> ") // 显示提示符
       if !scanner.Scan() {
          break // 读取用户输入
       }
       line := strings.TrimSpace(scanner.Text()) // 去除首尾空格
       if line == "" {                           // 空行退出
          break
       }
       // 添加用户消息到历史
       history = append(history, schema.UserMessage(line))

       events := runner.Run(ctx, history) // 运行智能体，获取事件流
       content, err := printAndCollectAssistantFromEvents(events)
       if err != nil {
          _, _ = fmt.Fprintln(os.Stderr, err)
          os.Exit(1) //错误处理
       }
       // 添加AI回复到历史
       history = append(history, schema.AssistantMessage(content, nil))
    }
    if err := scanner.Err(); err != nil {
       _, _ = fmt.Fprintln(os.Stderr, err)
       os.Exit(1)
    }
}

//核心问题 ：判断143行的流式，从event中找答案***************************************************************************************************

// 这是处理 AI 响应事件的核心函数
func printAndCollectAssistantFromEvents(events *adk.AsyncIterator[*adk.AgentEvent]) (string, error) {
    var sb strings.Builder // 用于收集完整的回复文本

    for {
       event, ok := events.Next() // 获取下一个事件
       if !ok {                   // 没有更多事件
          break
       }
       if event.Err != nil { //check error
          return "", event.Err
       }
       if event.Output == nil || event.Output.MessageOutput == nil {
          continue //跳过非消息输出
       }

       mv := event.Output.MessageOutput
       if mv.Role != schema.Assistant {
          continue // 只处理助手角色消息
       }

       if mv.IsStreaming { //处理流式输出
          mv.MessageStream.SetAutomaticClose() //自动关闭流
          for {
             // 只处理助手角色消息
             frame, err := mv.MessageStream.Recv()
             if errors.Is(err, io.EOF) { // 流结束
                break
             }
             if err != nil {
                return "", err //check error
             }
             if frame != nil && frame.Content != "" {
                sb.WriteString(frame.Content) //收集内容
                //实时显示
                _, _ = fmt.Fprint(os.Stdout, frame.Content)
             }
          }
          //换行
          _, _ = fmt.Fprintln(os.Stdout)
          continue
       }

       //非流式
       if mv.Message != nil {
          sb.WriteString(mv.Message.Content) //收集完整回复
          //显示
          _, _ = fmt.Fprintln(os.Stdout, mv.Message.Content)
       } else {
          _, _ = fmt.Fprintln(os.Stdout) //空回复
       }
    }

    return sb.String(), nil //返回收集到的完整回复
}
```
