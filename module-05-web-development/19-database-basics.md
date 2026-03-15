# 第19章：数据库基础

## 引言：数据存在哪里？

在上一章，我们搭建了一个后端 API，但有一个问题——数据是存在内存里的，服务器一重启，数据就全丢了。这就像你在白板上写的笔记，擦掉就没了。

我们需要一个**永久保存数据**的地方，这就是**数据库（Database）**。

**把数据库想象成一个超级有序的文件柜：**
- 每个抽屉是一张**表（Table）**
- 每个抽屉里的文件夹按照固定的格式整理
- 你可以快速找到任何一份文件，也可以添加、修改、删除文件
- 不管停电还是重启，文件都安安稳稳地在那里

而且这个文件柜有一个超级聪明的管理员，你只要说"帮我找出所有2024年以后入职的员工"，它就能瞬间给你结果。

---

## 核心概念讲解

### 两大类数据库

数据库主要分为两大阵营：

#### 关系型数据库（Relational Database）—— 整齐的 Excel 表格

**关系型数据库**把数据存储在一张张"表格"里，每张表有固定的列（字段），每一行是一条记录。

**就像 Excel 表格：**

| id | name | email | age |
|----|------|-------|-----|
| 1 | 小明 | xm@mail.com | 25 |
| 2 | 小红 | xh@mail.com | 23 |
| 3 | 小李 | xl@mail.com | 28 |

常见的关系型数据库：
- **SQLite** —— 最轻量级，一个文件就是一个数据库，适合学习和小型项目
- **PostgreSQL** —— 功能强大，适合正式项目，业界口碑极好
- **MySQL** —— 历史悠久，使用广泛，很多网站在用

关系型数据库最大的特点是：表和表之间可以建立**关系（Relationship）**。比如"用户表"和"订单表"通过用户 ID 关联起来——一个用户可以有多个订单。

#### 非关系型数据库（NoSQL Database）—— 灵活的笔记本

**NoSQL** 的意思是 "Not Only SQL"。这类数据库不要求固定的表格格式，数据以更灵活的方式存储。

**如果说关系型数据库是规规矩矩的表格，NoSQL 就像一个自由的笔记本**——每一页可以写不同格式的内容。

最有名的 NoSQL 数据库是 **MongoDB**，它用类似 JSON 的格式存储数据：

```json
{
    "_id": "abc123",
    "name": "小明",
    "email": "xm@mail.com",
    "hobbies": ["编程", "读书", "跑步"],
    "address": {
        "city": "北京",
        "district": "海淀区"
    }
}
```

注意看，这条数据里有数组（hobbies）和嵌套对象（address），这在传统的表格里是很难表达的。

**怎么选择？** 简单的经验法则：
- 数据结构固定、需要复杂查询 → **关系型数据库**
- 数据结构多变、需要灵活扩展 → **NoSQL 数据库**
- 刚开始学习 → 先学**关系型数据库**，因为 SQL 是基础中的基础

### CRUD 操作 —— 数据的四种基本操作

不管用什么数据库，你对数据的操作几乎都可以归结为四种，统称 **CRUD**：

| 操作 | 英文 | 含义 | 生活类比 |
|------|------|------|----------|
| **C** | Create | 创建新数据 | 在文件柜里放入新文件 |
| **R** | Read | 读取/查询数据 | 从文件柜里找出某个文件 |
| **U** | Update | 修改已有数据 | 打开文件，修改里面的内容 |
| **D** | Delete | 删除数据 | 把文件从柜子里取出扔掉 |

几乎所有的软件功能都围绕 CRUD 展开。注册账号是 Create，查看个人主页是 Read，修改昵称是 Update，注销账号是 Delete。

### SQL 基础语法 —— 和数据库"对话"

**SQL（Structured Query Language，结构化查询语言）** 是和关系型数据库交流的语言。别被名字吓到，SQL 读起来几乎就像英语句子。

#### 创建表（CREATE TABLE）

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE,
    age INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

翻译成大白话："创建一个叫 users 的表，里面有 id、name、email、age、created_at 这几列。"

#### 插入数据（INSERT）

```sql
INSERT INTO users (name, email, age) VALUES ('小明', 'xm@mail.com', 25);
INSERT INTO users (name, email, age) VALUES ('小红', 'xh@mail.com', 23);
```

翻译："往 users 表里插入一条记录。"

#### 查询数据（SELECT）

```sql
-- 查询所有用户
SELECT * FROM users;

-- 只查名字和邮箱
SELECT name, email FROM users;

-- 查询年龄大于 24 的用户
SELECT * FROM users WHERE age > 24;

-- 按年龄排序
SELECT * FROM users ORDER BY age DESC;

-- 只查前 10 条
SELECT * FROM users LIMIT 10;
```

SQL 的 SELECT 语句读起来就像英语："从 users 表中选取所有列，条件是年龄大于24，按年龄降序排列。"

