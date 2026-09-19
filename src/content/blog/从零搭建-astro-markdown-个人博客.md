---
title: 从零搭建自己的 Astro + Markdown 博客：完整路线图
description: 从安装环境、创建 Astro 项目、用 Markdown 管理文章、制作页面，到 Git 推送与自动部署的一套完整流程。
pubDate: 2025-03-07
tags: [Astro, Markdown, 博客, 教程, Git]
---

这篇文章记录的是一条适合长期写作的个人博客路线：**Astro 负责把网站构建得轻快，Markdown 负责把文章写得简单，Git 负责保存版本并触发部署。**

它不依赖数据库，也不需要后台管理系统。新写一篇博客的核心动作只是：在 `src/content/blog/` 新建一个 `.md` 文件，填写文章信息，写正文，然后提交和推送。对于个人随笔、学习笔记、技术文章，这种方式稳定、透明，也容易迁移。

![Astro Markdown 博客从本地写作到部署的流程图](/images/astro-blog-flow.svg)

## 目录

1. 先确定要搭建的是什么
2. 准备环境：Node.js、VS Code、Git
3. 创建 Astro 项目并跑起来
4. 建立 Markdown 内容集合
5. 写首页、文章页、标签页与关于页
6. 用 CSS 做出自己的视觉主题
7. 添加文章、图片与草稿
8. 本地检查、Git 管理与自动部署
9. 日常维护清单与常见问题

---

## 第一章：先确定博客的工作方式

### 这套技术栈分别负责什么？

| 部分 | 使用的技术 | 它负责什么 |
| --- | --- | --- |
| 网站框架 | Astro | 组织页面、组件、路由，构建出静态网站 |
| 文章 | Markdown | 用纯文本写标题、段落、图片、代码和表格 |
| 内容规则 | Astro Content Collections | 规定每篇文章必须有哪些字段，并读取所有文章 |
| 样式 | CSS | 颜色、字体、间距、响应式布局 |
| 版本与发布 | Git + 远程仓库 | 保存历史、备份代码、触发自动部署 |
| 托管平台 | Vercel / Netlify / GitHub Pages 等 | 把构建结果公开到互联网 |

### 为什么选择 Astro + Markdown？

- **写作足够轻**：文章是普通 `.md` 文件，在 VS Code 中就能写。
- **加载很快**：静态博客在构建时生成 HTML、CSS 和资源，不需要每次访客打开都查数据库。
- **代码与文章在一起**：主题、文章、图片和历史记录都在一个文件夹，备份和迁移很简单。
- **不被平台绑定**：以后换部署平台，仍然保留全部 Markdown 文件。

这套方式的取舍也很明确：它没有像 WordPress 那样的在线后台；每次发表文章需要编辑本地文件、检查构建并推送。如果你喜欢在本地慢慢写、希望完全掌控内容，这恰恰是优点。

---

## 第二章：准备环境

### 1. 安装 Node.js

Astro 通过 Node.js 运行。按照 Astro 当前官方文档，建议使用 **Node.js 22.12.0 或更高版本**（不要用奇数版本）。安装完成后打开终端：

```bash
node --version
npm --version
```

**效果示例：**

```text
v22.x.x
10.x.x
```

只要两条命令都能显示版本号，环境就准备好了。若报“command not found”，通常是 Node.js 没安装完成，或安装后终端还没有重新打开。

### 2. 安装 VS Code 与建议扩展

