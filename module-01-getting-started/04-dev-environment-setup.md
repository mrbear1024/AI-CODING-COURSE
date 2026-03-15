# 第4章：开发环境搭建

## 引言

前面三章，我们了解了课程大纲、计算机基础和命令行操作。现在是时候正式**搭建你的开发环境（Development Environment）**了。

开发环境就像画家的画室——你需要画布（代码编辑器）、颜料（编程语言运行时）、工具架（包管理器），都准备齐了才能开始创作。

这一章会手把手带你安装所有必要的工具。过程可能有点繁琐，但请耐心跟着做——这是一次性的"装修工程"，完成之后就可以安心写代码了。

---

## 4.1 安装代码编辑器

代码编辑器（Code Editor）是你写代码的地方。虽然理论上用记事本也能写代码，但专业的编辑器提供**语法高亮（Syntax Highlighting）**、**自动补全（Auto-completion）**、**错误提示**等功能，大大提升效率。

我们推荐两款编辑器，任选其一：

### 选项 A：VS Code（推荐新手）

**VS Code**（全称 Visual Studio Code）是微软出品的免费编辑器，全世界最多程序员在用。

**安装步骤**：

1. 打开浏览器，访问 https://code.visualstudio.com
2. 点击页面中间的大按钮"Download"——网站会自动识别你的操作系统
3. 下载完成后：
   - **Windows**：双击 `.exe` 文件，一路点"下一步"，安装时勾选"Add to PATH"
   - **macOS**：打开 `.dmg` 文件，把 VS Code 图标拖到"应用程序"文件夹
   - **Linux**：下载 `.deb` 或 `.rpm` 包，双击安装或用命令行安装
4. 安装完成后，打开 VS Code

**首次打开后的设置**：
- 界面语言：按 `Ctrl + Shift + P`（macOS 用 `Cmd + Shift + P`），输入"Configure Display Language"，选择"中文（简体）"
- 安装中文语言包（Chinese Language Pack）——VS Code 会自动提示你安装

### 选项 B：Cursor（AI 增强编辑器）

**Cursor** 是基于 VS Code 构建的编辑器，内置了强大的 AI 编程助手。如果你希望从一开始就用 AI 辅助写代码，这是一个很好的选择。

**安装步骤**：

1. 访问 https://cursor.sh
2. 点击"Download"按钮
3. 安装过程与 VS Code 相同
4. 首次打开时需要注册一个 Cursor 账号

**类比**：VS Code 就像一把普通的好刀，Cursor 就像一把带辅助瞄准的好刀——底子一样，但多了 AI 加成。

---

## 4.2 终端环境配置

### macOS

macOS 自带终端应用（Terminal），已经足够好用。打开方式：`Command + 空格` → 输入"Terminal"。

macOS 默认使用 **zsh** 作为命令行解释器（Shell），功能强大，无需额外配置。

### Windows

Windows 的默认命令行（CMD）功能较弱，我们建议安装以下工具：

**安装 Windows Terminal**（推荐）：
1. 打开 Microsoft Store（微软应用商店）
2. 搜索"Windows Terminal"
3. 点击"获取"安装

**安装 Git Bash**（让 Windows 支持 Linux 命令）：
1. 访问 https://git-scm.com/downloads
2. 下载 Windows 版本
3. 安装时保持默认选项即可
4. 安装完成后，右键桌面可以看到"Git Bash Here"选项

安装 Git Bash 后，你在 Windows 上也可以使用 `ls`、`cat`、`touch` 等 Linux 命令了。

### Linux

Linux 自带终端，通常按 `Ctrl + Alt + T` 即可打开。Linux 用户一般已经对命令行比较熟悉，无需额外配置。

---

## 4.3 安装 Node.js

**Node.js** 是 JavaScript 语言的运行时环境（Runtime）。JavaScript 是网页开发的核心语言，也是我们课程中重点使用的语言之一。

### 类比

编程语言是"菜谱"，运行时（Runtime）就是"厨房"。你写了一份 JavaScript 菜谱，需要 Node.js 这个厨房才能把菜做出来。

### 安装步骤（全平台通用）

1. 访问 https://nodejs.org
2. 你会看到两个版本：**LTS**（长期支持版）和 **Current**（最新版）
3. **选择 LTS 版本**——更稳定，适合学习
4. 下载并安装：
   - **Windows**：双击 `.msi` 文件，一路"下一步"
   - **macOS**：双击 `.pkg` 文件，按提示安装
   - **Linux**：推荐使用命令行安装

**Linux 用户安装命令**：
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### 验证安装

打开终端，输入：

```bash
node --version
npm --version
```

如果看到版本号（比如 `v20.x.x` 和 `10.x.x`），说明安装成功！

**npm**（Node Package Manager，Node 包管理器）会随 Node.js 一起自动安装。它就像一个"应用商店"，可以帮你下载别人写好的代码库（Package）。

