# 第26章：实战项目二 — 全栈 Todo 应用

## 项目介绍：从"看得见"到"存得住"

上一章的博客项目，主要是**前端**（Frontend）的工作——把内容漂亮地展示出来。但你有没有想过：当用户在网页上填了一个表单、创建了一条待办事项，这些数据去了哪里？

这就引出了**全栈开发**（Full-stack Development）的概念。如果把一家餐厅作为比喻：

- **前端**就是餐厅的大堂——装修、菜单、服务员，是顾客直接看到和接触的部分
- **后端**（Backend）就是厨房——接收订单、烹饪菜品、管理食材，顾客看不到但离不开
- **数据库**（Database）就是仓库——食材存放的地方，需要时随时取用

这一章，我们要做一个**全栈 Todo 应用**——前端让用户看到和操作待办事项，后端处理数据逻辑，数据库把数据安全地存起来。

---

## 技术选型（Tech Stack）

| 技术 | 角色 | 类比 |
|---|---|---|
| **React** | 前端框架 | 餐厅大堂的装修和布局 |
| **Node.js + Express** | 后端框架 | 厨房的工作台和工具 |
| **SQLite**（开发）/ **PostgreSQL**（生产） | 数据库 | 食材仓库 |
| **JWT** | 身份认证（Authentication） | 顾客的会员卡 |
| **Railway** / **Render** | 后端部署 | 把厨房搬到商业街上 |
| **Vercel** | 前端部署 | 开一家真正的门店 |

> **为什么用 SQLite 开发，PostgreSQL 上线？** SQLite 像一个小笔记本，数据直接存在一个文件里，不需要安装任何东西，开发时最方便。PostgreSQL 像一个专业的档案柜，适合在生产环境（Production Environment）中使用，性能更好、更安全。

---

## 功能清单

用上一章学到的 MVP 思维，我们把功能分成两期：

**第一期（MVP）：**
- 用户注册和登录（Registration & Login）
- 创建、查看、编辑、删除待办事项（CRUD）
- 标记完成/未完成

**第二期（进阶）：**
- 分类和标签（Categories & Tags）
- 按状态筛选（全部/进行中/已完成）
- 截止日期和提醒

---

## 分步实现：前后端协同开发

### 第一步：设计 API 端点（API Endpoints Design）

在动手写代码之前，先规划好前端和后端之间的"通信协议"。API（Application Programming Interface，应用程序接口）就像餐厅的点菜单——前端通过 API 告诉后端"我要什么"，后端返回对应的数据。

向 AI 请求帮助：

```
我要做一个 Todo 应用，需要支持用户注册/登录和待办事项的增删改查。
请帮我设计 RESTful API 端点，包括：
- 路径（URL path）
- HTTP 方法（GET/POST/PUT/DELETE）
- 请求参数
- 返回数据格式
```

AI 会生成类似这样的设计：

```
用户相关：
POST   /api/auth/register   - 注册新用户
POST   /api/auth/login      - 用户登录，返回 JWT token

待办事项相关（需要登录）：
GET    /api/todos           - 获取当前用户的所有 todo
POST   /api/todos           - 创建新 todo
PUT    /api/todos/:id       - 更新指定 todo
DELETE /api/todos/:id       - 删除指定 todo
```

把这个想象成快递公司的运单系统：每个"端点"就是一个服务窗口，`GET` 是"我要查快递"，`POST` 是"我要寄快递"，`PUT` 是"我要改地址"，`DELETE` 是"我要取消订单"。

### 第二步：用 AI 搭建后端（Build Backend）

**初始化后端项目：**

```bash
mkdir todo-backend
cd todo-backend
npm init -y
npm install express cors better-sqlite3 bcryptjs jsonwebtoken dotenv
```

每个包（package）的作用：
- `express`：后端框架，帮你处理 HTTP 请求（就像一个管家，帮你接待客人）
- `cors`：允许前端跨域访问（让不同地址的前端和后端能对话）
- `better-sqlite3`：操作 SQLite 数据库
- `bcryptjs`：加密用户密码（把密码变成一团乱码，即使数据库被偷也看不到原始密码）
- `jsonwebtoken`：生成和验证 JWT（用户的"数字身份证"）
- `dotenv`：管理环境变量（存放密钥等敏感信息）

**让 AI 帮你写后端代码：**

```
请帮我用 Express 创建一个 Todo 应用的后端，包含以下文件：
1. server.js - 主入口，配置 Express 和中间件
2. db.js - 数据库初始化，创建 users 和 todos 表
3. routes/auth.js - 注册和登录的路由
4. routes/todos.js - Todo 增删改查的路由
5. middleware/auth.js - JWT 验证中间件

数据库用 SQLite（better-sqlite3），密码用 bcryptjs 加密。
请在关键代码处加上中文注释。
```

