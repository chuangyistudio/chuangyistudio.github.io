# 创翼电子工作室

基于 [Astro](https://astro.build) + [Svelte](https://svelte.dev) + [Tailwind CSS](https://tailwindcss.com) 构建的技术博客。

[**🖥️ 在线预览**](https://chuangyistudio.github.io)

## ✨ 特性

- 基于 Astro 7 + Svelte 5 + Tailwind CSS 4
- 流畅的页面过渡动画（Swup）
- 亮色 / 暗色模式，可跟随系统
- 可自定义主题色和 Banner
- 响应式设计
- 全文搜索（Pagefind）
- Markdown 扩展语法（提示框、GitHub 仓库卡片、数学公式）
- 文章目录（TOC）
- RSS 订阅
- 多语言支持

## 🚀 快速开始

1. 克隆仓库：
   ```sh
   git clone https://github.com/chuangyistudio/chuangyistudio.github.io.git
   cd chuangyistudio.github.io
   ```

2. 安装依赖（需要 [pnpm](https://pnpm.io)）：
   ```sh
   pnpm install
   ```

3. 本地开发：
   ```sh
   pnpm dev
   ```

4. 修改 `src/config.ts` 自定义站点配置。

5. 创建新文章：
   ```sh
   pnpm new-post <文件名>
   ```

## 📝 文章 Frontmatter

```yaml
---
title: 我的第一篇文章
published: 2026-01-01
description: 文章简介
image: ./cover.jpg
tags: [标签1, 标签2]
category: 前端
draft: false
lang: zh_CN
---
```

## ⚡ 常用命令

| 命令 | 说明 |
|:---|:---|
| `pnpm install` | 安装依赖 |
| `pnpm dev` | 启动开发服务器 |
| `pnpm build` | 构建生产版本 |
| `pnpm preview` | 预览构建产物 |
| `pnpm check` | 代码检查 |
| `pnpm format` | 格式化代码（Biome） |
| `pnpm new-post <name>` | 创建新文章 |

## 🛠️ 技术栈

- **框架**: [Astro 7](https://astro.build) + [Svelte 5](https://svelte.dev)
- **样式**: [Tailwind CSS 4](https://tailwindcss.com)
- **搜索**: [Pagefind](https://pagefind.app)
- **数学**: [KaTeX](https://katex.org)
- **部署**: [GitHub Pages](https://pages.github.com)

## 链接

- [技术博客](https://chuangyistudio.github.io)
- [内部文档](https://github.com/chuangyi-electronics/chuangyi-electronics_internal-docs)

## 📄 许可证

MIT License