用 [VS Code](https://code.visualstudio.com/) 打开整个博客文件夹，而不是只打开某个 `.md` 文件。推荐安装：

- **Astro** 官方扩展：让 `.astro` 文件拥有语法高亮、诊断和补全。
- **Markdown All in One**（可选）：提供更方便的 Markdown 快捷键和目录功能。
- **Markdownlint**（可选）：检查 Markdown 常见格式问题。

Markdown 本身的编辑与预览功能 VS Code 已内置。关于具体语法和预览快捷键，可以阅读本站的 [VS Code Markdown 写作指南](/blog/vscode-markdown-写作指南/)。

### 3. 安装 Git

Git 用来保存每次改动，并把项目推送到 GitHub、GitLab 等远程仓库：

```bash
git --version
```

第一次使用还要设置署名：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

Git 的完整日常流程、撤销边界和命令解释，请阅读本站的 [Git 命令完全入门](/blog/git-命令完全入门与日常工作流/)。

---

## 第三章：创建第一个 Astro 项目

### 1. 用官方向导初始化

在你希望存放项目的位置打开终端，运行：

```bash
npm create astro@latest
```

向导会依次询问项目文件夹、模板、TypeScript、是否安装依赖、是否初始化 Git 等选项。第一次创建时，建议这样选择：

| 向导问题 | 建议选择 | 原因 |
| --- | --- | --- |
| 项目位置 | `./my-blog` | 创建一个名为 `my-blog` 的新文件夹 |
| 模板 | Empty / Minimal | 从干净的项目开始，最容易理解每个文件 |
| TypeScript | Strict 或默认推荐项 | 能更早发现拼写与数据类型问题 |
| 安装依赖 | Yes | 自动执行所需安装 |
| 初始化 Git | Yes | 从第一天开始保留历史 |

若你已经决定了文件夹名称，也可以直接指定：

```bash
npm create astro@latest my-blog
cd my-blog
```

项目初始化后，安装依赖并启动开发服务器：

```bash
npm install
npm run dev
```

**效果示例：**

```text
astro  v7.x.x ready in ... ms

┃ Local    http://localhost:4321/
```

在浏览器打开这个本地地址。以后你修改 `.astro`、`.md` 或 `.css` 并保存，页面会自动刷新。

### 2. 认识项目目录

刚创建的项目会有一些默认文件。最终一个 Markdown 博客的核心结构可整理成下面这样：

```text
my-blog/
├── public/                    # 原样公开的静态文件
│   ├── avatar.jpg             # 个人头像
│   ├── favicon.png            # 浏览器标签页图标
│   └── images/                # 文章图片
├── src/
│   ├── components/            # 可重复的小组件
│   │   ├── PostFeed.astro     # 文章列表
│   │   └── SiteRail.astro     # 侧栏个人信息
│   ├── content/
│   │   └── blog/              # 每一篇 Markdown 文章
│   ├── layouts/
│   │   └── BaseLayout.astro   # 页面共用外壳、导航、页脚
│   ├── pages/                 # 文件即路由
│   │   ├── index.astro        # /
│   │   ├── about.astro        # /about/
│   │   ├── blog/[...slug].astro # /blog/文章文件名/
│   │   └── tags/              # 标签页
│   ├── styles/
│   │   └── global.css         # 全站视觉规则
│   └── content.config.ts      # 文章字段规则
├── package.json               # npm 脚本与依赖
└── astro.config.mjs           # Astro 的站点级配置（按需创建）
```

这张图里最值得记住的两条规则：

1. `src/pages/` 中的文件决定 URL 路径。
2. `src/content/blog/` 中的每一个 `.md` 文件就是一篇文章。

---

## 第四章：让 Astro 认识你的 Markdown 文章

### 1. 创建内容集合配置

在 `src/` 下创建 `content.config.ts`。它相当于文章资料的“字段合同”：没有标题、摘要或日期的文章会被检查出来，避免发布后列表页缺内容。

```ts
import { defineCollection } from 'astro:content';
import { glob } from 'astro/loaders';
import { z } from 'astro/zod';

const blog = defineCollection({
  loader: glob({ base: './src/content/blog', pattern: '**/*.md' }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),
    updatedDate: z.coerce.date().optional(),
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(false),
  }),
});

export const collections = { blog };
```

每一部分的意义：

| 代码 | 作用 |
| --- | --- |
| `glob(...)` | 扫描 `src/content/blog/` 下所有 `.md` 文件 |
| `title` | 文章标题，必须有 |
| `description` | 列表摘要，必须有 |
| `pubDate` | 发布日期；`z.coerce.date()` 会把日期文本转成日期对象 |
| `updatedDate` | 可选的修改日期 |
| `tags` | 标签数组；没写时默认为空数组 |
| `draft` | 草稿开关；没写时默认为 `false` |

### 2. 写第一篇 Markdown 文章

创建 `src/content/blog/你好-世界.md`：

```md
---
title: 你好，世界
description: 这是我的第一篇 Astro Markdown 博客文章。
pubDate: 2026-09-19
tags: [博客, 随笔]
---

## 从这里开始写

Markdown 让写作回到内容本身。

![一张示例图片](/images/example.jpg)
```

开头三横线之间的部分叫 **Frontmatter**，用于文章元数据；后面才是读者会看到的正文。日期请写成 `YYYY-MM-DD`，标签要用方括号和逗号分隔。

### 3. 草稿怎么做？

还不想把文章展示到首页时，在 Frontmatter 加：

```md
draft: true
```

然后在读取文章的页面中筛掉草稿：

```ts
const posts = await getCollection('blog', ({ data }) => !data.draft);
```

完成后删除 `draft: true` 或写成 `draft: false`，文章会在下次本地刷新或部署构建时出现。

---

## 第五章：用页面与组件生成博客

Astro 的基本思路是：**页面负责 URL，组件负责重复区域，布局负责所有页面共有的外壳。**

### 1. 建立全站外壳：`BaseLayout.astro`

创建 `src/layouts/BaseLayout.astro`：

```astro
---
import '../styles/global.css';

interface Props {
  title?: string;
  description?: string;
}

const {
  title = '我的博客',
  description = '记录正在学习与思考的事。',
} = Astro.props;
---

<!doctype html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width" />
    <meta name="description" content={description} />
    <link rel="icon" type="image/png" href="/favicon.png" />
    <title>{title}</title>
  </head>
  <body>
    <header class="site-header">
      <a class="brand" href="/">
        <img src="/avatar.jpg" alt="我的头像" />
        <span>我的博客</span>
      </a>
      <nav aria-label="主导航">
        <a href="/">文章</a>
        <a href="/tags">标签</a>
        <a href="/about">关于</a>
      </nav>
    </header>

    <main><slot /></main>
    <footer>© {new Date().getFullYear()} 我的博客</footer>
  </body>
</html>
```

`<slot />` 是关键：不同页面的内容会被放进这个位置。`title` 和 `description` 是传进来的属性，浏览器标签页与搜索摘要可以使用它们。

### 2. 首页：读取并列出所有文章

在 `src/pages/index.astro` 里读取内容集合：

```astro
---
import { getCollection } from 'astro:content';
import BaseLayout from '../layouts/BaseLayout.astro';

const posts = (await getCollection('blog', ({ data }) => !data.draft))
  .sort((a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf());
---

<BaseLayout title="我的博客" description="记录正在学习与思考的事。">
  <section class="hero">
    <p>PERSONAL NOTES</p>
    <h1>把生活写成可以回看的文字。</h1>
  </section>

  <section aria-labelledby="recent-posts">
    <h2 id="recent-posts">最近更新</h2>
    <ul class="post-list">
      {posts.map((post) => (
        <li>
          <time datetime={post.data.pubDate.toISOString()}>
            {post.data.pubDate.toLocaleDateString('zh-CN')}
          </time>
          <a href={`/blog/${post.id}/`}>{post.data.title}</a>
          <p>{post.data.description}</p>
        </li>
      ))}
    </ul>
  </section>
</BaseLayout>
```

这里的 `getCollection('blog')` 对应前一章的 `collections = { blog }`。`post.id` 默认来自文件路径，因此 `你好-世界.md` 会成为 `/blog/你好-世界/`。

当文章变多后，可以把每页显示数量、分页按钮与文章列表抽到 `src/components/PostFeed.astro`，让首页和归档页共用同一套列表逻辑。

### 3. 文章详情页：动态路由 `[...slug].astro`

创建 `src/pages/blog/[...slug].astro`：

```astro
---
import { getCollection, render } from 'astro:content';
import BaseLayout from '../../layouts/BaseLayout.astro';

export async function getStaticPaths() {
  const posts = await getCollection('blog', ({ data }) => !data.draft);
  return posts.map((post) => ({
    params: { slug: post.id },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await render(post);
---

<BaseLayout title={`${post.data.title}｜我的博客`} description={post.data.description}>
  <article class="article">
    <header>
      <time datetime={post.data.pubDate.toISOString()}>
        {post.data.pubDate.toLocaleDateString('zh-CN')}
      </time>
      <h1>{post.data.title}</h1>
      <p>{post.data.description}</p>
    </header>
    <div class="prose"><Content /></div>
  </article>
</BaseLayout>
```

`[...slug]` 是 Astro 的动态路由写法，能接住所有文章文件名。`getStaticPaths()` 在构建时为每篇文章生成独立 HTML 页面；`render(post)` 把 Markdown 正文转换为页面内容。

### 4. 标签页的逻辑

标签并不是单独手写的列表，而是从所有文章的 `tags` 自动统计出来。基本流程是：读取所有非草稿文章 → 合并所有标签 → 去重 → 排序 → 按标签筛选文章。

```ts
const allPosts = await getCollection('blog', ({ data }) => !data.draft);
const tags = [...new Set(allPosts.flatMap((post) => post.data.tags))].sort();
```

标签首页可以列出 `tags`；动态标签详情页中用 `post.data.tags.includes(tag)` 筛选。这样只要在 Markdown Frontmatter 增加一个标签，标签页会自动更新。

### 5. 关于页与头像

`src/pages/about.astro` 适合放个人简介、联系方式、写作主题与常用工具。头像、网站 favicon 等不需要经过构建处理的公共资源放在 `public/`：

```text
public/avatar.jpg     → 网站里写 /avatar.jpg
public/favicon.png    → 浏览器标签页图标
public/images/book.jpg → 文章里写 /images/book.jpg
```

浏览器路径以 `/` 开头时，代表从 `public/` 的根目录开始寻找。

---

## 第六章：用 CSS 做出自己的主题

### 1. 把全站样式集中在一个文件

创建 `src/styles/global.css`，并像前面的 `BaseLayout.astro` 那样导入它。先设一套可调整的颜色变量：

```css
:root {
  --paper: #fbfaf4;
  --ink: #1f2726;
  --muted: #64716f;
  --line: #ddd7c8;
  --accent: #9a3b32;
  --teal: #4e7773;
}

* { box-sizing: border-box; }

body {
  margin: 0;
  background: var(--paper);
  color: var(--ink);
  font-family: system-ui, -apple-system, "PingFang SC", sans-serif;
  line-height: 1.75;
}

a { color: inherit; }
img { max-width: 100%; height: auto; }
```

变量的好处是：以后想整体换成蓝色、深色或更淡的纸张色，只需改几行，而不是在每个选择器里找颜色。

### 2. 控制阅读宽度，而不是无限铺满屏幕

文章正文过宽会很难读，过窄又会让标题频繁换行。可以先使用下面这个稳妥的范围：

```css
.article {
  width: min(100% - 2rem, 780px);
  margin: 0 auto;
  padding: 3rem 0 5rem;
}

.prose {
  font-size: 1rem;
  line-height: 1.9;
}

.prose h2 { margin-top: 3.25rem; }
.prose p { margin: 1.1rem 0; }
.prose img {
  display: block;
  width: min(100%, 500px);
  margin: 2rem auto;
  border-radius: 0.5rem;
}
```

`width: min(...)` 的意思是：小屏幕保留左右边距，大屏幕时正文最多 780px 宽。图片限制在 500px，能避免一张普通截图撑满文章页。

### 3. 首页与侧栏：用 Grid 而不是绝对定位

若想让首页有文章区与个人资料区，可以使用 CSS Grid：

```css
.home-grid {
  display: grid;
  grid-template-columns: minmax(0, 1fr) 280px;
  gap: clamp(2rem, 5vw, 5rem);
  align-items: start;
}

@media (max-width: 820px) {
  .home-grid { grid-template-columns: 1fr; }
}
```

桌面端左边是文章、右边是侧栏；屏幕小于 820px 时，自动改成单列。不要用大量 `position: absolute` 堆出主布局，否则文章变长、窗口变窄时很容易重叠。

### 4. 每次改样式要检查三件事

1. 桌面宽度：标题是否过大、正文是否太宽、右侧栏是否留白过多。
2. 手机宽度：导航会不会挤在一行、表格会不会溢出、图片是否超出屏幕。
3. 真实文章页：首页好看不代表长文章、代码块、表格、图片也好看。

---

## 第七章：日常添加文章与图片

### 新建文章的最小模板

每次在 `src/content/blog/` 创建新 `.md` 文件，复制下面的模板：

```md
---
title: 文章标题
description: 用一句话告诉读者这篇文章会讲什么。
pubDate: 2026-09-19
tags: [标签一, 标签二]
---

## 第一节

从这里开始写正文。
```

建议文件名使用清晰、稳定的名称，例如：

```text
2026-09-读完一本书.md
如何整理照片.md
git-命令完全入门与日常工作流.md
```

文件名会影响文章 URL。文章已发布后，尽量不要随意重命名；必须改名时，应考虑给旧链接设置重定向，避免外部分享链接失效。

### 添加图片的推荐方式

1. 把图片放入 `public/images/`，例如 `public/images/desk.jpg`。
2. 在文章里写：

   ```md
   ![深夜桌面上的笔记本和咖啡](/images/desk.jpg)
   ```

3. 在本地博客页面检查图片，而不仅仅在 VS Code Markdown Preview 中检查。

图片文件名建议使用英文小写、短横线，例如 `my-desk.jpg`。避免空格、很长的中文名和重复名称；它们并非一定不能用，但在 URL 和迁移中更容易出问题。

### 文章为什么没有出现在首页？

依次检查：

- 文件是否真的在 `src/content/blog/`，而不是项目外部或 `public/`？
- Frontmatter 是否有 `title`、`description`、`pubDate`、`tags`？
- 是否写了 `draft: true`？
- 日期格式是否是合法的 `YYYY-MM-DD`？
- 开发服务器是否仍在运行，终端有没有报错？

最后运行 `npm run build`。构建错误通常会告诉你具体的文件和字段。

---

## 第八章：本地检查、Git 与自动部署

### 1. 三个最常用的 npm 命令

```bash
npm run dev       # 开发服务器：写作/改样式时用
npm run build     # 正式构建：发布前必须跑一次
npm run preview   # 在本地预览已经生成的 dist/ 网站
```

效果与用途：

| 命令 | 结果 | 什么时候运行 |
| --- | --- | --- |
| `npm run dev` | 启动带自动刷新的本地网站 | 正在开发时 |
| `npm run build` | 生成 `dist/` 静态文件，并检查构建 | 每次推送前 |
| `npm run preview` | 用本地服务器打开 `dist/` | 怀疑构建后才出现问题时 |

### 2. 将项目推到远程仓库

如果项目在创建向导中没有初始化 Git，可在项目根目录运行：

```bash
git init -b main
git add .
git commit -m "初始化 Astro Markdown 博客"
```

然后在 GitHub、GitLab 或其他服务创建一个**空仓库**，复制仓库 HTTPS 或 SSH 地址。回到终端：

```bash
git remote add origin https://github.com/你的用户名/my-blog.git
git push -u origin main
```

第一次推送后，以后的日常更新通常是：

```bash
git status
npm run build
git add "src/content/blog/新文章.md"
git commit -m "新增文章：新文章标题"
git push
```

### 3. 连接托管平台，实现自动部署

可以选择 Vercel、Netlify、Cloudflare Pages、GitHub Pages 等静态托管服务。通用的自动部署逻辑是：

1. 在托管平台登录并选择“导入 Git 仓库”。
2. 授权它访问你的博客仓库。
3. 选择 `main` 作为生产分支。
4. 确认构建配置：

   ```text
   Build Command: npm run build
   Publish Directory: dist
   ```

5. 点击部署，等待首次构建成功。

以后每次执行 `git push` 到 `main`，平台会自动拉取新提交、运行 `npm run build`，再发布新的 `dist/` 内容。若部署失败，先看平台的构建日志；常见原因和本地 `npm run build` 失败原因相同。

### 4. 自定义域名（可选）

部署成功后，平台会先给一个临时网址。要用自己的域名时：

1. 在平台项目设置中添加域名，例如 `blog.example.com`。
2. 平台会显示需要在域名服务商处添加的 DNS 记录。
3. 按它给出的记录逐字添加，等待 DNS 生效。
4. 确认 HTTPS 证书已签发，再将这个地址分享给读者。

DNS 记录由你选择的托管平台与域名服务商决定，不要照搬别人的记录值；以平台当前页面给出的值为准。

---

## 第九章：从空项目到稳定博客的检查清单

### 首次上线前

- [ ] `npm run dev` 能打开本地首页。
- [ ] 至少有一篇 Markdown 文章，且列表页能看到它。
- [ ] 文章详情页、标签页、关于页都能打开。
- [ ] 浏览器标签标题、favicon、头像和图片都正常。
- [ ] 手机宽度下导航、图片、表格没有溢出。
- [ ] `npm run build` 成功完成。
- [ ] `.gitignore` 已忽略 `node_modules/`、`dist/`、`.env`。
- [ ] 远程仓库中没有密码、令牌、私人证件或不该公开的图片。
- [ ] 托管平台构建命令是 `npm run build`，发布目录是 `dist`。

### 每次发布新文章前

- [ ] 标题、摘要、日期与标签都填了。
- [ ] 图片有意义的替代文字，路径在本地页面中正常。
- [ ] 文章标题层级从 `##` 开始，段落和表格排版自然。
- [ ] `npm run build` 成功。
- [ ] `git diff --staged` 中只有本次想发布的内容。
- [ ] 推送后查看托管平台部署是否成功。

### 维护的边界：什么时候需要改代码？

| 你想改什么 | 首先修改哪里 |
| --- | --- |
| 新写一篇文章 | `src/content/blog/新的文章.md` |
| 修改已有文章 | 对应的 `.md` 文件 |
| 修改头像/标签页图标 | `public/avatar.jpg`、`public/favicon.png` |
| 修改首页标题、简介、侧栏文案 | 首页或侧栏组件中的文字 |
| 修改导航、页脚、网站标题模板 | `src/layouts/BaseLayout.astro` |
| 修改文章页字体、图片大小、主题颜色 | `src/styles/global.css` |
| 增加文章字段或修改校验规则 | `src/content.config.ts` |
| 新增一个固定页面 | `src/pages/` 下新增 `.astro` 文件 |

不确定时，先用 VS Code 全局搜索一段你在页面上看得到的文字；通常能最快定位它在哪个 `.astro`、`.md` 或 `.css` 文件中。

---

## 最后：这套博客真正的日常操作

搭建完成后，日常写作并不复杂：

```text
新建 Markdown 文章
      ↓
在 VS Code 预览、补图片、检查标题层级
      ↓
npm run build
      ↓
git add → git commit → git push
      ↓
托管平台自动重新发布
```

技术栈的价值不在于文件多，而在于每个文件的职责清楚：文章归 Markdown，页面归 `src/pages/`，重复结构归组件，视觉归 CSS，发布归 Git 与托管平台。只要保持这个边界，博客写得越久也不会变得难以维护。

## 参考资料

- [Astro 官方：安装与项目初始化](https://docs.astro.build/zh-cn/install-and-setup/)
- [Astro 官方：内容集合](https://docs.astro.build/zh-cn/guides/content-collections/)
- [Astro 官方：CLI 命令](https://docs.astro.build/zh-cn/reference/cli-reference/)
- [Astro 官方：部署静态站点](https://docs.astro.build/zh-cn/guides/deploy/)
