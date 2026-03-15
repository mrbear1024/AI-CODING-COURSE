# 第10章：编程基本概念 — 读懂代码的"最小知识包"

## 引言

你不需要会写代码，但你需要**能读懂代码**。就像你不需要会做一道菜，但看到菜谱时，你至少要知道"切丁"、"焯水"、"大火收汁"是什么意思。

这一章介绍编程中最核心的几个概念。掌握它们之后，你就能看懂 AI 生成的绝大多数代码，并且有能力提出修改意见。

---

## 10.1 变量（Variable）— 贴了标签的盒子

### 概念讲解

变量（Variable）就是一个**带名字的容器**，用来存放数据。你给它取个名字，往里面放东西，以后用名字就能取出来。

### 类比说明

想象你有一排收纳盒，每个盒子上贴着标签：

- 盒子 `name` 里放的是 `"小明"`
- 盒子 `age` 里放的是 `25`
- 盒子 `is_student` 里放的是 `true`（是/否）

你可以随时打开盒子看里面是什么，也可以把旧东西拿出来换成新的。

### 代码示例

```python
# Python
name = "小明"          # 创建一个变量，存放文字
age = 25              # 创建一个变量，存放数字
is_student = True     # 创建一个变量，存放"是"或"否"

print(name)           # 输出：小明
age = 26              # 把盒子里的 25 换成 26
```

```javascript
// JavaScript
let name = "小明";       // let 表示"让"——让 name 等于"小明"
const age = 25;          // const 表示"常量"——放进去就不能换了
let isStudent = true;
```

**关键区别**：Python 直接写名字就行；JavaScript 需要用 `let`（可变）或 `const`（不可变）来声明。

---

## 10.2 数据类型（Data Type）— 盒子里能放什么？

### 概念讲解

不同的盒子适合放不同的东西。编程中，数据有不同的"类型"（Type），计算机需要知道每个数据是什么类型才能正确处理它。

### 常见数据类型

| 类型 | 英文 | 例子 | 类比 |
|------|------|------|------|
| 数字 | Number | `42`, `3.14` | 计算器里的数 |
| 字符串 | String | `"你好"`, `"hello"` | 一串珠子——一个个字符串在一起 |
| 布尔值 | Boolean | `true`, `false` | 开关——只有开和关两种状态 |
| 数组/列表 | Array / List | `[1, 2, 3]`, `["苹果", "香蕉"]` | 购物清单——有序排列的一组东西 |
| 对象/字典 | Object / Dictionary | `{"name": "小明", "age": 25}` | 身份证——每条信息都有标签 |

### 代码示例

```python
# Python 示例
score = 95                              # 数字 (Number)
greeting = "欢迎来到 AI 编程课"           # 字符串 (String)
is_passed = True                        # 布尔值 (Boolean)
fruits = ["苹果", "香蕉", "橙子"]         # 列表 (List)
student = {"name": "小明", "grade": "A"} # 字典 (Dictionary)
```

> **记住**：字符串（String）要用引号包起来。`42` 是数字，`"42"` 是字符串——就像真钱和写在纸上的"42元"不是一回事。

---

## 10.3 条件判断（Conditional Statement）— 如果...就...

### 概念讲解

条件判断（Conditional Statement）让程序根据不同情况做不同的事。最常见的形式是 `if...else`（如果...否则...）。

### 类比说明

你每天出门前都在做条件判断：

- **如果**下雨，**就**带伞；**否则**，穿短袖出门
- **如果**余额大于 100 元，**就**可以下单；**否则**，提示余额不足

### 代码示例

```python
# Python
temperature = 35

if temperature > 30:
    print("太热了，开空调！")      # 温度超过 30 就执行这里
elif temperature > 15:
    print("天气不错，出去走走")     # elif = else if（否则如果）
else:
    print("有点冷，多穿点")        # 以上条件都不满足，执行这里
```

```javascript
// JavaScript（逻辑完全一样，只是写法稍有不同）
let temperature = 35;

if (temperature > 30) {
    console.log("太热了，开空调！");
} else if (temperature > 15) {
    console.log("天气不错，出去走走");
} else {
    console.log("有点冷，多穿点");
}
```

注意看：Python 用**缩进**（Indentation）来表示代码的层级关系，JavaScript 用**花括号** `{}` 来包裹。逻辑是一样的，只是"方言"不同。

---

## 10.4 循环（Loop）— 重复做同一件事

### 概念讲解

循环（Loop）让计算机重复执行一段代码，直到满足某个条件为止。最常见的是 `for` 循环和 `while` 循环。

### 类比说明

