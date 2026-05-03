title: 'AGENT'

date: 2026-05-04T11:00:00-07:00
lastmod: 2026-05-04T11:00:00-07:00

# AGENT

在adk框架中，agent.Agent是所有Agent必须实现的基础接口

∵它定义了一个Agent的基本属性以及它的隐形逻辑

以下是核心方法:

```go
Name() string
//作用：返回该 Agent 的名称（字符串）。AgentTree 中的每个名称必须唯一且不能是 "user"。
Description() string
//作用：返回该 Agent 的功能描述。如果系统包含多个 Agent，LLM 会依赖这段描述来决定是否将任务委托（Delegate）给该 Agent。建议保持简短明了。
SubAgents() []Agent
//作用：返回直接归属于该 Agent 的子 Agent 列表（切片）。这定义了 Agent 的层级树结构，框架会自动设置父子关系以支持跨 Agent 路由。
Run(InvocationContext) iter.Seq2[*session.Event, error]
//作用：定义该 Agent 的核心执行逻辑。它接收一个 InvocationContext（包含上下文、当前会话、内存、状态等），并返回一个迭代器，该迭代器会持续产出（Yield）执行过程中的事件 (*session.Event) 或报错信息。
```

agent.Config -> 自定义逻辑基础的Agent

```go
Name (string) 和 Description (string)//基础元数据。
SubAgents ([]Agent)//子节点。
BeforeAgentCallbacks / AfterAgentCallbacks//Agent 运行前后的生命周期钩子。
Run (func(InvocationContext) iter.Seq2[*session.Event, error])
//这是最关键的区别。你需要在这提供自定义的 Go 函数来完全掌控它如何工作，不一定是调用大模型，也可以是纯代码逻辑（例如 API 聚合器）。
```

### 首先，我们先创建一个大模型实例

###### 简单示例

```go
import (
    "google.golang.org/adk/agent/llmagent"
    "google.golang.org/adk/provider/googleai" // 引入 GoogleAI Provider
)

// 生成 Model 实例
myGeminiModel, err := googleai.NewModel(googleai.Config{
    ModelName: "gemini-2.5-flash",
    APIKey:    "YOUR_GEMINI_API_KEY",
    // 可以在这里配置 Temperature 等生成参数
})

```

对于模型实例，基本都会有Name、APIKey、Base_URL基本参数和其他参数

而兼容 openai 或其他的请求格式的可以直接使用对应的 provider 包

意思是，可以直接替换key和url,Agent并不知道也不在意底层跑的是GPT、DeepSeek或者豆包kimi

###### 而对于EINO来说

Eino 在核心层定义了一个**统一的 `ChatModel`接口**，业务代码只与这个接口打交道，完全不知道底层具体是哪个模型。这样，业务代码只依赖抽象，不依赖具体实现，eino便实现了统一不同 LLM 提供商的接口（OpenAI、Ark、Claude 等），切换模型无需修改业务代码。

###### 系统提示词参数：

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260407154601909.png" alt="image-20260407154601909" style="zoom:67%;" />

### 关于构建Agent

##### 对于LLM驱动agent

###### 什么是“大模型驱动智能体”? :

LLM 驱动 Agent（大模型驱动的智能体）本质上是一个**能自主理解任务、规划步骤、调用工具并完成复杂目标的“AI 执行者”**。它不再只是和你聊天的“大脑”，而是升级成了能替你干活、有“手”有“脚”的智能助手。

###### 而大模型实例化之后，使用New创建Agent：

New方法是不同大模型框架实现 Agent 的标准创建模式，而具体的包名、方法签名和功能会因框架而异。

**常见参数如下：**

Name 代理的名称（必须是非空字符串）
Model必填，底层模型（比如 gemini-flash2.5）。
Description 代理能力的描述，用一行简短说明即可，LLM 会根据它判断是否把任务交给这个 agent
Instruction / InstructionProvider系统提示词（静态/动态）。
GlobalInstruction / GlobalInstructionProvider 全局指令(静态/动态)
Tools / Toolsets可调用工具集合（静态/动态）。
BeforeModelCallbacks / AfterModelCallbacks模型调用前后拦截（钩子）。
BeforeToolCallbacks / AfterToolCallbacks工具调用前后拦截（钩子）。
GenerateContentConfig 温度、token 上限等生成参数。
InputSchema / OutputSchema 作为工具被调用时的输入输出约束。注意：配置 OutputSchema 后通常会限制 Agent 再去调用其它工具。
SubAgents 子 Agent。
IncludeContents 上下文控制，是否在请求中包含对话历史
DisallowTransferToParent / DisallowTransferToPeers 禁止转移到父/同级 agent

