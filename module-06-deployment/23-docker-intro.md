# 第23章：Docker 入门（可选进阶）

## 概念讲解

### 为什么需要 Docker？

你有没有遇到过这种情况？一段代码在你电脑上运行得好好的，发给同事就报错了。同事说："我这儿跑不起来啊！"你一脸无辜："在我的机器上明明可以的！（It works on my machine!）"

这个问题的根源是：**每台电脑的运行环境不同**——操作系统版本不同、安装的软件版本不同、配置不同。就像同样一道菜，在你家厨房做出来味道刚好，但换了一个厨房，火候不同、锅不同、调料品牌不同，味道就变了。

**Docker 的解决方案**：把你的"厨房"一起打包带走。不仅打包菜谱（代码），还打包厨房的所有设备和调料（运行环境）。这样无论搬到哪里，做出来的菜味道都一模一样。

### 容器（Container）vs 虚拟机（Virtual Machine）

Docker 使用的核心概念是**容器（Container）**。为了理解容器，我们先说说**虚拟机（Virtual Machine, VM）**。

**虚拟机**就像在一栋大楼里隔出几间完全独立的公寓——每间公寓都有自己的水电系统、自己的地基。虽然隔离性很好，但非常"重"——每个虚拟机都要运行一个完整的操作系统，动辄占用几个 GB 的内存。

**容器**则像是同一间大公寓里用隔断隔出的工位——大家共享水电（操作系统内核），但各自有独立的工作空间。容器比虚拟机**轻量得多**，启动只需要几秒钟。

```
虚拟机（VM）：                    容器（Container）：
┌──────────────────────┐          ┌──────────────────────┐
│  App A  │  App B     │          │  App A  │  App B     │
│─────────│────────────│          │─────────│────────────│
│  OS     │  OS        │          │  依赖    │  依赖      │
│─────────│────────────│          │─────────┴────────────│
│     虚拟化层          │          │     Docker 引擎       │
│──────────────────────│          │──────────────────────│
│     宿主操作系统       │          │     宿主操作系统       │
└──────────────────────┘          └──────────────────────┘

每个 VM 有完整 OS（很重）         容器共享宿主 OS（很轻）
```

### Dockerfile：你的"菜谱"

**Dockerfile** 是一个文本文件，描述了如何构建你的应用环境。它就像一份详细的菜谱，Docker 按照这个菜谱一步步"做菜"。

来看一个 Node.js 项目的 Dockerfile 示例：

```dockerfile
# 第一步：选择基础食材（基础镜像）
# 就像做菜前先选好"用什么锅"
FROM node:18-alpine

# 第二步：设定工作台位置
WORKDIR /app

# 第三步：先把食材清单复制过来（package.json）
COPY package*.json ./

# 第四步：采购食材（安装依赖）
RUN npm install

# 第五步：把所有菜谱材料搬过来（复制代码）
COPY . .

# 第六步：标注这道菜要用哪个炉灶（暴露端口）
EXPOSE 3000

# 第七步：开始做菜！（启动应用）
CMD ["node", "server.js"]
```

每一行都是一个指令。Docker 会从上到下依次执行，最终构建出一个包含你应用和所有依赖的**镜像（Image）**。

### 镜像（Image）与容器（Container）

这两个概念经常被搞混，用一个简单的类比来区分：

- **镜像（Image）**= 菜谱 + 预处理好的半成品食材包。它是一个**静态的模板**，可以反复使用。
- **容器（Container）**= 按照菜谱实际做出来的那盘菜。它是镜像的一个**运行中的实例**。

一个镜像可以同时运行出多个容器，就像同一份菜谱可以做出很多盘菜。

### 核心命令

Docker 日常使用只需要记住几个命令：

```bash
# 根据 Dockerfile 构建镜像
# -t 是给镜像取个名字（tag）
docker build -t my-app .

# 运行容器
# -p 3000:3000 表示把容器的 3000 端口映射到本机的 3000 端口
# -d 表示在后台运行（detached mode）
docker run -d -p 3000:3000 my-app

# 查看正在运行的容器
docker ps

# 查看容器日志
docker logs <container-id>

# 停止容器
docker stop <container-id>
```

### Docker Compose：管理多个容器

