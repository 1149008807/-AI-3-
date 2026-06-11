# AI 助手 - 功能使用文档

## 项目简介

基于 Express + 通义千问 API 的 AI 对话系统，提供类 ChatGPT 的 Web 聊天界面。

---

## 环境要求

- Node.js >= 18
- 通义千问 API 密钥（阿里云 DashScope）

---

## 快速开始

### 1. 安装依赖

```bash
npm install
```

### 2. 配置环境变量

创建 `.env` 文件（或直接在终端设置）：

```bash
set DASHSCOPE_API_KEY=你的API密钥
```

### 3. 启动服务

```bash
node server.js
```

服务运行在 `http://localhost:3000`

### 4. 打开页面

浏览器访问 `http://localhost:3000`

---

## 项目结构

```
├── server.js          # Express 服务入口，中间件与路由注册
├── index.html         # 聊天界面（前端）
├── chatApi.js         # 聊天 API 路由（/api/chat）
├── chatModel.js       # LLM 模型封装（通义千问）
├── request.js         # HTTP 请求封装（axios）
├── package.json       # 项目配置与依赖
```

---

## 功能说明

### 前端界面

- **消息展示**：用户消息靠右（灰色气泡），AI 回复靠左（深绿气泡），带圆形头像
- **发送消息**：输入框输入后点击发送按钮或按 Enter 键
- **加载状态**：AI 回复时显示三点跳动动画，按钮和输入框禁用
- **空状态提示**：无消息时显示"有什么可以帮助你的？"

### 后端 API

**接口地址**：`POST /api/chat`

**请求体**：

```json
{
  "message": "你的问题"
}
```

**成功响应**：

```json
{
  "code": 200,
  "data": "AI 的回答内容",
  "msg": "成功"
}
```

**失败响应**：

```json
{ "code": 400, "msg": "请输入问题" }
{ "code": 500, "msg": "请求失败" }
```

---

## 配置项

### 模型配置（chatModel.js）

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `model` | `qwen3.6-flash` | 通义千问模型名称 |
| `thinking` | `enabled` | 思考模式 |
| `reasoning_effort` | `high` | 推理深度 |

### 服务配置（server.js）

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `PORT` | `3000` | 服务端口 |

---

## 常见问题

1. **页面打开空白或 404**：确认 `server.js` 已启动，访问 `http://localhost:3000`
2. **发送消息后无回复**：检查 `DASHSCOPE_API_KEY` 环境变量是否设置正确
3. **网络错误提示**：确认 `index.html` 中 `fetch` 地址与后端端口一致
