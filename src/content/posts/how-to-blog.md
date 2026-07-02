---
title: 如何在实验室博客发布文章
published: 2026-07-01
description: "手把手教你使用 Markdown 在创翼电子工作室博客上发布文章，从环境搭建到发布上线。"
tags: [教程, Markdown, 博客, Git]
category: 教程
draft: false
---

## 前言

实验室博客是我们对外展示技术成果、分享学习心得的重要窗口。本文将带你从零开始，学会如何在博客上发布文章。

---

## 第一步：环境准备

### 安装必要工具

你需要安装以下工具：

1. **Git** — [下载地址](https://git-scm.com/downloads)
2. **Node.js (v20+)** — [下载地址](https://nodejs.org/)
3. **一个代码编辑器** — 推荐 [VS Code](https://code.visualstudio.com/)
4. **pnpm** — 安装完 Node.js 后在终端执行：

```bash
npm install -g pnpm
```

### 克隆仓库

```bash
git clone https://github.com/cjh1230/chuangyistudio.github.io.git
cd chuangyistudio.github.io
pnpm install
```

---

## 第二步：创建文章

所有文章存放在 `src/content/posts/` 目录下。

### 文件命名

建议使用英文短横线命名，例如：`my-first-post.md`

### Frontmatter 格式

每篇文章开头需要写一段 frontmatter（用 `---` 包裹的 YAML 配置）：

```yaml
---
title: 我的第一篇文章
published: 2026-07-01
description: "这是一篇测试文章"
tags: [电子, 嵌入式, 教程]
category: 技术分享
draft: false
---
```

| 字段 | 说明 |
|------|------|
| `title` | 文章标题 |
| `published` | 发布日期，格式 `YYYY-MM-DD` |
| `description` | 文章简介，会显示在首页卡片上 |
| `tags` | 标签列表，用方括号包裹 |
| `category` | 分类名称 |
| `draft` | 是否为草稿，`true` 时不会公开发布 |

### 编写正文

正文使用 Markdown 语法。常用语法速查：

```markdown
# 一级标题
## 二级标题
### 三级标题

**加粗文字**
*斜体文字*

- 无序列表项 1
- 无序列表项 2

1. 有序列表项 1
2. 有序列表项 2

[链接文字](https://example.com)

![图片描述](./image.png)

> 引用文字

`行内代码`
```

---

## 第三步：本地预览

在项目根目录执行：

```bash
pnpm dev
```

浏览器打开 `http://localhost:4321`，就能实时预览你的文章效果。

---

## 第四步：提交发布

### 提交到 Git

```bash
git add .
git commit -m "feat: 添加新文章《我的第一篇文章》"
git push origin main
```

推送后，GitHub Actions 会自动构建并部署到 GitHub Pages，几分钟后就能在 [chuangyistudio.github.io](https://chuangyistudio.github.io) 看到你的文章了！

---

## 注意事项

- 🚫 不要在文章中泄露个人隐私信息（手机号、身份证等）
- ✅ 引用外部图片时尽量使用图床或相对路径
- ✅ 提交前先在本地预览，确保排版没问题
- ✅ commit message 要清晰，方便追溯

---

## 常见问题

### Q: 为什么我的文章没显示？

检查 frontmatter 中的 `draft` 是否为 `false`，以及 `published` 日期是否已过。

### Q: 图片不显示怎么办？

确保图片路径正确。放在文章同目录下，使用相对路径引用：`./my-image.png`

### Q: 构建失败了？

检查 Markdown 语法是否正确，特别是 frontmatter 的 YAML 格式。

---

有任何问题，随时在实验室群里问！🚀
