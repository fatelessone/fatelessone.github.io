---
title: "这套博客怎么修改：完整本地使用手册"
description: "从写文章、改文案、换头像，到调整布局、分页、配色与发布前检查的项目说明。"
pubDate: 2025-09-17
updatedDate: 2026-09-19
tags: ["Astro", "使用说明", "博客"]
---

> 这篇文章是本博客的“使用说明书”。以后忘记某一处文字、图片、颜色或布局在哪里改时，先回到这里查找。所有路径都相对于项目根目录（也就是打开终端后所在的博客文件夹）。

## 先理解这套博客是怎么工作的

这个项目使用 **Astro + Markdown**。可以把它理解为两部分：

1. **内容**：每一篇文章都是 `src/content/blog/` 中的一个 Markdown 文件；
2. **外观和页面**：`.astro` 文件负责把文章组合成首页、详情页、标签页，`global.css` 负责颜色、字体、间距和响应式布局。

当运行 `npm run build` 时，Astro 会读取全部 Markdown，生成纯静态 HTML 到 `dist/`。因此，**不要手动修改 `dist/`**：它只是构建结果，下次构建时会重新生成。

## 项目地图：每个文件负责什么

```text
Fatelessone's blog/
├── public/                         # 不经过编译、可被浏览器直接访问的静态资源
│   ├── avatar.jpg                  # 网站头像：页头与右侧个人卡都会使用
│   └── favicon.png                 # 浏览器标签页图标
├── src/
│   ├── content/
│   │   └── blog/                   # 所有 Markdown 文章都放在这里
│   │       ├── hello-astro.md      # 就是当前这篇使用手册
│   │       └── 外区 apple id 注册.md
│   ├── components/
│   │   ├── PostFeed.astro          # 首页/归档页的文章列表和页码
│   │   └── SiteRail.astro          # 右侧个人资料、统计和常用标签
│   ├── layouts/
│   │   └── BaseLayout.astro        # 全站共同的页头、导航、favicon、页脚
│   ├── pages/
│   │   ├── index.astro             # 首页
│   │   ├── about.astro             # 关于页
│   │   ├── blog/[...slug].astro    # 每一篇文章的详情页模板
│   │   ├── page/[page].astro       # 第 2、3……页的文章归档模板
│   │   └── tags/                   # 标签总页与单个标签页
│   ├── styles/global.css           # 所有视觉样式：颜色、字体、布局、手机适配
│   └── content.config.ts           # 文章必须具备哪些字段的校验规则
├── astro.config.mjs                # Astro 的站点配置，目前保持默认
├── package.json                    # 开发、构建命令与依赖
└── README.md                       # 精简版项目说明
```

## 一、最常用操作：新增、编辑和隐藏文章

### 新增一篇文章

在 `src/content/blog/` 新建一个 `.md` 文件。文件名会成为网址的一部分：

```text
src/content/blog/my-note.md
→ https://你的域名/blog/my-note
```

也可以建立子文件夹：

```text
src/content/blog/notes/reading.md
→ https://你的域名/blog/notes/reading
```

复制下面模板，填写顶部的文章信息（也叫 **frontmatter**），然后在第二个 `---` 后写正文：

```md
---
title: "文章标题"
description: "首页卡片和搜索摘要中显示的一句话介绍。"
pubDate: 2026-09-19
updatedDate: 2026-09-19
tags: ["随笔", "阅读"]
# draft: true
---

这里开始写正文。

## 二级标题

支持 **粗体**、*斜体*、[链接](https://example.com)、列表和代码块。
```

字段说明：

| 字段 | 是否必填 | 用途 |
| --- | --- | --- |
| `title` | 必填 | 详情页标题、首页文章标题、浏览器标题。 |
| `description` | 必填 | 首页文章摘要和页面 `description`。建议一到两句话。 |
| `pubDate` | 必填 | 发布日期。首页和标签页会按这个日期倒序排列。 |
| `updatedDate` | 可选 | 最近更新日期，用于自己记录维护时间。 |
| `tags` | 可选 | 标签数组。例如 `tags: ["技术", "Astro"]`。 |
| `draft` | 可选 | 写成 `true` 时文章不会出现在首页、标签页和构建结果中。删除它或设为 `false` 即可发布。 |

### 文章内插入图片

推荐把自己拥有的图片放到 `public/images/`，例如：

```text
public/images/2026/desk.jpg
```

