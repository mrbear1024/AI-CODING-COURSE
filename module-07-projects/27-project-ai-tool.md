# 第27章：实战项目三 — AI 驱动的小工具

## 项目介绍：让你的应用拥有"大脑"

前两个项目里，我们做的应用就像一台"计算器"——你给它指令，它精确执行。但现在，我们要做一个能"思考"的应用。

想象一下：你做了一个网站，用户粘贴一篇长文章进去，点击按钮，几秒钟后就能得到一份精炼的摘要。或者用户用中文描述一个需求，应用自动生成对应的代码。这就是 **AI 驱动的应用**（AI-powered Application）。

这背后的秘密很简单——你的应用通过 **API**（Application Programming Interface，应用程序接口）调用了一个强大的 AI 模型，就像你打电话给一个专家咨询问题，专家给你答复，你再把答复转达给用户。

这一章，我们将做一个**智能文档助手**——用户输入或上传文本，AI 可以帮助总结、翻译、改写、提问。你可以把它改造成任何你想要的 AI 工具。

---

## 创意灵感：你可以做什么？

在开始之前，看看这些 AI 工具的点子，选一个最吸引你的：

| 工具类型 | 功能描述 | 难度 |
|---|---|---|
| **文档助手** | 总结、翻译、改写文本 | 入门 |
| **代码解释器** | 粘贴代码，AI 用大白话解释 | 入门 |
| **内容生成器** | 根据主题生成文章大纲或草稿 | 中等 |
| **数据分析助手** | 上传 CSV，AI 帮你分析数据趋势 | 中等 |
| **智能聊天机器人** | 基于特定知识库的问答机器人 | 中等 |
| **多模态工具** | 上传图片，AI 描述内容或提取文字 | 进阶 |

本章以**文档助手**为例，但整个架构（Architecture）适用于以上所有类型。

---

## 技术选型（Tech Stack）

| 技术 | 角色 | 为什么选它 |
|---|---|---|
| **Python + FastAPI** | 后端框架 | Python 是 AI 生态最丰富的语言，FastAPI 简洁高效 |
| **Claude API / OpenAI API** | AI 能力提供方 | 调用它就像"请一个 AI 专家当顾问" |
| **React** 或纯 **HTML/JS** | 前端界面 | 保持简单，专注于 AI 集成 |
| **Vercel + Railway** | 部署 | 前后端分离部署 |

> **也可以用 Node.js！** 如果你更熟悉 JavaScript，完全可以把后端换成 Node.js + Express。AI API 的 SDK（软件开发工具包）同时支持 Python 和 JavaScript。

---

## 分步实现

### 第一步：获取 API 密钥（Get API Keys）

调用 AI API 需要一把"钥匙"——**API Key**。这就像你要使用一个付费服务，需要先注册账号并获得访问权限。

