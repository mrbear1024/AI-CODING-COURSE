# 第25章：实战项目一 — 个人博客/作品集网站

## 项目介绍：你的互联网名片

每个手艺人都需要一个作品展示窗口。木匠有样板间，画家有画廊，而程序员的"画廊"就是个人博客和作品集网站（Portfolio Site）。

这个项目完成后，你将拥有：
- 一个可以展示文章和项目作品的个人网站
- 部署在互联网上，任何人都可以通过链接访问
- 用 Markdown 写文章，简单高效
- 响应式设计（Responsive Design），手机和电脑都好看

更重要的是，你将亲身体验**从零到上线**的完整流程，并且全程有 AI 辅助你完成每一步。

---

## 技术选型（Tech Stack）

我们选择以下技术栈，原因在括号里说明：

| 技术 | 用途 | 为什么选它 |
|---|---|---|
| **Astro** | 前端框架（Frontend Framework） | 专为内容网站设计，简单快速，对新手友好 |
| **Tailwind CSS** | 样式工具（CSS Framework） | 不用写传统 CSS，直接在 HTML 里用类名控制样式 |
| **Markdown** | 文章格式 | 写起来像纯文本，渲染出来像排版过的文章 |
| **Vercel** | 部署平台（Deployment Platform） | 免费、一键部署、自动更新 |

> **为什么选 Astro 而不是 Next.js？** Astro 更适合内容型网站（博客、文档），它默认不加载多余的 JavaScript（JS），页面加载更快。Next.js 功能更强大，但对于博客来说有点"杀鸡用牛刀"。当然，如果你更熟悉 React，用 Next.js 也完全可以。

---

## 分步实现：跟着 AI 一起写

### 第一步：初始化项目（Project Initialization）

打开终端（Terminal），告诉 AI 你想做什么：

```
我想用 Astro 创建一个个人博客项目，请告诉我初始化命令，
并解释每一步在做什么。
```

AI 会指导你运行：

```bash
# 创建 Astro 项目
npm create astro@latest my-blog

# 进入项目目录
cd my-blog

# 安装 Tailwind CSS
npx astro add tailwind

# 启动开发服务器
npm run dev
```

打开浏览器访问 `http://localhost:4321`，你应该能看到 Astro 的欢迎页面。就像盖房子打好了地基，虽然还是空的，但"地"已经有了。

### 第二步：设计布局和页面（Layout & Pages）

一个博客网站通常需要这些页面：

- **首页**（Home）：展示最近的文章列表
- **文章页**（Post）：单篇文章的详细内容
- **关于页**（About）：介绍你自己
- **作品集页**（Portfolio）：展示你的项目

先创建公共布局（Layout）——就像房子的框架，每个房间都共用同样的屋顶和地基：

向 AI 发出请求：

```
请帮我创建一个 Astro 的基础布局组件（Layout），包含：
1. 顶部导航栏，有"首页""博客""作品集""关于"四个链接
2. 主内容区域（用 <slot /> 占位）
3. 底部页脚，包含版权信息
使用 Tailwind CSS 来写样式。
```

AI 会生成类似这样的代码（保存为 `src/layouts/BaseLayout.astro`）：

```astro
---
// 这是 Astro 组件的"脚本区域"（frontmatter）
const { title } = Astro.props;
---

<html lang="zh-CN">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width" />
    <title>{title}</title>
  </head>
  <body class="min-h-screen flex flex-col bg-gray-50">
    <!-- 导航栏 -->
    <nav class="bg-white shadow-sm px-6 py-4">
      <div class="max-w-4xl mx-auto flex justify-between items-center">
        <a href="/" class="text-xl font-bold">我的博客</a>
        <div class="space-x-4">
          <a href="/" class="hover:text-blue-600">首页</a>
          <a href="/blog" class="hover:text-blue-600">博客</a>
          <a href="/portfolio" class="hover:text-blue-600">作品集</a>
          <a href="/about" class="hover:text-blue-600">关于</a>
        </div>
      </div>
    </nav>

    <!-- 主内容区域 -->
    <main class="flex-1 max-w-4xl mx-auto w-full px-6 py-8">
      <slot />
    </main>

    <!-- 页脚 -->
    <footer class="bg-gray-800 text-white text-center py-4">
      <p>&copy; 2025 我的博客. All rights reserved.</p>
    </footer>
  </body>
</html>
```

看到 Tailwind CSS 的用法了吗？`bg-white` 就是白色背景，`px-6` 就是左右内边距（padding），`hover:text-blue-600` 就是鼠标悬停时变蓝色。像说话一样直观！

### 第三步：创建博客文章系统（Markdown Blog）

Astro 天生支持 Markdown（一种简洁的文本格式）。你只需要在特定文件夹里创建 `.md` 文件，它就会自动变成网页。

在 `src/content/blog/` 文件夹下创建你的第一篇文章：

