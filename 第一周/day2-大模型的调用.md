## 大模型调用，nodeJs
    1、大模型用的是阿里的免费模型
 ```javascript
    // Please install OpenAI SDK first: `npm install openai`

    import OpenAI from "openai";

    const openai = new OpenAI({
            baseURL: 'https://api.deepseek.com',
            apiKey: process.env.DEEPSEEK_API_KEY,
    });

    async function main() {
    const completion = await openai.chat.completions.create({
        messages: [{ role: "system", content: "You are a helpful assistant." }],
        model: "deepseek-v4-pro",
        thinking: {"type": "enabled"},
        reasoning_effort: "high",
        stream: false,
    });

    console.log(completion.choices[0].message.content);
    }

    main();
```
    具体参数详情可以参考 https://api-docs.deepseek.com/zh-cn/api/create-chat-completion

    2、tool工具定义的需要传入对应的ID

## 消息结构
``` json
const messages = [
    { role: "system", content: "You are a helpful assistant.,回答时请用中文回答" },
    {role: "user", content: "你是谁？"},
]
```

## 多轮试对话
``` json
    [
        {"role": "user", "content": "What's the highest mountain in the world?"},
        {"role": "assistant", "content": "The highest mountain in the world is Mount Everest."},
        {"role": "user", "content": "What is the second?"}
    ]
```