**获取 Claude API Key：**
1. 访问 [console.anthropic.com](https://console.anthropic.com)
2. 注册账号
3. 在 "API Keys" 页面创建一个新的密钥
4. 把密钥复制下来，妥善保存

**获取 OpenAI API Key：**
1. 访问 [platform.openai.com](https://platform.openai.com)
2. 注册账号
3. 在 "API Keys" 页面创建密钥

> **重要安全提醒**：API Key 就像你的银行卡密码，**绝对不能**放在前端代码里，**绝对不能**上传到 GitHub。要把它存在环境变量（Environment Variable）里，后面会教你怎么做。

把密钥存在 `.env` 文件中：

```
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxx
```

然后确保 `.gitignore` 文件中有 `.env` 这一行，防止密钥被意外上传。

### 第二步：设计工具的功能（Design Functionality）

我们的文档助手提供四个核心功能：

1. **总结**（Summarize）：把长文章压缩成几句话
2. **翻译**（Translate）：中英互译
3. **改写**（Rewrite）：换一种风格重写（正式、口语、简洁……）
4. **提问**（Ask）：针对文档内容提问，AI 回答

向 AI 规划具体的交互流程：

```
我想做一个文档助手工具，用户流程是：
1. 用户在文本框中粘贴一段文字
2. 选择要执行的操作（总结/翻译/改写/提问）
3. 如果选"提问"，还需要输入问题
4. 点击"执行"按钮
5. AI 的回复以流式（streaming）方式逐字显示

请帮我设计后端 API 和前端页面的详细方案。
```

### 第三步：搭建调用 AI API 的后端（Build Backend）

**初始化项目：**

```bash
mkdir ai-doc-assistant
cd ai-doc-assistant

# 创建 Python 虚拟环境
python -m venv venv
source venv/bin/activate  # Mac/Linux
# venv\Scripts\activate   # Windows

# 安装依赖
pip install fastapi uvicorn anthropic python-dotenv
```

**核心代码——调用 Claude API：**

让 AI 帮你写后端：

```
请用 FastAPI 创建一个后端服务，实现以下功能：
1. POST /api/summarize - 接收文本，返回 AI 生成的摘要
2. POST /api/translate - 接收文本和目标语言，返回翻译结果
3. POST /api/rewrite - 接收文本和目标风格，返回改写结果
4. POST /api/ask - 接收文本和问题，返回 AI 的回答
5. 所有端点都支持流式响应（streaming response）

使用 Anthropic Python SDK 调用 Claude API。
API Key 从环境变量读取。
```

AI 会生成类似这样的代码：

```python
# main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from fastapi.responses import StreamingResponse
from pydantic import BaseModel
import anthropic
import os
from dotenv import load_dotenv

load_dotenv()  # 从 .env 文件加载环境变量

app = FastAPI()

# 允许前端跨域访问
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

# 初始化 Claude 客户端
client = anthropic.Anthropic(
    api_key=os.getenv("ANTHROPIC_API_KEY")
)

class SummarizeRequest(BaseModel):
    text: str
    language: str = "中文"

class AskRequest(BaseModel):
    text: str
    question: str

@app.post("/api/summarize")
async def summarize(request: SummarizeRequest):
    """总结文本——就像让一个读书很快的朋友帮你概括要点"""

    async def generate():
        with client.messages.stream(
            model="claude-sonnet-4-20250514",
            max_tokens=1024,
            messages=[{
                "role": "user",
                "content": f"请用{request.language}总结以下文本的要点，"
                           f"保持简洁清晰：\n\n{request.text}"
            }]
        ) as stream:
            for text in stream.text_stream:
                yield text

    return StreamingResponse(generate(), media_type="text/plain")

@app.post("/api/ask")
async def ask(request: AskRequest):
    """基于文档回答问题——就像一个读完文章的助手回答你的疑问"""

    async def generate():
        with client.messages.stream(
            model="claude-sonnet-4-20250514",
            max_tokens=1024,
            messages=[{
                "role": "user",
                "content": f"基于以下文本回答问题。如果文本中没有相关信息，"
                           f"请如实说明。\n\n文本：{request.text}\n\n"
                           f"问题：{request.question}"
            }]
        ) as stream:
            for text in stream.text_stream:
                yield text

    return StreamingResponse(generate(), media_type="text/plain")
```

启动后端：

```bash
uvicorn main:app --reload --port 8000
```

### 第四步：创建用户友好的前端（Build Frontend）

前端的任务是让用户方便地输入文本、选择操作、查看 AI 的回复。

```
请帮我创建一个简洁美观的前端页面（HTML + Tailwind CSS + JavaScript），功能：
1. 左边是输入区域：一个大文本框 + 操作选择（总结/翻译/改写/提问）
2. 右边是输出区域：显示 AI 的回复
3. 支持流式显示——AI 的回复逐字出现，像打字一样
4. 有一个"清空"按钮重置页面
5. 操作进行中显示加载动画

页面布局要响应式，手机上左右变上下排列。
```

AI 会生成一个完整的页面。核心的流式请求（Streaming Request）代码长这样：

```javascript
async function callAI(endpoint, data) {
  const outputEl = document.getElementById('output');
  outputEl.textContent = '';  // 清空旧内容

  // 使用 fetch API 发送请求
  const response = await fetch(`http://localhost:8000/api/${endpoint}`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
  });

  // 读取流式响应——就像水龙头里的水，一点一点流出来
  const reader = response.body.getReader();
  const decoder = new TextDecoder();

  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    // 每收到一小段文字，就追加到页面上
    outputEl.textContent += decoder.decode(value);
  }
}
```

**流式响应**（Streaming Response）是这个项目的亮点。没有流式响应时，用户需要等 AI 完全生成后才能看到结果（可能要等 10-20 秒，体验很差）。有了流式响应，文字像打字一样逐渐出现，用户立即能看到"AI 正在思考"。

这就像去餐厅吃饭——流式响应是"做好一道上一道"，非流式是"全部做好了再一起端上来"。

### 第五步：处理流式响应的细节（Handle Streaming）

流式响应有几个需要注意的细节，让 AI 帮你处理：

```
我的 AI 文档助手已经能基本工作了，但有几个体验问题：
1. 流式输出时，如果用户中途点击"取消"，请求没有中断
2. 网络错误时没有提示
3. AI 输出的 Markdown 格式（加粗、列表等）没有渲染

