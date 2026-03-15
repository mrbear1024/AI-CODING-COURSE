# 第30章：性能与优化基础

## 引言：速度就是用户体验

你有没有打开过一个网页，等了三四秒还没加载出来，然后直接关掉了？你不是一个人——研究表明，**页面加载每多 1 秒，就会有约 10% 的用户流失**。

性能优化就像给汽车做保养：不是等到车坏了才修，而是让它一直保持最佳状态。好消息是，对于初学者来说，掌握几个核心原则就能让你的应用快上一大截。

---

## 一、前端性能优化

前端是用户直接感知的部分，优化效果最明显。

### 1. 图片优化（Image Optimization）

图片通常占网页总大小的 50% 以上。

**类比：** 你要寄一本相册给朋友。原图每张 10MB，100 张就是 1GB——快递费吓人。但如果你把图片压缩到合适的尺寸，可能只需要 50MB，效果几乎看不出区别。

**实操步骤：**

- **选择合适的格式：** 照片用 WebP 或 JPEG，图标/插画用 SVG 或 PNG
- **压缩图片：** 使用 TinyPNG、Squoosh 等工具
- **响应式图片：** 手机端不需要加载 4K 大图

```html
<!-- 根据屏幕大小加载不同尺寸的图片 -->
<img
  srcset="photo-400w.webp 400w, photo-800w.webp 800w, photo-1200w.webp 1200w"
  sizes="(max-width: 600px) 400px, (max-width: 1000px) 800px, 1200px"
  src="photo-800w.webp"
  alt="示例图片"
/>
```

### 2. 懒加载（Lazy Loading）

**类比：** 去自助餐厅吃饭，你不会一次把所有菜都端到桌上。而是先拿眼前想吃的，需要时再去取——这就是懒加载的思路。

```html
<!-- 图片懒加载：只在即将进入视口时才加载 -->
<img src="photo.webp" loading="lazy" alt="示例图片" />
```

对于页面下方用户可能根本看不到的图片，懒加载可以显著减少初始加载时间。

### 3. 代码分割（Code Splitting）

**类比：** 你写了一本 1000 页的百科全书，但读者只想查一个词条。与其让读者下载整本书，不如按需加载对应的章节。

```javascript
// 传统方式：一次性加载所有页面的代码
import AdminPanel from './AdminPanel';

// 代码分割：只在用户访问管理页面时才加载
const AdminPanel = React.lazy(() => import('./AdminPanel'));
```

现代构建工具（Vite、Webpack）和框架（Next.js）会自动帮你做基本的代码分割。

---

## 二、缓存（Caching）——不要重复搬砖

### 类比

你每天上班都要查同一趟地铁的发车时间。第一天你查了时刻表，记住了"早上 8:05 发车"。之后每天你就不需要再查了，直接去等——这就是缓存。

缓存的核心思想：**把用过的数据存起来，下次直接用，不用重新获取。**

### 浏览器缓存（Browser Cache）

浏览器会自动把下载过的文件（CSS、JS、图片）存在本地。下次访问同一个网页时，直接用本地的文件，不用重新下载。

服务器通过 HTTP 头部信息告诉浏览器："这个文件可以缓存 7 天"：

```
Cache-Control: max-age=604800
```

### 服务器缓存（Server Cache）

后端把频繁查询的数据库结果存在内存里（如 Redis），避免每次都查数据库。

**类比：** 图书馆管理员把最热门的书放在前台，而不是每次都跑去仓库找——从仓库取需要 5 分钟，从前台拿只要 5 秒。

```javascript
// 伪代码示例
async function getPopularPosts() {
  // 先检查缓存
  const cached = await redis.get('popular_posts');
  if (cached) return JSON.parse(cached);

  // 缓存没有，查数据库
  const posts = await db.query('SELECT * FROM posts ORDER BY views DESC LIMIT 10');

  // 存入缓存，设置 5 分钟过期
  await redis.set('popular_posts', JSON.stringify(posts), 'EX', 300);

  return posts;
}
```

