# 第3章：命令行（CLI）入门

## 引言

上一章我们提到，终端（Terminal）是用文字跟电脑对话的窗口。这一章，我们要正式学习这门"对话的语言"——命令行（CLI, Command Line Interface）。

你可能会问：都 2026 年了，为什么还要学这么"原始"的东西？答案很简单：**命令行是程序员最基本也最强大的工具**。就像厨师再高级也离不开刀一样，程序员再厉害也离不开命令行。

别怕，命令行没有看上去那么可怕。学完这章，你就能像"发短信"一样流畅地跟你的电脑沟通了。

---

## 3.1 为什么要用命令行？

### 类比：语音消息 vs. 文字消息

图形界面（GUI）就像发语音消息——直观，但啰嗦、不精确。命令行就像发文字消息——简洁、精确、可以直接复制转发。

举个例子，你想在桌面创建一个叫"my-project"的文件夹，里面再建 3 个子文件夹：

**用图形界面（GUI）**：右键 → 新建文件夹 → 改名 → 双击进入 → 再右键 → 新建 → 改名 × 3……大概需要 30 秒。

**用命令行（CLI）**：

```bash
mkdir -p ~/Desktop/my-project/{src,docs,tests}
```

一行命令，不到 2 秒。

命令行的优势还有：
- **可重复**：命令可以保存、分享、自动化执行
- **可远程**：服务器通常没有图形界面，只能用命令行
- **更强大**：很多高级操作只有命令行能完成

---

## 3.2 打开你的终端

不同操作系统打开终端的方式不同：

- **macOS**：按 `Command + 空格`，输入"Terminal"，回车
- **Windows**：按 `Win + R`，输入"cmd"或"powershell"，回车；或者搜索"Windows Terminal"
- **Linux**：按 `Ctrl + Alt + T`

打开之后你会看到一个黑色（或白色）窗口，上面有一个闪烁的光标——这就是你跟电脑对话的地方。

---

## 3.3 理解"路径"

在学习命令之前，你需要先理解一个关键概念：**路径（Path）**。

### 类比：地址

路径就是文件或文件夹在电脑里的"地址"。就像你告诉快递员你的地址：

> 北京市 / 海淀区 / 中关村大街 / 1号楼 / 301室