```markdown
---
title: "我的第一篇博客"
description: "用 Astro 搭建博客的心路历程"
pubDate: 2025-01-15
tags: ["编程", "Astro", "入门"]
---

# 大家好！

这是我的第一篇博客文章。我正在学习用 AI 辅助编程，
这个博客就是我的第一个实战项目。

## 我学到了什么

1. 如何使用 Astro 框架
2. Tailwind CSS 的基本用法
3. 如何与 AI 协作写代码

## 感受

AI 编程就像有一个随时待命的导师，
你问它问题，它不仅给你答案，还会解释原理。
```

上面 `---` 之间的部分叫做 **frontmatter**（前置数据），就像一封信的信封——写着标题、日期、标签等"元信息"（metadata）。下面的部分就是文章正文。

接下来，让 AI 帮你创建文章列表页和文章详情页：

```
我已经在 src/content/blog/ 下创建了 Markdown 博客文章。
请帮我：
1. 创建一个博客列表页（/blog），展示所有文章的标题、日期和描述
2. 创建文章详情页的模板，展示完整文章内容
3. 使用 Astro 的 Content Collections API
```

### 第四步：添加响应式设计（Responsive Design）

响应式设计意味着你的网站在手机、平板和电脑上都能正常显示。Tailwind CSS 让这件事变得非常简单。

核心概念是**断点前缀**（Breakpoint Prefix）：

```html
<!-- 手机上一列，平板两列，电脑三列 -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <!-- 文章卡片 -->
</div>
```

- 没有前缀：应用到所有屏幕（默认就是手机）
- `md:`：屏幕宽度 >= 768px 时生效（平板）
- `lg:`：屏幕宽度 >= 1024px 时生效（电脑）

这就像搭积木——小屏幕时积木竖着排成一列，大屏幕时积木横着排成多列。

让 AI 帮你检查和优化响应式设计：

```
请检查我的博客首页代码，确保在手机上：
1. 导航栏变成汉堡菜单（hamburger menu）
2. 文章卡片变成单列排列
3. 字体大小适当调整
请给出需要修改的代码。
```

### 第五步：部署到 Vercel（Deploy）

部署（Deploy）就是把你本地电脑上的网站"搬"到互联网上，让全世界的人都能访问。Vercel 是目前最适合前端项目部署的平台之一，而且对个人项目完全免费。

**准备工作：把代码上传到 GitHub**

```
我的 Astro 博客项目已经开发完成，在本地运行正常。
请告诉我如何：
1. 在 GitHub 创建一个仓库（repository）
2. 把代码推送上去
3. 在 Vercel 上导入这个 GitHub 仓库并部署
请给出详细的步骤。
```

AI 会指导你执行以下流程：

```bash
# 1. 初始化 Git 仓库（如果还没有的话）
git init
git add .
git commit -m "Initial commit: my blog"

# 2. 在 GitHub 上创建仓库后，关联并推送
git remote add origin https://github.com/你的用户名/my-blog.git
git push -u origin main
```

然后去 [vercel.com](https://vercel.com)：
1. 用 GitHub 账号登录
2. 点击 "Import Project"
3. 选择你的博客仓库
4. Vercel 会自动检测到这是 Astro 项目，点击 "Deploy"
5. 等待几十秒……你的博客就上线了！

Vercel 会给你一个类似 `my-blog-abc123.vercel.app` 的域名。你的博客，全世界都能看到了！

---

## AI 辅助开发的完整工作流

回顾一下整个过程，你会发现一个模式：

```
你的想法 → 告诉 AI → AI 生成代码 → 你审查和测试 → 提出修改 → AI 调整 → 重复……
```

这就是 AI 辅助开发的核心工作流（Workflow）。你不需要记住所有的 API 和语法，但你需要：

1. **知道你想要什么**——明确的需求描述
2. **能看懂 AI 生成的代码**——至少知道它在做什么
3. **能发现问题并描述问题**——"这个按钮点击没反应"比"代码有 bug"有用得多
4. **不断学习**——每次 AI 给你的代码，都是学习的机会

---

## 拓展挑战

博客上线后，你可以尝试逐步添加这些功能：

- [ ] 暗色模式（Dark Mode）切换
- [ ] 文章搜索功能
- [ ] 标签页面（点击标签看到所有相关文章）
- [ ] RSS 订阅
- [ ] 添加 Google Analytics 统计访问量
- [ ] 绑定自定义域名

每添加一个功能，都向 AI 提出请求，然后理解它给你的代码。这是最好的学习方式。

---

## 本章小结

在这一章里，你完成了人生中第一个完整的 Web 项目：

1. **初始化**：用 Astro + Tailwind CSS 搭建项目骨架
2. **布局设计**：创建公共布局和多个页面
3. **内容系统**：用 Markdown 写博客文章，Astro 自动渲染成网页
4. **响应式设计**：让网站在各种设备上都好看
5. **部署上线**：通过 GitHub + Vercel 把网站发布到互联网

最关键的收获是：你体验了一个完整的 AI 辅助开发流程。从需求描述到代码生成，从本地测试到线上部署，AI 在每一步都能帮到你。

下一章，我们要挑战更有难度的项目——一个全栈（Full-stack）的 Todo 应用！
