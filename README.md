# Fatelessone 的个人博客

这是一个使用 Astro 与 Markdown 构建的静态个人博客。

## 写一篇新文章

在 `src/content/blog/` 中新建一个 `.md` 文件，例如：

```text
src/content/blog/my-first-note.md
```

复制下面的模板并填写内容：

```md
---
title: "文章标题"
description: "用一句话说明这篇文章的内容。"
pubDate: 2026-09-18
tags: ["随笔", "技术"]
# draft: true
---

从这里开始写正文。

## 小标题

Markdown 支持 **加粗**、[链接](https://example.com)、列表和代码块。
```

文件名会成为文章网址：`my-first-note.md` 对应 `/blog/my-first-note`。文件可以放在子文件夹中，例如 `src/content/blog/notes/idea.md` 会对应 `/blog/notes/idea`。

`draft: true` 会让文章不显示在博客中，删除这一行或改成 `false` 即可发布。

首页每页显示 6 篇文章，文章列表可在固定区域内滚动浏览。第 7 篇文章起会自动生成第 2 页（`/page/2`）及底部页码，无需额外配置。

## 本地开发

```bash
npm run dev
```

浏览器打开终端显示的本地地址，通常为 `http://localhost:4321`。保存 Markdown 或代码后，页面会自动刷新。

## 构建发布版本

```bash
npm run build
npm run preview
```

构建产物位于 `dist/`，适合部署到 Vercel、GitHub Pages、Netlify 等静态托管服务。

## 项目结构

```text
src/
├── content/blog/       # 所有博客文章（只需在这里新增 Markdown）
├── content.config.ts   # 文章标题、日期、标签等字段的校验规则
├── layouts/            # 全站头部、导航和页脚
├── pages/              # 首页、文章详情、标签页与关于页
└── styles/             # 全站样式
```