然后在文章中这样引用：

```md
![书桌上的笔记本](/images/2026/desk.jpg)
```

不要写电脑上的绝对路径，例如 `/Users/你的名字/Desktop/photo.jpg`；上线后服务器没有这条路径。当前全站对文章图片设置了最大宽度 `500px` 和最大高度 `400px`，样式在 `src/styles/global.css` 的 `.prose img`。

### 编辑或删除文章

- **改正文、标题、摘要或标签**：直接编辑对应 `.md` 文件并保存。
- **删除文章**：删除对应的 `.md` 文件。删除前若只是想暂时下线，优先加 `draft: true`。
- **改文章网址**：重命名文件或移动文件夹即可；旧网址会失效，因此已公开的文章要谨慎改名。

如需测试分页、长短标题或不同标签，可以临时在 `src/content/blog/samples/` 新建测试文章。测试完成后删除整个 `samples/` 文件夹即可；它不会影响博客系统本身。当前项目中没有保留测试文章。

## 二、修改首页

文件：`src/pages/index.astro`

首页顶部的文字就在下面这段附近：

```astro
<p class="eyebrow">PERSONAL NOTES · {new Date().getFullYear()}</p>
<h1>把生活的线索，写成可回看的字。</h1>
<p>这里是 Fatelessone 的个人存档：记录思考、阅读与正在发生的事。</p>
```

可以直接把三段文字换成你的博客名称、Slogan 和简介。`{new Date().getFullYear()}` 会自动显示当前年份；如果想固定年份，改成普通文字，例如 `PERSONAL NOTES · 2026`。

首页会自动读取全部非草稿文章，按 `pubDate` 从新到旧排序。这里的关键逻辑是：

```ts
const posts = (await getCollection('blog', ({ data }) => !data.draft)).sort(
  (a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf(),
);
```

通常不需要改它。只有在想让草稿也显示、或想按其他规则排序时，才需要调整。

## 三、修改文章列表、滚动区和分页

文章列表的显示样式与页码都在 `src/components/PostFeed.astro`。

### 每页显示多少篇

当前每页是 **6 篇**。这个数字有三个位置必须保持一致：

1. `src/pages/index.astro` 中的 `const postsPerPage = 6`；
2. `src/pages/page/[page].astro` 的 `getStaticPaths()` 函数中的 `const postsPerPage = 6`；
3. `src/components/PostFeed.astro` 中文章序号计算里的 `* 6`。

例如改为每页 8 篇，就把这三个 `6` 都改为 `8`。只改其中一个会导致页码、文章数量或列表序号不一致。

### 列表文字、序号和翻页按钮

仍在 `src/components/PostFeed.astro`：

- “最近更新”：`<h2 id="latest-posts">最近更新</h2>`；
- `PAGE 01 / 03` 的格式：同一段中的 `<span>`；
- “上一页”“下一页”：底部 `<nav class="pagination">`；
- 日期格式：文件顶部的 `formatDate()` 函数；
- 文章卡片中显示的日期、标题、摘要、标签：`posts.map(...)` 内部。

列表区域为什么会有滚动条？这是 `global.css` 中 `.scrollable-post-list` 的作用。它的 `max-height: 28rem` 控制最大高度；想让列表更高可以增大它，例如改为 `32rem`，想取消滚动则删除 `max-height` 和 `overflow-y: scroll`。

## 四、修改右侧个人资料栏

文件：`src/components/SiteRail.astro`

右侧栏在首页、分页页和每篇文章详情页中共用，因此**只修改这一份文件就会全站同步**。

以下文字可直接替换：

```astro
<p class="eyebrow">HELLO, I AM</p>
<h2><a href="/about">Fatelessone</a></h2>
<p>写字，阅读，偶尔整理生活的线索。</p>
<a class="text-link" href="/about">关于这个人 →</a>
```

这里同时自动显示：

- `文章`：当前所有非草稿文章数量；
- `标签`：所有非草稿文章中的去重标签数；
- `常用标签`：按名称排序后的前 8 个标签；
- “查看全部 N 个标签”：进入 `/tags`。

如果想显示更多或更少的常用标签，修改文件顶部这一行：

```ts
const featuredTags = tags.slice(0, 8);
```

例如改为 `tags.slice(0, 6)`，右栏只显示 6 个标签。

## 五、修改头像、浏览器图标和导航

### 网站头像

