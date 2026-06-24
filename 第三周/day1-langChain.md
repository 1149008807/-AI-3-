# LangChain v1.x 学习总结

## 概述

LangChain v1 版本进行了重大架构重构，从单体包改为模块化设计，许多旧版 API 已废弃或移除。

---

## 核心概念

### 1. 模型调用 (ChatOpenAI)

```javascript
import { ChatOpenAI } from "@langchain/openai";

const model = new ChatOpenAI({
    model: "qwen3.6-flash",
    temperature: 0.7,
    apiKey: process.env.DASHSCOPE_API_KEY,
    configuration: {
        baseURL: "https://dashscope.aliyuncs.com/compatible-mode/v1"
    }
});
```

**要点：**
- `apiKey`：API 密钥，支持环境变量 `process.env.DASHSCOPE_API_KEY`
- `configuration`：自定义配置，主要用于设置 `baseURL`
- 支持阿里云 DashScope、OpenAI 等多种兼容 OpenAI API 的后端

### 2. 消息类型

```javascript
import { HumanMessage, AIMessage, SystemMessage } from "@langchain/core/messages";

const messages = [
    new SystemMessage("你是一个有帮助的助手"),
    new HumanMessage("你好"),
    new AIMessage("你好！有什么可以帮你的？")
];
```

**消息类型：**
| 类型 | 用途 | 旧版名称 |
|------|------|----------|
| `HumanMessage` | 用户消息 | `HumanMessage` |
| `AIMessage` | AI 回复 | `AIMessage` |
| `SystemMessage` | 系统提示 | `SystemMessage` |
| `ToolMessage` | 工具返回结果 | - |

### 3. 模型调用

```javascript
const res = await model.invoke(messages);
console.log(res.content);  // 获取文本内容
```

**返回的 `AIMessage` 对象属性：**
- `content`：回复文本内容
- `tool_calls`：工具调用数组（如果有）
- `additional_kwargs`：额外参数

---

## 对话历史管理

### 新版方案

#### 1. MemorySaver (LangGraph)

```javascript
import { MemorySaver } from "@langchain/langgraph";

const checkpointer = new MemorySaver();

const model = new ChatOpenAI({
    model: "qwen3.6-flash",
    checkpointer  // 自动保存对话历史
});
```

**特点：**
- 需安装 `@langchain/langgraph`
- 自动持久化对话上下文
- 支持有状态的多轮对话

#### 2. 手动管理消息数组

```javascript
const messageHistory = [];

// 每次对话时构建消息数组
const messages = [
    new SystemMessage(SYSTEM_PROMPT),
    ...messageHistory,
    new HumanMessage('用户输入')
];

const res = await model.invoke(messages);

// 添加到历史
messageHistory.push(new HumanMessage('用户输入'));
messageHistory.push(res);
```

**适用场景：** 简单对话，无需复杂状态管理

---

## 已废弃/移除的 API

| 旧版 | 新版方案 |
|------|----------|
| `import { BufferMemory } from "langchain/memory"` | `@langchain/community/memory/chat_memory` 或手动管理 |
| `import { ConversationChain } from "langchain/chains"` | 直接使用 `model.invoke()` |
| `import { LLMChain } from "langchain/chains"` | 使用 `RunnableSequence` |

---

## 模块导出位置

| 功能 | 导入路径 |
|------|----------|
| 模型 | `@langchain/openai` |
| 消息类型 | `@langchain/core/messages` |
| 工具 | `@langchain/core/tools` |
| MemorySaver | `@langchain/langgraph` |
| BufferMemory | `@langchain/community/memory/chat_memory` |
| 输出解析 | `@langchain/core/output_parsers` |

---

## 示例代码

### 基础对话

```javascript
import { ChatOpenAI } from "@langchain/openai";
import { HumanMessage, SystemMessage } from "@langchain/core/messages";

const model = new ChatOpenAI({
    model: "qwen3.6-flash",
    apiKey: process.env.DASHSCOPE_API_KEY,
    configuration: {
        baseURL: "https://dashscope.aliyuncs.com/compatible-mode/v1"
    }
});

const messages = [
    new SystemMessage("你是一个有帮助的助手"),
    new HumanMessage("你好")
];

const res = await model.invoke(messages);
console.log(res.content);
```

### 带历史记录的多轮对话

```javascript
import { MemorySaver } from "@langchain/langgraph";
import { createReactAgent } from "@langchain/langgraph/prebuilt";

const checkpointer = new MemorySaver();

const model = new ChatOpenAI({
    model: "qwen3.6-flash",
    apiKey: process.env.DASHSCOPE_API_KEY,
    configuration: {
        baseURL: "https://dashscope.aliyuncs.com/compatible-mode/v1"
    },
    checkpointer
});

// 使用 thread_id 来区分不同会话
const config = { configurable: { thread_id: "session-123" } };

const res = await model.invoke(
    [new HumanMessage("你好")],
    config
);
```

---

## 依赖安装

```bash
# 核心包
npm install langchain @langchain/core @langchain/openai

# 社区模块（包含 BufferMemory 等）
npm install @langchain/community

# LangGraph（包含 MemorySaver）
npm install @langchain/langgraph
```

---

## 注意事项

1. **版本兼容性**：LangChain v1.x 变化较大，旧代码可能需要迁移
2. **模块化架构**：不同功能分布在不同包中，按需安装
3. **API 兼容性**：支持 OpenAI 兼容的任何 API（DashScope、Azure OpenAI 等）
4. **消息格式**：新版统一使用消息对象而非原始 JSON

---

## 参考链接

- [LangChain 官方文档](https://js.langchain.com/docs/)
- [LangChain 中文文档](https://langchain-doc.cn/v1/python/langchain/short-term-memory.html)
- [LangGraph 文档](https://langchain-ai.github.io/langgraphjs/)
- [DashScope API 文档](https://help.aliyun.com/zh/model-studio/developer-reference/compatibility-of-openai-with-dashscope)