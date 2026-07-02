---
title: Markdown 语法说明
published: 2026-07-01
description: "实验室博客使用的 Markdown 语法参考，看完就能上手写文章。"
tags: [Markdown, 教程]
category: 教程
draft: false
---

## 什么是 Markdown

Markdown 是一种轻量级标记语言，用纯文本就能写出排版工整的文章。实验室博客的所有文章都用 Markdown 编写。

---

## 标题

在文字前加 `#`，数量越多层级越低：

```markdown
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
```

# 一级标题
## 二级标题
### 三级标题
#### 四级标题

---

## 文字样式

```markdown
**加粗**
*斜体*
~~删除线~~
`行内代码`
```

**加粗**  *斜体*  ~~删除线~~  `行内代码`

---

## 列表

### 无序列表

```markdown
- 项目一
- 项目二
  - 子项目
```

- 项目一
- 项目二
  - 子项目

### 有序列表

```markdown
1. 第一步
2. 第二步
3. 第三步
```

1. 第一步
2. 第二步
3. 第三步

---

## 链接与图片

```markdown
[链接文字](https://www.example.com)

![图片描述](./image.png)
```

---

## 引用

```markdown
> 这是一段引用文字
> 可以多行
```

> 这是一段引用文字
> 可以多行

---

## 代码块

用三个反引号包裹，后面跟上语言名称可启用语法高亮：

````markdown
```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```
````

效果：

```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

---

## 分隔线

```markdown
---
```

---

## 表格

```markdown
| 姓名 | 年级 | 方向 |
|------|------|------|
| 张三 | 大一 | 嵌入式 |
| 李四 | 大二 | 硬件 |
| 王五 | 大三 | 软件 |
```

| 姓名 | 年级 | 方向 |
|------|------|------|
| 张三 | 大一 | 嵌入式 |
| 李四 | 大二 | 硬件 |
| 王五 | 大三 | 软件 |

---

## 提示框

```markdown
:::note
这是普通提示
:::

:::tip
这是小技巧
:::

:::important
这是重要信息
:::

:::warning
这是警告
:::

:::caution
这是注意事项
:::
```

---

## 数学公式

行内公式用 `$` 包裹，独立公式用 `$$`：

```markdown
行内公式：$E = mc^2$

独立公式：
$$
f(x) = \int_{-\infty}^{\infty} \hat{f}(\xi) e^{2\pi i \xi x} d\xi
$$
```

行内公式：$E = mc^2$

独立公式：
$$
f(x) = \int_{-\infty}^{\infty} \hat{f}(\xi) e^{2\pi i \xi x} d\xi
$$

---

## 文字隐藏

```markdown
答案在 :spoiler[这里 **加粗也支持**] 被隐藏了
```

答案在 :spoiler[这里 **加粗也支持**] 被隐藏了

---

## GitHub 仓库卡片

```markdown
::github{repo="cjh1230/chuangyistudio.github.io"}
```

::github{repo="cjh1230/chuangyistudio.github.io"}

---

## 快速参考

| 效果 | 语法 |
|------|------|
| **加粗** | `**文字**` |
| *斜体* | `*文字*` |
| ~~删除线~~ | `~~文字~~` |
| `代码` | `` `代码` `` |
| [链接](url) | `[文字](url)` |
| 引用 | `> 文字` |
| 分隔线 | `---` |
| 标题 | `# ## ### ####` |
| 无序列表 | `- 文字` |
| 有序列表 | `1. 文字` |
