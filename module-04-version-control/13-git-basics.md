# 第13章：Git 基础 — 代码的时光机

## 概念讲解

你有没有写过一篇长文档，写着写着突然后悔删掉了某一段？你疯狂按 Ctrl+Z，却发现已经撤销不回去了。或者你把文件复制了一堆：`报告_v1.docx`、`报告_v2_最终版.docx`、`报告_v2_最终版_真的最终版.docx`……

**Git** 就是为了彻底解决这个问题而生的。它是一个 **版本控制系统（Version Control System）**，你可以把它想象成代码的**时光机** — 它能记住你项目的每一次变化，让你随时回到过去的任何一个时刻。

### 为什么需要版本控制？

想象你在搭一座乐高城堡：

- 你搭到第 50 步的时候，想试试换个塔楼的造型
- 如果没有版本控制，你只能拆掉重来，祈祷自己记得原来的样子
- 如果有版本控制，就像每一步都拍了一张照片 — 随时可以翻回去看，甚至直接恢复

Git 就是那台自动帮你拍照的相机。

---

## 核心概念

### 1. Repository（仓库）

**仓库**就是你项目的"家"。它是一个被 Git 跟踪的文件夹，里面包含了你所有的代码文件，以及 Git 偷偷记录的全部历史信息。

> 类比：仓库就像一本相册，里面不仅有最新的照片，还保存了从第一张照片开始的所有记录。

### 2. Commit（提交）

**提交**就是给项目拍一张"快照"。每次你完成一个小功能、修复一个 bug，就应该做一次 commit，并附上一句说明（commit message），比如"添加了登录按钮"。

> 类比：Commit 就像游戏里的**存档点** — 如果后面搞砸了，你可以读档回到这里。

### 3. Branch（分支）

**分支**允许你在不影响主线代码的情况下，开辟一条新的开发路线。主分支通常叫 `main`，你可以从它上面分出新的分支去尝试新功能。

> 类比：想象一本"选择你自己的冒险"故事书。在某一页你可以选择走左边的门或右边的门，每条路都是一个独立的故事线（分支），最后你可以选择哪条路线作为最终结局。

### 4. Merge（合并）

当你在分支上完成了新功能，确认没问题后，就可以把它**合并**回主分支。

> 类比：两条河流汇合成一条大河 — 各自带来的水（代码）融合在一起。

---

## 实操步骤

### 安装 Git

```bash
# macOS（通常已预装，或通过 Homebrew）
brew install git

# Ubuntu / Debian
sudo apt install git

# Windows：下载 Git for Windows
# https://git-scm.com/download/win
```

安装后，先做个自我介绍：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

### 基本命令实战

#### 第一步：创建仓库（git init）

```bash
mkdir my-first-project
cd my-first-project
git init
```

这就像在书架上放了一本新的空相册，准备开始记录。

#### 第二步：查看状态（git status）

```bash
git status
```

这个命令随时告诉你：哪些文件被修改了、哪些还没有被"拍照"记录。养成习惯，经常运行它。

#### 第三步：添加文件到暂存区（git add）

```bash
echo "# 我的第一个项目" > README.md
git add README.md
```

`git add` 就像是把要拍照的人叫到镜头前站好。文件还没被真正记录，只是"准备好了"。

#### 第四步：提交（git commit）

```bash
git commit -m "初始提交：添加 README 文件"
```

按下快门！这一刻被永久记录了。`-m` 后面的文字是对这次快照的说明。

#### 第五步：查看历史（git log）

```bash
git log
```

翻看相册，查看之前所有的快照记录。你会看到每次提交的时间、作者和说明。

#### 第六步：创建与切换分支（git branch / git checkout）

```bash
git branch feature-login    # 创建一个叫 feature-login 的新分支
git checkout feature-login   # 切换到这个分支
# 或者一步到位：
git checkout -b feature-login
```

现在你在新分支上了，随便怎么改都不会影响 `main` 分支。

#### 第七步：合并分支（git merge）

```bash
git checkout main           # 先切回主分支
git merge feature-login     # 把 feature-login 的改动合并进来
```

### .gitignore 文件

有些文件你不希望 Git 跟踪，比如密码文件、临时文件、系统生成的杂物。创建一个 `.gitignore` 文件来告诉 Git "忽略这些"：

```
# .gitignore 示例
node_modules/
.env
*.log
.DS_Store
__pycache__/
```

> 类比：`.gitignore` 就像告诉相机"这些人不用拍进合照里"。

---

## AI 辅助练习

试试向 AI 助手提出以下请求，边做边学：

1. **"帮我创建一个 Git 仓库，并完成三次有意义的提交。"** — 让 AI 带着你一步步操作
2. **"我执行 git status 后看到这些信息，是什么意思？"** — 把命令输出粘贴给 AI，让它帮你翻译
3. **"帮我写一个适合 Python 项目的 .gitignore 文件。"** — AI 能快速生成适合不同项目类型的忽略规则
4. **"我想在不影响主分支的情况下尝试一个新功能，该怎么做？"** — AI 会引导你使用分支

### 动手练习

完成以下任务：

1. 创建一个新文件夹，用 `git init` 初始化仓库
2. 创建 3 个文件，分别做 3 次提交，每次都写清楚提交信息
3. 用 `git log` 查看你的提交历史
4. 创建一个新分支 `experiment`，在上面修改文件
5. 切回 `main` 分支，确认文件没有被改动
6. 把 `experiment` 分支合并到 `main`
7. 创建 `.gitignore` 文件并提交

---

## 本章小结

| 概念 | 英文术语 | 类比 |
|------|----------|------|
| 仓库 | Repository | 相册 |
| 提交 | Commit | 拍照 / 游戏存档 |
| 分支 | Branch | 平行故事线 |
| 合并 | Merge | 河流汇合 |
| 暂存区 | Staging Area | 拍照前站位 |
| 忽略文件 | .gitignore | 不用入镜的人 |

记住 Git 的核心工作流：**修改文件 → git add（准备） → git commit（记录） → 继续开发**。这个循环会贯穿你整个编程生涯。下一章，我们将把这台时光机连接到云端 — GitHub！