真实项目通常不只一个服务——你可能有 Web 应用、数据库、缓存等。**Docker Compose** 让你用一个文件管理所有这些服务。

就像一个乐队指挥——他不演奏任何乐器，但他协调所有乐手（容器）一起工作。

创建 `docker-compose.yml` 文件：

```yaml
version: '3'
services:
  # 你的 Web 应用
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/mydb
    depends_on:
      - db

  # 数据库
  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=mydb
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

然后一条命令启动所有服务：

```bash
# 启动所有服务
docker-compose up -d

# 停止所有服务
docker-compose down

# 查看所有服务的日志
docker-compose logs
```

## 实操步骤

### 动手实践：容器化一个简单的 Node.js 应用

**前提**：你需要先安装 Docker Desktop（[docker.com/get-started](https://docker.com/get-started)）。

**第一步：创建一个简单的应用**

创建项目文件夹，写一个最简单的 Node.js 服务器：

```javascript
// server.js
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello from Docker!');
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

创建 `package.json`：
```json
{
  "name": "docker-demo",
  "version": "1.0.0",
  "scripts": {
    "start": "node server.js"
  }
}
```

**第二步：编写 Dockerfile**

在项目根目录创建 `Dockerfile`（无后缀名）：

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

同时创建 `.dockerignore` 文件（类似 `.gitignore`，告诉 Docker 忽略哪些文件）：

```
node_modules
.git
```

**第三步：构建并运行**

```bash
# 构建镜像
docker build -t docker-demo .

# 运行容器
docker run -d -p 3000:3000 docker-demo

# 打开浏览器访问 http://localhost:3000
# 你应该能看到 "Hello from Docker!"
```

恭喜！你的第一个 Docker 容器跑起来了！

**第四步：验证和清理**

```bash
# 查看运行中的容器
docker ps

# 停止容器
docker stop <container-id>

# 删除容器
docker rm <container-id>
```

### 什么时候该用 Docker，什么时候没必要？

| 场景 | 是否需要 Docker | 理由 |
|------|:---:|------|
| 部署到 Vercel/Netlify | 不需要 | 平台已经帮你处理环境 |
| 部署到 Railway | 可选 | Railway 支持自动检测，也支持 Docker |
| 部署到 VPS | 推荐 | Docker 让环境配置标准化，避免各种兼容问题 |
| 团队协作开发 | 推荐 | 确保所有人的开发环境一致 |
| 项目依赖复杂（多个服务） | 强烈推荐 | Docker Compose 一键启动所有服务 |
| 个人简单项目 | 不需要 | 杀鸡不用牛刀 |

> 简单原则：如果你的项目能用 Vercel/Railway 等平台直接部署，就不需要 Docker。当你的项目变复杂、需要部署到 VPS、或者团队协作时，Docker 就是你的好帮手。

## AI 辅助练习

Docker 的学习曲线比较陡，善用 AI 能让你少走很多弯路：

1. **"帮我为以下项目写一个 Dockerfile：[描述你的项目技术栈]"**
2. **"我运行 docker build 时报了这个错：[粘贴错误信息]，怎么解决？"**
3. **"帮我写一个 docker-compose.yml，包含一个 Node.js 应用和 PostgreSQL 数据库"**
4. **"解释一下 Docker 的分层缓存机制，为什么要先 COPY package.json 再 COPY 其他文件？"**

> 提示：第 4 个问题的答案涉及 Docker 的一个重要优化原理——如果 `package.json` 没有变化，Docker 会跳过 `npm install` 这一步，大大加快构建速度。

## 本章小结

- Docker 解决了 **"在我机器上能跑"** 的经典问题——把代码和运行环境一起打包
- **容器（Container）**比虚拟机更轻量，启动只需几秒
- **Dockerfile** 是构建环境的"菜谱"，定义了从基础镜像到启动应用的每一步
- **镜像（Image）**是静态模板，**容器（Container）**是运行实例
- 核心命令：`docker build`（构建）、`docker run`（运行）、`docker-compose up`（启动多服务）
- Docker 不是万能的——简单项目用平台直接部署即可，复杂项目或 VPS 部署时 Docker 才真正发光
- 作为初学者，了解 Docker 的概念即可，等项目复杂到需要时再深入学习