请帮我处理这些问题。
```

AI 会帮你添加：

- **AbortController** 来支持取消请求
- **try-catch** 错误处理和用户友好的错误提示
- **Markdown 渲染**（可以用 `marked` 库把 Markdown 转成 HTML）

### 第六步：部署上线（Deploy）

**后端部署到 Railway：**

```bash
# 创建 requirements.txt（Python 的依赖清单）
pip freeze > requirements.txt
```

```
我的 FastAPI 项目需要部署到 Railway。
请帮我创建：
1. Procfile（告诉 Railway 怎么启动应用）
2. 需要配置的环境变量列表
3. 部署步骤说明
```

Railway 会给你一个类似 `ai-doc-assistant.up.railway.app` 的地址。

**前端部署到 Vercel：**

把前端代码中的 API 地址从 `localhost:8000` 改成 Railway 的地址，然后按照之前学的方法部署到 Vercel。

---

## 安全与成本注意事项

### API 密钥安全

```
✅ 正确做法：
- API Key 存在服务器端的环境变量里
- 用户请求 → 你的后端 → AI API（用户永远看不到你的 Key）

❌ 错误做法：
- 把 API Key 写在前端 JavaScript 里
- 把 API Key 提交到 GitHub
  （有人专门扫描 GitHub 偷 API Key，你可能第二天就收到一大笔账单）
```

### 费用控制

AI API 是按使用量收费的（通常按 Token 数量，Token 可以理解为"字词片段"）。为了避免意外的高额费用：

1. **设置用量限制**（Usage Limit）：在 API 平台的控制台设置每月最高消费
2. **限制单次输入长度**：在后端代码中限制用户输入的文本长度
3. **添加频率限制**（Rate Limiting）：防止用户短时间内疯狂调用

向 AI 请求帮助：

```
请帮我在 FastAPI 后端添加以下安全措施：
1. 限制输入文本最长 10000 字符
2. 每个 IP 每分钟最多请求 10 次
3. 记录每次调用的 token 使用量
```

---

## 进阶：让你的工具更强大

一旦基础版本运行起来了，你可以尝试这些进阶功能：

- **对话历史**（Conversation History）：让 AI 记住之前的对话内容，可以追问
- **Prompt 模板**（Prompt Template）：预设不同场景的提示词，用户一键切换
- **文件上传**：支持上传 PDF、Word 文档，自动提取文字
- **多模型支持**：同时接入 Claude 和 GPT，让用户选择
- **收藏和历史**：把 AI 的回复保存下来，方便回看

每一个进阶功能都可以让 AI 帮你实现。关键是先让 MVP 跑起来，再逐步添加。

---

## 本章小结

这一章你完成了一个真正 AI 驱动的应用，核心收获包括：

1. **API 密钥管理**：学会了安全地获取和使用 AI API Key
2. **Prompt 设计**：通过不同的提示词（Prompt）让同一个 AI 模型完成不同任务
3. **流式响应**：实现了打字机效果的实时输出，大幅提升用户体验
4. **后端集成**：学会了用 Python/FastAPI 作为"中间人"，连接前端用户和 AI API
5. **安全意识**：理解了 API Key 保护和费用控制的重要性
6. **完整部署**：将 AI 工具发布到互联网上

回顾这三个实战项目的进阶过程：

```
第25章 博客网站   → 纯前端，展示静态内容
第26章 Todo 应用  → 全栈开发，前后端数据交互
第27章 AI 工具    → 全栈 + AI API 集成，应用有了"智能"
```

你已经从一个编程零基础的学习者，成长为能够独立构建并部署 AI 驱动应用的开发者。这不是终点，而是一个新的起点。AI 的世界每天都在变化，但你已经掌握了最核心的能力——**把想法变成现实的能力**。

继续用 AI 作为你的编程伙伴，去构建更多有趣、有用的工具吧！