在电脑里，地址用斜杠分隔（macOS/Linux 用 `/`，Windows 用 `\`）：

```
/Users/xiaoming/Documents/report.docx
```

### 绝对路径 vs. 相对路径

- **绝对路径（Absolute Path）**：从根目录开始的完整地址，像"北京市海淀区中关村大街1号楼301室"
  ```
  /Users/xiaoming/Documents
  ```

- **相对路径（Relative Path）**：从你当前位置出发的地址，像"隔壁301室"
  ```
  ./Documents       # 当前目录下的 Documents
  ../Pictures       # 上一级目录下的 Pictures
  ```

几个特殊符号：
- `~` 表示你的**主目录（Home Directory）**，比如 `/Users/xiaoming`
- `.` 表示**当前目录（Current Directory）**
- `..` 表示**上一级目录（Parent Directory）**

---

## 3.4 核心命令速查

下面是你必须掌握的基础命令。每个命令我们都给出"翻译"和类比：

### pwd —— 我在哪？

```bash
pwd
```

**全称**：Print Working Directory（打印当前工作目录）

**类比**：你在商场迷路了，看一眼墙上的地图，上面标着"您在此处"。`pwd` 就是那个"您在此处"。

### ls / dir —— 这里有什么？

```bash
ls          # macOS / Linux
dir         # Windows CMD
```

**全称**：List（列出内容）

**类比**：打开抽屉看看里面有什么东西。`ls -la` 可以看到更多细节（包括隐藏文件和文件大小），就像用手电筒照一下抽屉深处。

### cd —— 去别的地方

```bash
cd Documents           # 进入 Documents 文件夹
cd ..                  # 回到上一级
cd ~                   # 回到主目录
cd /                   # 去根目录
```

**全称**：Change Directory（切换目录）

**类比**：在大楼里走动——`cd Documents` 是走进"Documents"房间，`cd ..` 是退出当前房间。

### mkdir —— 新建文件夹

```bash
mkdir my-folder               # 新建一个文件夹
mkdir -p a/b/c                # 一次性创建多层嵌套文件夹
```

**全称**：Make Directory（创建目录）

**类比**：在文件柜里放一个新的文件夹。

### touch / echo —— 新建文件

```bash
touch hello.txt               # macOS / Linux：创建空文件
echo. > hello.txt             # Windows CMD：创建空文件
```

**类比**：在文件夹里放一张白纸。

### cp —— 复制

```bash
cp file.txt backup.txt               # 复制文件
cp -r my-folder my-folder-backup     # 复制整个文件夹（-r 表示递归）
```

**全称**：Copy（复制）

### mv —— 移动 / 改名

```bash
mv file.txt Documents/          # 把文件移到 Documents 文件夹
mv old-name.txt new-name.txt    # 重命名文件
```

**全称**：Move（移动）

**类比**：`mv` 就像把文件从一个抽屉挪到另一个抽屉；如果目标是同一个抽屉但换了名字，就变成了"改名"。

### rm —— 删除

```bash
rm file.txt                    # 删除文件
rm -r my-folder                # 删除文件夹及其内容
```

**全称**：Remove（删除）

**注意**：命令行删除不经过回收站，删了就真没了！就像碎纸机，用之前要想清楚。

### cat —— 查看文件内容

```bash
cat hello.txt
```

**全称**：Concatenate（连接，但常用来查看文件内容）

**类比**：打开一张纸，看看上面写了什么。

---

## 3.5 实践练习

### 练习 1：探索你的电脑

打开终端，依次执行以下命令：

```bash
pwd                    # 看看你现在在哪里
ls                     # 看看当前目录有什么
cd ~                   # 回到主目录
ls                     # 看看主目录里有什么
cd Desktop             # 进入桌面
pwd                    # 确认你在桌面了
```

### 练习 2：创建项目文件夹

用命令行完成以下操作：

```bash
cd ~/Desktop
mkdir my-first-project
cd my-first-project
mkdir src docs tests
ls                     # 确认三个文件夹都创建好了
```

### 练习 3：创建和操作文件

```bash
cd ~/Desktop/my-first-project
touch README.txt                          # 创建文件
echo "Hello, World!" > README.txt         # 写入内容（macOS/Linux）
cat README.txt                            # 查看内容
cp README.txt docs/README-backup.txt      # 复制到 docs
ls docs                                   # 确认复制成功
mv README.txt src/                        # 移动到 src
ls                                        # 原位置应该没了
ls src                                    # 新位置应该有了
```

### 练习 4：AI 辅助练习

向 AI 提问：

> 我想用命令行完成以下操作，请告诉我该输入什么命令：
> 1. 在桌面创建一个叫"practice"的文件夹
> 2. 在里面创建三个文件：a.txt, b.txt, c.txt
> 3. 把 a.txt 改名为 main.txt
> 4. 把 b.txt 移动到一个叫 archive 的子文件夹里
> 5. 删除 c.txt

按照 AI 给你的命令，亲手在终端里执行一遍。

---

## 3.6 常见问题

**Q：我输错命令了怎么办？**
A：按 `Ctrl + C` 可以取消当前命令。如果不小心进入了奇怪的状态，关掉终端窗口重新打开就好。

**Q：大小写重要吗？**
A：在 macOS/Linux 上，文件名区分大小写（`File.txt` 和 `file.txt` 是两个不同的文件）。Windows 上不区分。命令本身在所有系统上都是小写。

**Q：为什么我的 Windows 命令跟教程不一样？**
A：Windows 的传统命令行（CMD）和 macOS/Linux 的命令略有不同。建议 Windows 用户安装 Git Bash 或使用 Windows Terminal 配合 PowerShell，命令会更接近。我们在下一章会介绍安装方法。

---

## 本章小结

- 命令行（CLI）是用文字操作电脑的方式，比图形界面更高效、更强大
- **路径**是文件的"地址"，分为绝对路径和相对路径
- 核心命令只有这几个：`pwd`、`ls`、`cd`、`mkdir`、`touch`、`cp`、`mv`、`rm`、`cat`
- 命令行删除没有回收站，操作前请三思
- 遇到不会的命令，随时问 AI——它就是你的"命令行字典"

下一章，我们将搭建完整的开发环境，安装写代码需要的一切工具。准备好你的电脑，我们要"装修工作室"了！