想象你在工厂流水线上贴标签：

- **for 循环**：给这 100 个箱子每个都贴一张标签（明确知道要做多少次）
- **while 循环**：一直贴标签，直到标签用完为止（不确定次数，但知道什么时候停）

### 代码示例

```python
# for 循环：遍历一个列表
shopping_list = ["牛奶", "面包", "鸡蛋"]

for item in shopping_list:
    print(f"需要买：{item}")

# 输出：
# 需要买：牛奶
# 需要买：面包
# 需要买：鸡蛋
```

```python
# while 循环：持续执行直到条件不满足
count = 0

while count < 3:
    print(f"这是第 {count + 1} 次循环")
    count = count + 1    # 每次循环后，count 增加 1
```

> **注意**：`while` 循环如果忘了更新条件（比如忘了写 `count = count + 1`），程序就会永远循环下去，变成"死循环"（Infinite Loop）——就像一首歌单曲循环，永远不停。

---

## 10.5 函数（Function）— 可重复使用的"菜谱"

### 概念讲解

函数（Function）是一段有名字的代码，你可以随时"调用"（Call）它来执行。你也可以给它"原料"（参数，Parameter），它会返回"成品"（返回值，Return Value）。

### 类比说明

函数就像一份菜谱：

- **菜谱名**：`make_coffee`（做咖啡）
- **需要的原料（参数）**：咖啡豆、水、牛奶
- **做出的成品（返回值）**：一杯拿铁

你不需要每次都从头描述怎么做咖啡，只要说"按照 `make_coffee` 这个菜谱来"就行了。

### 代码示例

```python
# 定义一个函数（写菜谱）
def calculate_discount(price, discount_rate):
    """计算打折后的价格"""
    final_price = price * (1 - discount_rate)
    return final_price

# 调用函数（按菜谱做菜）
result = calculate_discount(100, 0.2)   # 原价 100，打 8 折
print(f"折后价：{result} 元")            # 输出：折后价：80.0 元

# 可以反复调用
result2 = calculate_discount(200, 0.3)  # 原价 200，打 7 折
print(f"折后价：{result2} 元")           # 输出：折后价：140.0 元
```

**函数的威力**：写一次，用无数次。AI 生成的代码中到处都是函数。当你看到 `def`（Python）或 `function`（JavaScript）这个关键词时，就知道——这是在定义一份"菜谱"。

---

## 实操练习

### 练习 1：看代码猜输出

不要运行代码，先自己猜结果，然后让 AI 验证：

```python
x = 10
y = 3
if x > y:
    result = x + y
else:
    result = x - y
print(result)
```

问 AI："这段代码的输出是什么？请逐行解释。"对比你的猜测和 AI 的回答。

### 练习 2：让 AI 用你听得懂的话解释代码

把以下代码发给 AI 助手：

```javascript
function greetUsers(names) {
    for (let i = 0; i < names.length; i++) {
        if (names[i] === "Admin") {
            console.log("Welcome back, Admin!");
        } else {
            console.log("Hello, " + names[i] + "!");
        }
    }
}
greetUsers(["Alice", "Admin", "Bob"]);
```

提示词："请用中文逐行解释这段 JavaScript 代码，并告诉我它用到了哪些编程概念（变量、条件、循环、函数）。"

### 练习 3：用自然语言让 AI 写代码

给 AI 这个需求：

> "请用 Python 写一个函数，输入一个数字列表，返回其中所有大于 60 分的成绩。请在每行加上中文注释。"

拿到代码后，试着用本章学到的概念去理解每一行。

---

## AI 辅助练习

1. "请给我出 5 道'看代码猜输出'的题目，难度从易到难，涉及变量、条件判断和循环。"
2. "请用'做饭'的比喻，解释 Python 中函数的参数和返回值。"
3. "JavaScript 的 `let`、`const` 和 `var` 有什么区别？请用收纳盒的比喻解释。"

---

## 本章小结

| 概念 | 英文 | 一句话理解 |
|------|------|-----------|
| 变量 | Variable | 贴了标签的盒子，里面装数据 |
| 数据类型 | Data Type | 盒子里装的东西的"种类"：数字、文字、对错、清单、档案 |
| 条件判断 | Conditional | 如果...就...否则...——让程序做选择 |
| 循环 | Loop | 重复做同一件事，直到达成条件 |
| 函数 | Function | 可重复使用的菜谱，有原料（参数）和成品（返回值） |

> **核心理念：你不需要背语法，你需要的是看到 AI 写的代码时，能说出"哦，这里是一个循环，它在遍历列表里的每个元素"。理解比记忆重要一万倍。**