---

## 4.4 安装 Python

**Python** 是最流行的编程语言之一，语法简洁、易于学习，在数据分析和人工智能领域尤其常用。

### 安装步骤

**Windows**：
1. 访问 https://www.python.org/downloads/
2. 点击"Download Python 3.x.x"
3. **重要**：安装时务必勾选底部的 "Add Python to PATH"
4. 点击"Install Now"

**macOS**：
1. 推荐使用 Homebrew（一个 macOS 的包管理器）安装：

```bash
# 先安装 Homebrew（如果还没有的话）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 再安装 Python
brew install python
```

**Linux**（通常已预装）：
```bash
sudo apt update
sudo apt install python3 python3-pip
```

### 验证安装

```bash
python3 --version        # macOS / Linux
python --version          # Windows（或 python3 --version）
pip3 --version            # 检查 pip 包管理器
```

看到版本号就说明安装成功。

**pip**（Python 的包管理器）就像 npm 一样，是 Python 世界的"应用商店"，可以帮你安装各种 Python 工具包。

---

## 4.5 安装 Git

**Git** 是一个版本控制工具（Version Control），可以帮你跟踪代码的每一次修改。

### 类比

Git 就像一台时光机。每次你保存代码的一个版本，Git 就拍一张"快照"。如果后来你把代码改坏了，可以随时回到任何一个之前的"快照"。

### 安装步骤

- **Windows**：上面安装 Git Bash 时已经一起装好了
- **macOS**：在终端输入 `git --version`，系统会自动提示你安装
- **Linux**：`sudo apt install git`

### 验证安装

```bash
git --version
```

---

## 4.6 最终验证：全部检查一遍

现在，让我们一口气验证所有工具是否安装成功。打开终端，依次输入：

```bash
# 代码编辑器
code --version        # VS Code（如果安装了的话）

# Node.js 和 npm
node --version
npm --version

# Python 和 pip
python3 --version     # macOS / Linux（Windows 用 python --version）
pip3 --version        # macOS / Linux（Windows 用 pip --version）

# Git
git --version
```

如果每一行都显示了版本号，恭喜你——你的开发环境搭建完成了！

### 如果遇到报错怎么办？

最常见的问题是**"command not found"**（找不到命令），通常原因是安装时没有正确添加到 PATH（系统搜索路径）。

解决方法：
1. 完全关闭终端窗口，重新打开再试
2. 如果还是不行，重新安装对应工具，注意勾选"Add to PATH"选项
3. 把错误信息复制给 AI，让它帮你排查

---

## 4.7 实践练习

### 练习 1：用 VS Code 写第一行代码

1. 打开 VS Code（或 Cursor）
2. 点击"文件" → "新建文件"
3. 在编辑器里输入：
   ```javascript
   console.log("Hello, World!");
   ```
4. 按 `Ctrl + S`（macOS 用 `Cmd + S`）保存到桌面，文件名为 `hello.js`
5. 打开终端，执行：
   ```bash
   cd ~/Desktop
   node hello.js
   ```
6. 如果看到终端输出了 `Hello, World!`，恭喜你——你已经运行了第一行代码！

### 练习 2：用 Python 写第一行代码

1. 在 VS Code 里新建文件，输入：
   ```python
   print("你好，世界！")
   ```
2. 保存为 `hello.py`
3. 在终端执行：
   ```bash
   python3 hello.py
   ```
4. 看到输出"你好，世界！"就成功了！

### 练习 3：AI 辅助排错

如果上面任何步骤出了问题，把终端显示的错误信息复制给 AI：

> 我在学习编程，按照教程执行了以下命令但出现了错误：
> [粘贴你输入的命令]
> 错误信息如下：
> [粘贴终端显示的错误信息]
> 我的操作系统是 [Windows/macOS/Linux]，请帮我解决这个问题。

AI 几乎可以解决所有安装类问题——这就是你的"技术支持热线"。

---

## 本章小结

| 工具 | 作用 | 类比 |
|------|------|------|
| VS Code / Cursor | 写代码的编辑器 | 画家的画布和画笔 |
| Terminal | 用命令操作电脑 | 跟电脑发短信 |
| Node.js | 运行 JavaScript 的环境 | JavaScript 的"厨房" |
| npm | JavaScript 的包管理器 | JavaScript 的"应用商店" |
| Python | 运行 Python 的环境 | Python 的"厨房" |
| pip | Python 的包管理器 | Python 的"应用商店" |
| Git | 版本控制工具 | 代码的"时光机" |

恭喜你完成了模块 1 的全部内容！你现在已经：
- 了解了课程整体规划
- 掌握了计算机基础知识
- 学会了命令行基本操作
- 搭建好了完整的开发环境

从下一个模块开始，我们将正式进入编程的世界。准备好了吗？让我们一起出发！