文件：`public/avatar.jpg`

它同时用于：

- 顶部导航左侧小头像；
- 首页、分页页和文章详情页右栏的圆形个人头像。

要替换时，准备一张尽量方形的 JPG 图片，覆盖 `public/avatar.jpg`，并保持文件名不变。这样不需要改任何代码。浏览器可能缓存图片；替换后可强制刷新页面。

### 浏览器标签页图标

实际生效文件：`public/favicon.png`。

`src/layouts/BaseLayout.astro` 中有：

```html
<link rel="icon" type="image/png" href="/favicon.png" />
```

因此要换标签页图标，替换 `public/favicon.png` 即可。建议使用带透明背景的正方形 PNG，推荐至少 `256 × 256`。浏览器会强缓存 favicon；如果看见旧图标，关闭该标签页后重开、清缓存或强制刷新。

`public/favicon.svg` 与 `public/favicon.ico` 目前不被页面引用，可以保留但不必编辑。

### 顶部导航和页脚

文件：`src/layouts/BaseLayout.astro`

这里负责全站都会出现的内容：

- 导航品牌名 `Fatelessone`；
- “文章 / 标签 / 关于”三个导航文字及链接；
- 浏览器 `<title>`、描述和 favicon；
- 底部的 `© 年份 Fatelessone. Built with Astro.`。

例如改博客名，需要至少替换两处：导航中的 `<span>Fatelessone</span>`，以及页脚里的 `Fatelessone`。每一个页面传入的浏览器标题在各页面文件的 `<BaseLayout title="...">` 中修改。

## 六、修改“关于”页、标签页与文章详情页

### 关于页

文件：`src/pages/about.astro`

这是一个普通静态页面。修改其中的标题和段落即可，页面地址固定是 `/about`。

### 标签总页

文件：`src/pages/tags/index.astro`

这里自动收集全部标签，展示 `/tags` 页面。想改“标签”“用主题找到感兴趣的文章”等文案，就在这个文件中搜索并替换相应文字。

### 单个标签页

文件：`src/pages/tags/[tag].astro`

不要把 `[tag]` 改成普通文件名。方括号表示动态路由：它会为每一个标签自动生成一个页面，例如：

```text
/tags/阅读
/tags/Astro
```

这个文件里的 `tag-post-card` 负责标签页中每篇文章的日期、标题和摘要。日期与标题被分为两列，以避免中文被挤成一字一行。

### 文章详情页

文件：`src/pages/blog/[...slug].astro`

这同样是动态路由，不要改名。它会把所有 Markdown 渲染成 `/blog/...` 页面，并自动显示：返回链接、发布日期、文章标题、摘要、标签、正文和右侧资料栏。

如果要改文章详情页顶部的固定文案，搜索：

```astro
<a class="back-link" href="/">← 所有文章</a>
<p class="post-date">发布于 ...</p>
```

### 第 2 页及之后的归档页

文件：`src/pages/page/[page].astro`

首页是 `/`，只有超过每页文章数量后才会生成 `/page/2`、`/page/3` 等页面。这里也共用了 `PostFeed.astro` 和 `SiteRail.astro`，所以文章列表和右侧栏会和首页保持一致。

## 七、修改颜色、字体、间距和手机布局

文件：`src/styles/global.css`。这是唯一的全站样式文件，改外观时先从这里找。

### 先改颜色变量

文件最顶部的 `:root` 是全站配色表：

```css
--paper: #faf8f1;        /* 页面背景暖白色 */
--paper-strong: #f0ede2; /* 引用框、代码背景 */
--ink: #20241f;          /* 主要文字 */
--muted: #65706a;        /* 次要文字 */
--line: #ddd7c7;         /* 分割线 */
--red: #8b3027;          /* 强调色、链接悬停 */
--teal: #4e716c;         /* 日期、辅助链接 */
--gold: #bd8124;         /* 文章列表序号 */
```

优先修改变量，而不是在后面的每一条 CSS 中到处换颜色；这样整站更容易保持一致。

### 常见外观需求对应的 CSS 区块