#### 更新数据（UPDATE）

```sql
-- 把小明的年龄改为 26
UPDATE users SET age = 26 WHERE name = '小明';
```

**重要提醒：UPDATE 和 DELETE 语句一定要加 WHERE 条件！** 不然会修改或删除所有数据。这就像你说"把文件柜里的文件都扔了"——肯定不是你想要的结果。

#### 删除数据（DELETE）

```sql
-- 删除名为小明的用户
DELETE FROM users WHERE name = '小明';
```

#### JOIN —— 连接多张表

关系型数据库的强大之处在于**表的关联查询**。假设有两张表：

**users 表：**
| id | name |
|----|------|
| 1 | 小明 |
| 2 | 小红 |

**orders 表：**
| id | user_id | product | price |
|----|---------|---------|-------|
| 1 | 1 | 键盘 | 299 |
| 2 | 1 | 鼠标 | 99 |
| 3 | 2 | 耳机 | 199 |

想知道每个用户买了什么？用 **JOIN**：

```sql
SELECT users.name, orders.product, orders.price
FROM users
JOIN orders ON users.id = orders.user_id;
```

结果：

| name | product | price |
|------|---------|-------|
| 小明 | 键盘 | 299 |
| 小明 | 鼠标 | 99 |
| 小红 | 耳机 | 199 |

JOIN 就像把两个文件柜的资料对照着看——"根据用户 ID 把两张表拼在一起"。

### ORM —— 用编程语言"说" SQL

直接写 SQL 字符串有时候不太方便，也容易出错。**ORM（Object-Relational Mapping，对象关系映射）** 让你用自己熟悉的编程语言来操作数据库。

**没有 ORM 时（直接写 SQL）：**
```python
cursor.execute("SELECT * FROM users WHERE age > 24")
```

**有 ORM 时（用 Python 对象操作）：**
```python
# 使用 SQLAlchemy ORM
users = User.query.filter(User.age > 24).all()
```

两种写法效果一样，但 ORM 的方式更像在写普通代码，不用在代码里嵌入 SQL 字符串。

常见的 ORM 工具：
- **Python**：SQLAlchemy、Prisma（通过 Prisma Client Python）
- **JavaScript/Node.js**：Prisma、Sequelize、TypeORM
- 对初学者来说，**Prisma** 是目前体验最好的选择之一

---

## 实操步骤：从零开始操作数据库

**方案一：用在线工具练习 SQL（最简单）**

访问 [SQLite Online](https://sqliteonline.com/)，直接在浏览器里练习 SQL，不用安装任何东西。

**方案二：让 AI 帮你搭建项目**

向 AI 提出需求：
> "请帮我用 Python + SQLite 写一个简单的用户管理程序，包括：
> 1. 创建 users 表
> 2. 添加用户
> 3. 查询所有用户
> 4. 根据 ID 查询单个用户
> 5. 更新用户信息
> 6. 删除用户
> 请给出完整代码和运行说明。"

---

## AI 辅助练习

**练习 1：SQL 翻译官**
> 向 AI 说："我有一个 products 表，包含 id、name、price、category、stock 字段。请帮我写出以下查询的 SQL：
> 1. 查找价格低于 100 元的所有商品
> 2. 查找库存为 0 的商品
> 3. 按价格从高到低排序
> 4. 查找'电子产品'分类中价格最高的 5 个商品"

**练习 2：用自然语言写 SQL**
> "请帮我设计一个简单的图书管理系统的数据库，需要管理：书籍信息、作者信息、借阅记录。请给出建表语句和一些示例数据的插入语句。"

**练习 3：把数据库接入 API**
> "请帮我把上一章的待办事项 API 改造一下，把数据存到 SQLite 数据库中，而不是内存里。这样服务器重启后数据不会丢失。"

**练习 4：学习 ORM**
> "请用 Prisma（或 SQLAlchemy）重写上面的数据库操作代码，让我对比一下直接写 SQL 和用 ORM 的区别。"

---

## 本章小结

| 概念 | 一句话总结 |
|------|-----------|
| 数据库 | 永久保存数据的"超级文件柜" |
| 关系型数据库 | 数据存在整齐的表格中，表之间可以关联 |
| NoSQL 数据库 | 数据格式灵活，适合多变的数据结构 |
| CRUD | 四种基本操作：创建、读取、更新、删除 |
| SQL | 和关系型数据库"对话"的语言，语法接近英语 |
| JOIN | 把多张表连接起来查询 |
| ORM | 用编程语言操作数据库，不用直接写 SQL |

数据库是每个应用的基石。不管是简单的待办事项还是庞大的电商平台，底层都离不开数据库。好消息是，SQL 的基本语法非常直观，而且 AI 特别擅长写 SQL 查询——你只要用自然语言描述你想要什么数据，AI 就能帮你生成对应的 SQL。这是 AI 辅助编程中最实用的场景之一！