##### 当然，也可以自己写一个Run

###### 若是自己写一个Run，

则需要通过工作流来调用工具，相对可控但固定，
而使用LLM驱动，则可让大模型决定是否调用工具

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260407154107966.png" alt="image-20260407154107966" style="zoom:50%;" />

### 文本处理:

##### 参数微调（处理文本）

对于GenerateContentConfig,在ADK里只做一件事：纯粹的模型超参数微调

**做了什么**：用你的数据继续训练，**改变模型权重**，让模型学会新知识

```go
genConfig := &genai.GenerateContentConfig{
		MaxOutputTokens: maxOutputTokens,
		Temperature:     genai.Ptr[float32](acfg.temperature),
	}

llmagent.New(llmagent.Config{
		Name:                  acfg.name,
		Model:                 m,
		Description:           acfg.description,
		Instruction:           acfg.instruction,
		Tools:                 acfg.tools,
		BeforeToolCallbacks:   []llmagent.BeforeToolCallback{monitor.OnBeforeTool},
		AfterToolCallbacks:    []llmagent.AfterToolCallback{monitor.OnAfterTool},
		GenerateContentConfig: genConfig,
	})

```

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260407155015556.png" alt="image-20260407155015556" style="zoom:67%;" />

##### Schema约束:

**Schema约束**是对数据结构和内容规则的**强制性定义**，确保数据符合预定格式、类型和关系。就像建筑图纸规定房屋结构一样，schema约束规定数据必须遵守的规则。

在部分agent中，可以让你定义InputSchema（输入契约）和OutputSchema（输出强约束）的格式与参数。

O:通过在底层将Go结构体转换为标准的JSON Schema,强制大模型必须按照这个结构输出，然后框架会自动帮你做 json.Unmarshal
I:告诉 Agent（或者调用这个 Agent 的其他父级 Agent），我这个处理节点需要什么样的数据结构。

###### 番外：子Agent

adk-go 采用了一种符合直觉的设计模式：“层级化路由”。adk-go 依赖大模型天然的“工具调用 (Tool Calling)”能力，将子 agent 动态包装成一个tool，以此来实现agent之间的通信。

在 adk-go 的代码里，无论你是哪种父子执行关系，Go 代码的写法都是完全一样的。
决定它们是“路由”、“顺序”还是“并行”的，完全取决于父 Agent 的 Instruction（系统提示词）和子 Agent 的 Description（工具描述）。 提示词就是多智能体协同的控制流。

```go
import (
    "google.golang.org/adk/agent"
    "google.golang.org/adk/agent/llmagent"
)

// 1. OCR 文本提取 Agent
ocrAgent, _ := llmagent.New(llmagent.Config{
    Name:        "OCR_Worker",
    Description: "专门负责从图片中提取文本。当需要知道图片里写了什么时调用我。",
    Model:       myModel,
    Instruction: "你是一个 OCR 助手。解析输入的图片 URL，返回识别到的文本。",
    InputSchema:  ImageInput{},    // { "image_url": "..." }
    OutputSchema: OCRResult{},     // { "text": "..." }
})

// 2. 文本毒性分析 Agent
safetyAgent, _ := llmagent.New(llmagent.Config{
    Name:        "Safety_Worker",
    Description: "专门负责分析文本是否包含违规、引战或有害内容。",
    Model:       myModel,
    Instruction: "你是一个安全审核员。严格判定输入的文本是否违规。",
    InputSchema:  TextAnalyzeInput{}, // { "text": "..." }
    OutputSchema: SafetyResult{},     // { "is_harmful": true, "reason": "..." }
})

// 3. 路由/主管 Agent
managerAgent, _ := llmagent.New(llmagent.Config{
    Name:  "Moderation_Manager",
    Model: myModel,
    
    // 明确告诉主管它的工作流
    Instruction: `你是多模态审核主管。
    收到图片后，请按以下步骤执行：
    1. 调用 OCR_Worker 获取图片中的文字。
    2. 将获取到的文字，传给 Safety_Worker 进行毒性分析。
    3. 综合所有的信息，生成最终的审核报告。`,
    
    // 【魔法发生的地方】
    SubAgents: []agent.Agent{ocrAgent, safetyAgent}, 
    
    OutputSchema: FinalReport{}, 
})

```