AI 会为你生成完整的后端代码。这里看一个简化版的 Todo 路由：

```javascript
// routes/todos.js
const express = require('express');
const router = express.Router();
const db = require('../db');
const authMiddleware = require('../middleware/auth');

// 所有 todo 路由都需要登录验证
router.use(authMiddleware);

// 获取当前用户的所有 todo
router.get('/', (req, res) => {
  const todos = db.prepare(
    'SELECT * FROM todos WHERE user_id = ? ORDER BY created_at DESC'
  ).all(req.userId);  // req.userId 由 auth 中间件注入
  res.json(todos);
});

// 创建新 todo
router.post('/', (req, res) => {
  const { title, category } = req.body;
  const result = db.prepare(
    'INSERT INTO todos (title, category, user_id) VALUES (?, ?, ?)'
  ).run(title, category || '默认', req.userId);
  res.json({ id: result.lastInsertRowid, title, completed: false });
});
```

**测试你的 API：**

启动后端后，用 AI 帮你测试：

```
我的后端已经在 localhost:3000 运行了。
请给我几条 curl 命令来测试：
1. 注册一个新用户
2. 登录获取 token
3. 用 token 创建一条 todo
4. 获取所有 todo
```

### 第三步：用 AI 搭建前端（Build Frontend）

```bash
npm create vite@latest todo-frontend -- --template react
cd todo-frontend
npm install axios react-router-dom
npm install -D tailwindcss @tailwindcss/vite
```

向 AI 描述你要的界面：

```
请帮我用 React + Tailwind CSS 创建 Todo 应用的前端，包含：
1. 登录/注册页面
2. Todo 列表主页面，包含：
   - 顶部：添加新 todo 的输入框和按钮
   - 中间：todo 列表，每条可以勾选完成、编辑、删除
   - 筛选栏：全部/进行中/已完成
3. 使用 axios 调用后端 API
4. 把 JWT token 存在 localStorage 里

请用组件化的方式组织代码，每个功能一个组件。
```

AI 会生成一系列组件文件。核心的 Todo 列表组件大概长这样：

```jsx
// components/TodoList.jsx
import { useState, useEffect } from 'react';
import api from '../utils/api';  // 封装了 axios 的工具

function TodoList() {
  const [todos, setTodos] = useState([]);      // 待办事项列表
  const [newTodo, setNewTodo] = useState('');   // 输入框内容
  const [filter, setFilter] = useState('all');  // 筛选条件

  // 组件加载时获取 todo 列表
  useEffect(() => {
    fetchTodos();
  }, []);

  const fetchTodos = async () => {
    const response = await api.get('/todos');
    setTodos(response.data);
  };

  const addTodo = async () => {
    if (!newTodo.trim()) return;  // 不允许空内容
    await api.post('/todos', { title: newTodo });
    setNewTodo('');  // 清空输入框
    fetchTodos();    // 刷新列表
  };

  // ... 省略其他操作

  return (
    <div className="max-w-2xl mx-auto p-6">
      {/* 添加新 todo */}
      <div className="flex gap-2 mb-6">
        <input
          className="flex-1 border rounded-lg px-4 py-2"
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          placeholder="添加新的待办事项..."
          onKeyDown={(e) => e.key === 'Enter' && addTodo()}
        />
        <button
          onClick={addTodo}
          className="bg-blue-500 text-white px-6 py-2 rounded-lg"
        >
          添加
        </button>
      </div>

      {/* Todo 列表 */}
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} onUpdate={fetchTodos} />
      ))}
    </div>
  );
}
```

### 第四步：前后端联调（Connect Frontend to Backend）

这是全栈开发中最关键的环节——让前端和后端真正"对话"起来。就像接通水管，水龙头（前端）和水塔（后端）之间需要管道（API 调用）。

创建一个 API 工具文件：

```
请帮我创建一个 axios 实例（instance），功能包括：
1. 基础 URL 指向后端地址
2. 自动在请求头（header）里带上 JWT token
3. 如果 token 过期（401 错误），自动跳转到登录页
```

AI 会生成：

