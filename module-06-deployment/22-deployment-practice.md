# 第22章：部署实战

## 概念讲解

写好代码只是旅程的一半——另一半是让全世界都能访问你的作品。这就是**部署（Deployment）**。

把部署想象成**开餐厅的最后一步**：菜谱研发好了（代码写好了），食材准备好了（依赖安装了），现在要正式开业，让顾客能进来吃饭。

本章我们会从最简单的方案开始，一步步走到更专业的部署方式。

---

## 一、前端部署到 Vercel / Netlify

前端部署是最简单的，因为前端项目本质上就是一堆**静态文件（Static Files）**——HTML、CSS、JavaScript。你只需要把这些文件放到一个能被访问的地方就行了。

### 用 Vercel 部署（推荐）

**第一步：准备工作**
- 确保你的代码已经推送到 GitHub 仓库
- 确保项目能在本地正常运行 (`npm run build` 没有错误)

**第二步：连接 Vercel**
1. 访问 [vercel.com](https://vercel.com)，用 GitHub 登录
2. 点击 "Add New Project"
3. 选择你的 GitHub 仓库
4. Vercel 会自动检测框架类型（React、Vue、Next.js 等）

**第三步：配置与部署**
1. 确认构建命令（Build Command）：通常是 `npm run build`
2. 确认输出目录（Output Directory）：React 项目通常是 `build`，Vite 项目是 `dist`
3. 点击 "Deploy"
4. 等待 1-2 分钟，你的网站就上线了！

```
部署成功后你会得到一个地址：
https://your-project-abc123.vercel.app
```

**最棒的特性**：以后每次你 `git push` 到 GitHub，Vercel 会**自动重新部署**。这就是 CI/CD 的魔力（后面会讲）。

### Netlify 的步骤几乎一样

1. 访问 [netlify.com](https://netlify.com)，用 GitHub 登录
2. 点击 "Add new site" → "Import an existing project"
3. 选择仓库，配置构建命令，部署完成

---

## 二、后端部署到 Railway / Fly.io

后端和前端不同——后端是一个**持续运行的程序**，需要处理请求、连接数据库。所以部署后端会稍微复杂一点。

### 用 Railway 部署

Railway 就像一个"智能厨房"——你把菜谱（代码）给它，它帮你准备好所有厨具（环境），然后开始做菜（运行服务）。

**第一步：准备代码**

确保你的后端项目有以下内容：
```json
// package.json 中要有 start 脚本
{
  "scripts": {
    "start": "node server.js"
  }
}
```

确保服务器监听的端口使用环境变量：
```javascript
// server.js
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

> 这一点非常重要！Railway 会通过 `PORT` 环境变量告诉你的应用该用哪个端口。

**第二步：部署到 Railway**
1. 访问 [railway.app](https://railway.app)，用 GitHub 登录
2. 点击 "New Project" → "Deploy from GitHub repo"
3. 选择你的后端仓库
4. Railway 自动检测并部署

**第三步：添加数据库（如果需要）**
1. 在项目中点击 "New" → "Database" → 选择 PostgreSQL/MySQL
2. Railway 会自动生成 `DATABASE_URL` 环境变量
3. 你的代码中用 `process.env.DATABASE_URL` 来连接

---

## 三、VPS 部署入门

VPS 部署就像你自己**从零开始装修一间店铺**——自由度最高，但也最费劲。

### 连接你的 VPS：SSH

**SSH（Secure Shell）**是远程登录服务器的方式。就像用一把安全的钥匙，打开远方电脑的大门。

```bash
# 基本连接命令
ssh username@your-server-ip

# 例如
ssh root@123.45.67.89
```

### 必会的 Linux 基础命令

登录后你会看到一个**命令行界面（Terminal）**——没有图形界面，一切靠打字。以下是最常用的命令：

```bash
ls              # 列出当前目录的文件（List）
cd /path        # 进入某个目录（Change Directory）
pwd             # 显示当前目录路径（Print Working Directory）
mkdir myapp     # 创建文件夹（Make Directory）
nano file.txt   # 编辑文件（简单文本编辑器）
sudo apt update # 更新系统软件包列表（Ubuntu/Debian）
sudo apt install nginx  # 安装 Nginx
```

### Nginx：你的"前台接待员"

**Nginx** 是一个**反向代理服务器（Reverse Proxy）**。这个概念有点抽象，让我们用类比来理解。

想象一栋写字楼：
- **Nginx** 就是大楼的前台接待员
- 访客（用户请求）进来后先到前台
- 前台根据访客要找的部门（域名/路径），引导他们去对应的办公室（后端服务）

一个基本的 Nginx 配置文件：

```nginx
server {
    listen 80;
    server_name mysite.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

这段配置的意思是：所有访问 `mysite.com` 的请求，都转发给本机 3000 端口上运行的应用。

---

## 四、环境变量与密钥管理

**环境变量（Environment Variables）**是存储敏感信息的安全方式。

类比：你做菜需要秘方（API 密钥、数据库密码），但你不会把秘方写在菜单上让所有顾客看到。环境变量就是那个锁在厨房保险箱里的秘方。

**铁律**：**永远不要把密钥写在代码里，更不要提交到 GitHub！**

```bash
# 错误做法 ❌
const API_KEY = "sk-abc123secret";

# 正确做法 ✅
const API_KEY = process.env.API_KEY;
```

在各平台设置环境变量的方法：
- **Vercel**：项目 Settings → Environment Variables
- **Railway**：项目 → Variables 选项卡
- **VPS**：创建 `.env` 文件，或在系统中设置

---

## 五、CI/CD：代码推送自动部署

**CI/CD（Continuous Integration / Continuous Deployment，持续集成 / 持续部署）**是什么？

想象一个自动化的出版流程：
- 你写好文章（代码）
- 提交给编辑部（git push）
- 自动校对、排版、印刷、上架（测试、构建、部署）

这一切**自动发生**，你不用手动做任何事情。

使用 Vercel 或 Railway 时，CI/CD 已经**内置**了——每次你 `git push`，平台会自动拉取新代码、构建并部署。

更复杂的场景可以用 **GitHub Actions**——在 `.github/workflows/` 目录下写配置文件，定义自动化流程。

---

## 六、监控与日志（Monitoring & Logs）

部署不是终点，你还需要知道应用是否正常运行。

- **日志（Logs）**：应用运行时输出的信息。Vercel/Railway 都有在线日志查看器
- **监控（Monitoring）**：检查应用是否在线、响应是否正常
- **UptimeRobot**（免费）：每 5 分钟检查你的网站是否能访问，挂了会发邮件通知你

---

## 七、常见部署问题与解决方案

| 问题 | 可能原因 | 解决方法 |
|------|---------|---------|
| 构建失败（Build Failed） | 依赖未安装、代码语法错误 | 查看构建日志，在本地先 `npm run build` 测试 |
| 网站显示 502/503 | 后端服务没启动或崩溃 | 检查日志，确认 `PORT` 环境变量设置正确 |
| 环境变量不生效 | 变量名写错、未重新部署 | 仔细核对变量名，重新部署 |
| 数据库连接失败 | 连接字符串错误、IP 未授权 | 检查 `DATABASE_URL`，确认数据库允许外部连接 |
| 本地能跑，线上不行 | 环境差异、路径问题 | 检查 Node.js 版本是否一致，路径是否用了绝对路径 |

## 实操步骤

### 综合练习：部署一个全栈项目

1. 把你之前做的前端项目推送到 GitHub
2. 在 Vercel 上部署前端
3. 如果有后端，在 Railway 上部署后端
4. 在 Vercel 中设置好环境变量（比如后端的 API 地址）
5. 测试所有功能是否正常
6. 把部署后的网址分享给朋友！

## AI 辅助练习

在部署过程中遇到问题时，试着这样向 AI 求助：

1. **"我的 Vercel 部署失败了，以下是构建日志：[粘贴日志]。请帮我分析原因并给出解决方案。"**
2. **"帮我写一个 Nginx 配置文件，把 mysite.com 指向本机 3000 端口的 Node.js 应用，并配置 SSL。"**
3. **"我的 Railway 后端返回 502 错误，环境变量如下：[列出变量名，不要列出值！]。可能是什么问题？"**
4. **"帮我写一个 GitHub Actions 配置，每次推送到 main 分支时自动运行测试。"**

> 重要提醒：向 AI 求助时，**永远不要把密钥和密码的实际值发出去**！只发变量名就好。

## 本章小结

- **前端部署**最简单：推送到 GitHub → Vercel/Netlify 自动构建 → 上线
- **后端部署**需要注意端口配置和环境变量：Railway/Fly.io 是好选择
- **VPS 部署**自由度最高但最复杂：需要掌握 SSH、Linux 命令、Nginx
- **环境变量**用来存储敏感信息——永远不要把密钥写在代码里
- **CI/CD** 让部署自动化——推送代码就自动部署
- 遇到问题先**看日志**，把日志发给 AI 是最高效的求助方式
