# 第13章：Git 基础 — 代码的时光机

## 概念讲解

你有没有试过写一篇文章，改了又改，最后想回到三天前的版本，却发现已经覆盖掉了？或者你把文件命名为 `报告_最终版.docx`、`报告_最终版2.docx`、`报告_真的最终版.docx`……这种痛苦，每个人都经历过。

**Git** 就是为了解决这个问题而诞生的。它是一个 **版本控制系统（Version Control System）**，你可以把它想象成代码的"时光机"——它记录你对文件做的每一次修改，让你随时可以回到过去的任何一个时刻。

## 类比说明

想象你在玩一个冒险游戏。每到一个关键节点，你都会**存档**。如果后面打 Boss 失败了，你可以读取之前的存档重新来过。Git 的工作方式几乎一模一样：

- **Repository（仓库）**：就是你的整个游戏存档文件夹，包含了所有历史记录。
- **Commit（提交）**：就是一次"存档"操作。每次提交都会拍一张代码的"快照"，并附上一段说明。
- **Branch（分支）**：想象游戏里有一个分叉路口——你可以走左边，也可以走右边。分支让你在不影响主线的情况下，大胆尝试新想法。
- **Merge（合并）**：当你在分支上的实验成功了，就可以把它合并回主线，就像两条路最终汇合到一起。

## 实操步骤

### 1. 安装 Git

在终端（Terminal）中输入以下命令检查是否已安装：

```bash
git --version
```

如果没有安装，可以访问 [git-scm.com](https://git-scm.com) 下载。

### 2. 初始化仓库（git init）

选一个文件夹作为你的项目目录，然后运行：

```bash
mkdir my-first-project
cd my-first-project
git init
```

这就像在这个文件夹里安装了一台时光机。你会看到提示"Initialized empty Git repository"。

### 3. 查看状态（git status）

```bash
git status
```

这个命令就像仪表盘，告诉你当前有哪些文件被修改了、哪些还没保存到时光机里。随时都可以运行它来查看情况。

### 4. 添加文件到暂存区（git add）

创建一个文件并把它加入"待存档"列表：

```bash
echo "Hello, Git!" > hello.txt
git add hello.txt
```

`git add` 就像把物品放到购物车里——你还没结账（提交），只是先选好了要保存哪些东西。想一次添加所有文件，可以用 `git add .`（注意那个点）。

### 5. 提交更改（git commit）

```bash
git commit -m "我的第一次提交：添加 hello.txt"
```

`-m` 后面的引号里是**提交信息（commit message）**，描述你这次做了什么。好的提交信息就像日记，将来回顾时一目了然。

### 6. 查看历史记录（git log）

```bash
git log
```

这会显示所有的存档记录，包括时间、作者和提交信息。就像翻看时光机的旅行日志。按 `q` 键退出。

### 7. 创建分支（git branch）和切换分支（git checkout）

```bash
git branch experiment       # 创建一个叫 experiment 的分支
git checkout experiment     # 切换到这个分支
```

现在你在"实验分支"上了，随便改代码都不会影响主分支（main）。这就像在游戏里开了一个平行存档。

也可以用一条命令同时完成创建和切换：

```bash
git checkout -b experiment
```

### 8. 合并分支（git merge）

当实验成功，切回主分支并合并：

```bash
git checkout main
git merge experiment
```

恭喜！两条时间线合并成功。

### 9. .gitignore 文件

有些文件你不想让 Git 追踪，比如临时文件、密码配置、系统生成的杂项文件。创建一个 `.gitignore` 文件来告诉 Git 忽略它们：

```
# 忽略所有 .log 文件
*.log

# 忽略 node_modules 文件夹
node_modules/

# 忽略环境变量文件（通常含密码等敏感信息）
.env
```

把 `.gitignore` 想象成时光机的过滤器——告诉它"这些东西不用记录"。

## AI 辅助练习

试着让 AI 助手（如 ChatGPT 或 Claude）帮你完成以下任务：

1. **提示词**：`"我在 Git 中创建了一个新分支做实验，但改到一半想放弃回到主分支，应该怎么做？请一步步教我。"`
2. **提示词**：`"帮我写一个适合 Python 项目的 .gitignore 文件，并解释每一行的作用。"`
3. **挑战**：创建一个新仓库，添加三个文件，做三次提交，然后用 `git log` 查看历史。再创建一个分支，在分支上修改文件，最后合并回主分支。

## 本章小结

| 概念 | 类比 | 核心命令 |
|------|------|---------|
| Repository（仓库） | 游戏存档文件夹 | `git init` |
| Commit（提交） | 一次存档 | `git add` + `git commit` |
| Branch（分支） | 平行时间线 | `git branch` / `git checkout` |
| Merge（合并） | 时间线汇合 | `git merge` |
| .gitignore | 时光机过滤器 | 创建 `.gitignore` 文件 |

记住：Git 是程序员最重要的工具之一。刚开始可能觉得命令多，但只要掌握本章这几个基础命令，你就已经能应对日常 80% 的场景了。多练习，很快就会成为肌肉记忆！