```javascript
// utils/api.js
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:3000/api',
});

// 请求拦截器：每次请求自动带上 token
api.interceptors.request.use(config => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// 响应拦截器：处理认证失败
api.interceptors.response.use(
  response => response,
  error => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### 第五步：添加用户认证（Authentication）

用户认证的流程可以类比为进入一栋大楼：

1. **注册**（Register）= 在前台办理门禁卡
2. **登录**（Login）= 刷卡通过闸机，拿到一张临时通行证（JWT Token）
3. **访问受保护的资源** = 用通行证进入各个房间
4. **Token 过期** = 通行证到期了，需要重新刷卡（重新登录）

向 AI 请求完整的认证流程：

```
请帮我实现 Todo 应用的完整登录注册流程：
1. 注册页面：用户名、邮箱、密码、确认密码
2. 登录页面：邮箱、密码
3. 登录成功后跳转到 Todo 主页
4. 未登录时访问主页自动跳转到登录页
5. 有一个退出登录按钮

使用 React Router 管理路由，JWT 存在 localStorage。
```

### 第六步：部署上线（Deploy Both Services）

全栈项目需要部署两个服务——这就像开一家餐厅，你需要找到两个地方：一个给顾客坐的大堂（前端），一个做菜的厨房（后端）。

**部署后端到 Railway：**

```
我的 Express + SQLite 后端项目已经完成。
请告诉我如何部署到 Railway：
1. 需要修改什么配置？
2. 环境变量（environment variables）怎么设置？
3. 数据库从 SQLite 切换到 PostgreSQL 需要改什么？
```

AI 会指导你：
- 注册 Railway 账号，创建新项目
- 添加 PostgreSQL 数据库服务
- 设置环境变量（`JWT_SECRET`、`DATABASE_URL` 等）
- 推送代码自动部署

**部署前端到 Vercel：**

和上一章一样，把前端代码推到 GitHub，然后在 Vercel 导入。唯一需要注意的是把 API 地址从 `localhost:3000` 改成 Railway 给你的真实地址。

---

## 关键知识：全栈数据流（Full-stack Data Flow）

理解数据在全栈应用中的流动，是这个项目最重要的收获。让我们追踪一下"创建一条 todo"的完整旅程：

```
1. 用户在输入框输入"买牛奶"，点击"添加"按钮
          ↓
2. React 组件调用 api.post('/todos', { title: '买牛奶' })
          ↓
3. Axios 自动在请求头加上 JWT Token，发送 HTTP POST 请求
          ↓
4. Express 收到请求，auth 中间件验证 Token，确认是用户 A
          ↓
5. Todo 路由把数据写入数据库：
   INSERT INTO todos (title, user_id) VALUES ('买牛奶', 'A')
          ↓
6. 数据库返回新创建的记录（包含自动生成的 ID）
          ↓
7. Express 把结果以 JSON 格式返回给前端
          ↓
8. React 收到响应，更新页面上的 todo 列表
          ↓
9. 用户看到"买牛奶"出现在列表中 ✓
```

这就是一个请求的完整生命周期。前端是用户的"代言人"，后端是"执行者"，数据库是"记忆库"。三者协同工作，才能完成一个看似简单的操作。

---

## 常见问题与排错

当你遇到问题时，把错误信息完整地告诉 AI：

```
我的 Todo 应用前端运行正常，但调用后端 API 时报错：
"Access to XMLHttpRequest has been blocked by CORS policy"
请问这是什么问题，怎么解决？
```

常见问题汇总：

| 问题 | 可能原因 | 解决方向 |
|---|---|---|
| CORS 错误 | 后端没配置 CORS 或地址不对 | 检查 `cors()` 中间件配置 |
| 401 Unauthorized | Token 过期或格式错误 | 检查 Token 是否正确传递 |
| 500 Server Error | 后端代码报错 | 查看后端控制台的错误日志 |
| 数据不显示 | API 返回格式和前端期望不一致 | 用浏览器开发工具（DevTools）的 Network 面板查看实际返回的数据 |

---

## 本章小结

这一章你完成了一个真正的全栈项目，掌握了以下核心技能：

1. **API 设计**：学会了 RESTful 风格的接口设计，理解了前后端的"通信协议"
2. **后端开发**：用 Express 处理请求，用 SQLite 存储数据
3. **前端开发**：用 React 构建交互界面，用 Axios 调用 API
4. **前后端联调**：理解了完整的数据流转过程
5. **用户认证**：实现了 JWT 登录系统
6. **全栈部署**：前端和后端分别部署到不同平台

全栈开发就像学会了做一道完整的菜——从采购食材（数据库），到在厨房加工（后端），到精美地摆盘端上桌（前端）。每一个环节都不可或缺。

下一章，我们将迎来最激动人心的项目——用 AI API 做一个真正"智能"的工具！