---

## 三、CDN（内容分发网络，Content Delivery Network）

### 类比

假设你在北京开了一家面包店，上海的顾客要买面包。如果只有北京一家店，面包快递到上海需要 2 天。但如果你在上海也开了分店，顾客走路就能买到——这就是 CDN 的原理。

CDN 在全球各地部署了服务器节点。用户访问你的网站时，会自动从离他最近的节点获取静态文件（图片、CSS、JS），而不是从你的源服务器获取。

**使用方法很简单：** 大多数部署平台（Vercel、Cloudflare Pages）已内置 CDN，你不需要额外配置。

---

## 四、数据库优化基础

### 索引（Index）

**类比：** 一本 500 页的书没有目录，你要找某个章节只能一页一页翻。加了目录（索引）之后，直接翻到目录查页码。

```sql
-- 如果你经常按 email 查找用户，给 email 列加索引
CREATE INDEX idx_users_email ON users(email);
```

加索引后，查询速度可能从几秒变成几毫秒。但也不要给每个字段都加索引——索引会占用额外空间，也会拖慢写入速度。

### 查询优化（Query Optimization）

```sql
-- 不好：查出所有字段，即使你只需要名字
SELECT * FROM users;

-- 好：只查需要的字段
SELECT name, email FROM users WHERE active = true;
```

**原则：** 只查你需要的数据，不多也不少。

---

## 五、用 Lighthouse 衡量性能

Google Lighthouse 是一个免费的网页性能检测工具，内置在 Chrome DevTools 中。

**使用步骤：**

1. 打开 Chrome，访问你的网站
2. 按 `F12` 打开 DevTools
3. 找到 **Lighthouse** 标签页
4. 点击 **Analyze page load**
5. 等待报告生成

Lighthouse 会给你的页面打分（0-100），并提供具体的优化建议，包括：

- **Performance（性能）：** 加载速度、交互响应
- **Accessibility（无障碍）：** 对残障用户是否友好
- **Best Practices（最佳实践）：** 是否遵循 Web 开发规范
- **SEO（搜索引擎优化）：** 对搜索引擎是否友好

**提示：** 不需要追求满分。**90 分以上就是优秀**，把精力集中在分数最低的项目上。

---

## 六、不要过度优化

计算机科学领域有一句名言：

> "Premature optimization is the root of all evil."
> "过早的优化是万恶之源。" —— Donald Knuth

### 这句话的意思是：

1. **先让它能用**——确保功能正确
2. **再让它好用**——改善用户体验和代码结构
3. **最后让它快**——针对实际的性能瓶颈进行优化

不要在项目刚开始时就花大量时间优化一个可能根本不会成为瓶颈的地方。用数据说话：**先测量，再优化。** Lighthouse 和浏览器 DevTools 的 Performance 面板就是你的测量工具。

---

## AI 辅助练习

1. **性能诊断：** 用 Lighthouse 检测你的项目，把报告截图发给 AI，问："请帮我分析这份 Lighthouse 报告，最应该优先优化哪些项目？"
2. **图片优化实践：** 让 AI 帮你写一个脚本，自动把项目中的图片转换为 WebP 格式
3. **缓存策略设计：** 向 AI 描述你的应用场景，问："对于这样的应用，应该怎么设计缓存策略？"

---

## 本章小结

- 性能直接影响用户体验——**快就是好**
- 前端三大优化：图片优化、懒加载（Lazy Loading）、代码分割（Code Splitting）
- 缓存（Caching）的核心思想：**用过的数据存起来，下次直接用**
- CDN 让用户从最近的服务器获取内容，显著加速静态资源加载
- 数据库索引（Index）就像书的目录，能极大提升查询速度
- 使用 Lighthouse 工具客观测量性能，用数据指导优化
- 记住：**先让它能用，再让它变快**——不要过早优化
