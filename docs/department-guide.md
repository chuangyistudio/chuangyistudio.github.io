# 实验室部门协作指南

## 架构

```
┌──────────────────────────────────────────────┐
│  chuangyistudio.github.io  （实验室门户）      │
│  - 实验室简介                                  │
│  - 部门介绍 + 入口链接                          │
│  - 招新 / 获奖 / 通知                          │
└──────┬───────────────┬────────────────────────┘
       │               │
       ▼               ▼
┌──────────────┐ ┌──────────────┐
│  视觉组站点    │ │  嵌入式组站点  │  ← 各部门自己维护
│  (独立 repo)  │ │  (独立 repo)  │
└──────────────┘ └──────────────┘
```

**一句话：主站只管"介绍 + 导航"，各部门技术内容放在自己的独立站点里。**

---

## 部门负责人操作步骤

### 第一步：给部门建一个独立站点

选一个免费方案（推荐 GitHub Pages）：

| 方案 | 适合人群 | 地址 |
|------|---------|------|
| **GitHub Pages + 静态框架** | 会写 Markdown 就行 | 见下方推荐 |
| 语雀 / 飞书文档 | 不想碰代码 | 公开分享链接即可 |

静态框架推荐（任选一个，都能用 Markdown 写内容）：

- [Docusaurus](https://docusaurus.io) — Facebook 出品，功能全
- [VitePress](https://vitepress.dev) — 轻量，基于 Vite
- [Hexo](https://hexo.io) — 中文社区活跃，主题多
- [Hugo](https://gohugo.io) — 构建极快

**视觉组示例：**
```
视觉组 org（你已有的） → 新建 repo: vision-studio.github.io
→ 部署后获得 https://vision-studio.github.io
```

### 第二步：在主站添加部门入口

1. Fork `chuangyistudio/chuangyistudio.github.io`
2. 开分支：`git checkout -b dept/vision`
3. 编辑 `src/content/spec/about.md`，在"实验室简介"后面加上部门板块
4. 推到你自己的 fork，提 PR 到主仓库

**about.md 部门板块模板：**

```markdown
---

### 视觉组

专注于计算机视觉、图像处理、深度学习方向。

- 🔗 [视觉组主页](https://vision-studio.github.io)
- 📂 [GitHub](https://github.com/your-org)

**方向：** 目标检测 · 图像分割 · 多模态感知

---
```

### 第三步：导航栏添加入口（可选）

编辑 `src/config.ts` 的 `navBarConfig.links`：

```ts
{
  name: "视觉组",
  url: "https://vision-studio.github.io",
  external: true,
},
```

---

## PR 流程（每次修改都一样）

```bash
# 1. 同步上游
git checkout main
git pull origin main

# 2. 开分支
git checkout -b dept/vision-update

# 3. 改内容（about.md / config.ts）

# 4. 推到自己的 fork
git push my dept/vision-update

# 5. 在 GitHub 网页上提 PR：
#    your-fork/dept/vision-update  →  chuangyistudio/main
```

PR 标题用英文，格式：`feat: add vision group entry`

---

## 目录结构约定

主仓库内容目录：

```
src/content/
  spec/
    about.md          ← 实验室介绍（部门板块加在这里）
  posts/              ← 仅放实验室级别的通知、招新公告
    welcome.md        ← 示例文章，可删
```

各部门自己的站点内部结构完全自治，主仓库不做约束。

---

## FAQ

**Q: 视觉组成员能否直接 push 到主仓库？**
不行，成员太多容易冲突。走 Fork + PR，视觉组内部可以先在你的 fork 上 review。

**Q: 部门站点必须是 GitHub Pages 吗？**
不一定。语雀、Notion、自建服务器都行，只要有一个公开 URL 就能挂到主站。

**Q: 我想用主站直接发文章可以吗？**
实验室级别的通知、招新、获奖公告可以直接发在 `src/content/posts/` 下。部门的技术文章请放在各自站点。

**Q: 多部门同时改 about.md 冲突怎么办？**
每个部门只改自己那一段，冲突很少；真冲突了让管理员合并时手动解决。