| 想改什么 | 在 `global.css` 搜索什么 |
| --- | --- |
| 页面最大宽度 | `.container` |
| 页头高度、品牌和导航 | `.site-header`、`.nav`、`.brand`、`.nav-links` |
| 首页大标题和简介 | `.hero`、`.hero h1` |
| 首页左右两栏比例 | `.home-grid`，当前右栏为 `250px` |
| 文章列表高度和滚动条 | `.scrollable-post-list` |
| 文章卡片标题和摘要 | `.post-card`、`.post-card h3` |
| 右侧栏、头像和统计 | `.profile-rail`、`.profile-card`、`.profile-avatar`、`.site-stats` |
| 标签按钮 | `.tag-list a`、`.tag-directory a` |
| 页码 | `.pagination`、`.page-numbers` |
| 文章详情页标题 | `.article-header h1` |
| 正文、列表、引用、图片 | `.prose`、`.prose ul`、`.prose blockquote`、`.prose img` |
| 手机端样式 | 文件末尾的 `@media (max-width: 820px)` 与 `@media (max-width: 580px)` |

### 改文字大小的建议

当前使用 `rem` 和 `clamp()`，这是为了在手机和桌面之间自动缩放。一般只微调数值，不要突然把单位改成 `px`。

- 首页主标题：`.hero h1`；
- 文章详情标题：`.article-header h1`；
- 正文：`.prose`，当前是 `1rem`；
- 正文二级标题：`.prose h2`；
- 文章图片：`.prose img`，当前最大宽度 `500px`、最大高度 `400px`。

改完后一定同时查看桌面和手机宽度。若只想为手机改小，在 `@media (max-width: 580px)` 中新增或修改对应规则，不要影响桌面版本。

## 八、路由速查表

| 地址 | 对应来源 | 说明 |
| --- | --- | --- |
| `/` | `src/pages/index.astro` | 首页，显示第 1 页文章。 |
| `/page/2` | `src/pages/page/[page].astro` | 从第 2 页开始的归档。 |
| `/blog/文件名` | `src/content/blog/文件名.md` | 某篇文章详情。 |
| `/blog/notes/reading` | `src/content/blog/notes/reading.md` | 子文件夹中的文章详情。 |
| `/tags` | `src/pages/tags/index.astro` | 所有标签。 |
| `/tags/阅读` | `src/pages/tags/[tag].astro` | 某个标签下的文章。 |
| `/about` | `src/pages/about.astro` | 关于页。 |

## 九、本地查看、构建与发布前检查

### 本地实时预览

在项目根目录运行：

```bash
npm run dev
```

终端会显示本地网址，通常是 `http://localhost:4321`。保存 Markdown、Astro 或 CSS 文件后，浏览器会自动刷新。

如果需要按项目约定以后台方式启动：

```bash
npx astro dev --background
npx astro dev status
npx astro dev logs
npx astro dev stop
```

开发页面底部出现的 Astro 工具栏只存在于开发模式，运行构建后部署的正式网站不会出现。

### 每次发布前必做

```bash
npm run build
```

构建成功说明：所有 Markdown 必填字段正确、动态页面能生成、Astro 代码没有语法错误。构建失败时优先看终端提示的文件名和行号。

之后再提交并推送：

```bash
git add .
git commit -m "更新博客"
git push
```

如果部署平台已连接仓库，推送后会自动重新构建并更新线上网站。Git 会保留旧提交；线上展示的是最新构建结果，不会抹掉历史记录。

## 十、常见问题排查

### 新文章没有出现在首页

按顺序检查：

1. 文件是否确实在 `src/content/blog/` 或其子文件夹中；
2. 扩展名是否是 `.md`；
3. 是否误写了 `draft: true`；
4. `title`、`description`、`pubDate` 是否都存在；
5. 执行 `npm run build`，查看报错。

### 标签页没有新标签

检查文章 frontmatter 是否使用了正确数组格式：

```yaml
tags: ["阅读", "随笔"]
```

保存后刷新页面；标签页与右侧统计会自动重建。

### 头像或 favicon 换了但浏览器还是旧图

这是浏览器缓存。先确认文件名仍是 `public/avatar.jpg` 或 `public/favicon.png`，然后强制刷新、清理站点缓存，或关闭标签页后重新打开。

### 修改 CSS 后页面异常

优先撤回刚改的一小段，再一次只调整一个选择器。改颜色时优先改 `:root` 变量；改手机效果时只动文件末尾的媒体查询。每次修改后运行 `npm run build`。

---

如果未来新增搜索、评论、RSS、多语言、深色模式或新的栏目，建议先在本文补上对应文件和操作步骤。这样这份手册会一直和博客一起成长。
