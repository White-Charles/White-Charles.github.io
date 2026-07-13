# 发布文章操作指南

> 如何在你的个人网站上发布一篇新的博文（含图片）

---

## 目录

1. [准备工作](#1-准备工作)
2. [编写文章](#2-编写文章)
3. [添加图片](#3-添加图片)
4. [本地预览](#4-本地预览)
5. [发布上线](#5-发布上线)
6. [完整示例](#6-完整示例)
7. [注意事项](#7-注意事项)

---

## 1. 准备工作

### 1.1 进入项目目录

```bash
cd /home/zwbai/文档/white-charles.github.io
```

### 1.2 创建新文章

```bash
npx hexo new post "文章标题"
```

这会自动在 `source/_posts/` 下创建一个 `.md` 文件，并填入基本 frontmatter。

> 你也可以手动创建 `.md` 文件，不一定非要用 `hexo new`。

---

## 2. 编写文章

### 2.1 文件位置

所有文章存放在：

```
source/_posts/
├── 你的文章.md
├── 你的文章/          ← 同名文件夹放图片（可选）
├── A01.md            ← LVTHW 存档
├── A02.md
└── ...
```

### 2.2 Frontmatter（文章头信息）

每篇文章开头需要有 `---` 包裹的 YAML 头信息：

```yaml
---
title: 文章标题
date: 2026-07-13 10:00:00
tags: [标签1, 标签2]
categories: [分类名]
---
```

**字段说明：**

| 字段 | 必需 | 说明 |
|------|:----:|------|
| `title` | ✅ | 文章标题 |
| `date` | ✅ | 发布日期，格式 `YYYY-MM-DD HH:mm:ss` |
| `tags` | ❌ | 标签列表，用 `[]` 或 `-` 列表 |
| `categories` | ❌ | 分类（目前你的网站无分类页面，可选填） |
| `comments` | ❌ | 是否开启评论，默认 true |

**你的文章示例：**

```yaml
---
title: 使用向日葵远程控制Ubuntu屏幕不刷新的问题解决
date: 2024-02-22 13:29:26
tags: [Linux]
---
```

### 2.3 正文写作

正文使用标准 Markdown 语法：

```markdown
## 章节标题

这是一段正文。可以包含 **粗体**、*斜体*、`代码` 等。

### 子章节

- 列表项1
- 列表项2

1. 编号1
2. 编号2

> 引用文字

`行内代码`

```python
# 代码块
print("Hello World")
```
```

> ⚠️ 注意：每段之间需空一行，否则可能合并显示。

---

## 3. 添加图片

### 3.1 推荐方式（与你的现有文章一致）

图片放在 `source/img/` 目录下，Markdown 中用**绝对路径**引用：

```
存放位置: source/img/你的图片.jpg
Markdown:  ![描述文字](/img/你的图片.jpg)
```

示例（你已有的文章就是这样做的）：

```markdown
![ ](/img/1.jpeg)
```

### 3.2 多张图片归类存放

如果图片较多，可以在 `source/img/` 下建子文件夹：

```
source/img/my-article/
├── screenshot1.png
├── screenshot2.png
└── diagram.png
```

Markdown 引用：

```markdown
![界面截图](/img/my-article/screenshot1.png)
![流程图](/img/my-article/diagram.png)
```

### 3.3 图片规范建议

| 要求 | 建议 |
|------|------|
| 格式 | `.png` 或 `.jpg` |
| 大小 | 单张不超过 500KB |
| 命名 | 英文或拼音，不要含空格 |
| 路径 | 统一用 `/img/...` 绝对路径 |

---

## 4. 本地预览

### 4.1 构建站点

```bash
cd /home/zwbai/文档/white-charles.github.io
npx hexo generate
```

### 4.2 启动本地服务器

```bash
npx hexo server
```

浏览器打开 `http://localhost:4000` 即可预览。

> 本地服务器支持热更新：修改文件后保存，刷新浏览器即可看到变化。

### 4.3 检查确认

预览时检查以下几点：

- [ ] 文章标题是否正确显示
- [ ] 标签是否正确
- [ ] 所有图片能正常加载
- [ ] 文章排版是否正常
- [ ] 目录 / 搜索是否正常工作

---

## 5. 发布上线

### 5.1 单命令部署

```bash
npx hexo generate && npx hexo deploy
```

这会执行两步：

1. `hexo generate` — 构建静态文件到 `public/` 目录
2. `hexo deploy` — 将 `public/` 内容推送到 GitHub 的 `gh-pages` 分支

### 5.2 提交源码

建议同时把源文件提交到 `main` 分支：

```bash
git add source/_posts/你的文章.md source/img/你的图片.jpg
git commit -m "Add: 文章标题"
git push origin main
```

### 5.3 等待生效

部署后约 **1-2 分钟**，访问 https://white-charles.github.io/ 即可看到更新。

---

## 6. 完整示例

### 场景：发布一篇"深度学习入门"文章，配 2 张图

**步骤 1：写文章**

`source/_posts/deep-learning-intro.md`：

```markdown
---
title: 深度学习入门笔记
date: 2026-07-13 14:30:00
tags: [Deep_Learning, Python, Tutorial]
---

## 前言

最近开始学习深度学习，记录一些基本概念。

## 神经网络结构

![神经网络示意图](/img/dl/network.png)

一个简单的全连接网络包含输入层、隐藏层和输出层。

## 训练过程

![损失函数下降曲线](/img/dl/loss_curve.png)

随着训练进行，损失函数逐渐下降并收敛。
```

**步骤 2：放图片**

```
source/img/dl/
├── network.png
└── loss_curve.png
```

**步骤 3：部署**

```bash
npx hexo generate && npx hexo deploy
git add source/_posts/deep-learning-intro.md source/img/dl/
git commit -m "Add: 深度学习入门笔记"
git push origin main
```

---

## 7. 注意事项

### ❌ 不要做的

1. **不要用 `post_asset_folder` 的相对路径引用图片**（即 `![](A02/A02-1.png)` 这种）
   - 这是 LVTHW 存档的历史写法，在你的文章里统一用 `/img/...` 绝对路径
2. **不要把图片放在 `source/_posts/` 下**
   - `_posts/` 只放 `.md` 文件
3. **不要在 Markdown 文件名中使用中文或特殊符号**
   - 用英文短横线命名，如 `deep-learning-intro.md`
4. **不要删除已有的 `.bak` 文件**
   - 那是 LVTHW 文章的备份

### ✅ 推荐的

1. 图片用 `/img/...` 绝对路径
2. 文件名用英文小写 + 短横线
3. 每篇文章单独 commit，方便追踪
4. 发布前先用 `hexo server` 本地预览
5. 文章写好后再统一部署，避免频繁构建

### 文件结构总结

```
white-charles.github.io/
├── source/
│   ├── _posts/           ← 你的文章 .md 放这里
│   │   ├── your-post.md
│   │   ├── A01.md        ← LVTHW（不动）
│   │   └── ...
│   ├── img/              ← 图片放这里
│   │   ├── 1.jpeg
│   │   ├── 2.jpeg
│   │   ├── avatar.jpg
│   │   └── your-img/     ← 你自己的图片文件夹
│   ├── about/
│   ├── collect/
│   └── tags/
├── themes/aircloud/      ← 主题（一般不动）
├── _config.yml           ← 站点配置（一般不动）
└── PUBLISH_GUIDE.md      ← 本文件
```

---

> **一句话总结**：写 `.md` 文件放 `source/_posts/`，图片放 `source/img/`，Markdown 里用 `/img/图片名` 引用，然后 `hexo generate && hexo deploy` 搞定。
