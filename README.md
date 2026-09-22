# 求职，毕业与享受生活

一套基于 Astro 的个人博客模板：免费部署、Markdown 写作、支持归档、标签、搜索、RSS 和深色模式。

设计思路参考了 [如何 10 分钟快速搭建你自己的独立博客](https://guangzhengli.com/blog/zh/how-to-create-your-blog-for-free-in-10min)，但将教程中已经停止维护的示例仓库替换为更现代的 Astro 静态站点方案，部署方式仍然是 GitHub Pages 免费托管。

## 本地预览

先安装依赖：

```bash
pnpm install
```

启动本地开发服务器：

```bash
pnpm dev
```

浏览器打开终端显示的地址即可。构建生产版本：

```bash
pnpm build
```

## 像 Word / 飞书一样管理

项目已经配置好 Pages CMS。它提供一个在线可视化后台，可以通过表单修改网站设置，并用所见即所得编辑器撰写文章。内容保存后会自动提交到 GitHub，然后触发网站重新部署。

使用前需要先把项目推送到 GitHub，然后：

1. 打开 [Pages CMS](https://app.pagescms.org/)。
2. 使用 GitHub 登录并授权访问你的博客仓库。
3. 选择博客仓库。
4. 在左侧使用「网站设置」修改名称、作者、导航和头像。
5. 使用「博客文章」新建或编辑文章，正文可以直接像 Word 一样排版。
6. 点击保存，等待 GitHub Actions 自动发布。

后台配置保存在根目录的 `.pages.yml`，网站基础信息保存在 `src/data/site.json`，上传的图片会保存到 `public/images`。

## 内容排版保护

Pages CMS 的富文本表格编辑器在部分浏览器中按回车时，可能会写入不可见控制字符 `U+001F`。这个字符在网页上通常会显示为方框，但它实际携带的是换行语义。

项目包含 `scripts/sanitize-content.mjs`，处理规则如下：

- 将 `U+001F` 转换为 `<br>`，保留编辑器中输入的换行；
- 删除零宽空格 `U+200B`；
- 删除 Unicode 替换字符 `U+FFFC`、`U+FFFD`；
- 删除 BOM 和单词连接符 `U+FEFF`、`U+2060`。

`pnpm dev` 和 `pnpm build` 都会在运行前自动执行处理。也可以手动执行：

```bash
pnpm sanitize
```

该脚本会检查 `src/content`、`src/data` 和 `.pages.yml`，既避免隐藏字符进入最终网站，也不会牺牲编辑器中的换行效果。

### 新文章文件名

Pages CMS 会自动为新文章生成类似下面的文件名：

```text
2026-09-22-173045.md
```

这样即使文章标题是中文，也不会因为英文 slug 为空而导致扩展名错误。创建文章时可以修改这个文件名，建议改成简短英文地址，例如 `my-first-note.md`。
## 第一步：改成你自己的信息

除了使用 Pages CMS，也可以手动修改 `src/data/site.json`：

```json
{
  "title": "你的博客名称",
  "shortTitle": "简称",
  "author": "你的名字",
  "description": "一句话介绍你的博客",
  "tagline": "首页展示的短句",
  "email": "你的邮箱",
  "avatar": "/avatar.svg",
  "social": [
    { "label": "GitHub", "href": "https://github.com/你的用户名" },
    { "label": "邮件", "href": "mailto:你的邮箱" }
  ]
}
```

然后：

1. 将 `public/avatar.svg` 替换成你的头像，或者修改配置指向新的图片。
2. 修改 `public/favicon.svg` 更换网站图标。
3. 修改 `public/robots.txt` 中的站点域名。
4. 在 `astro.config.mjs` 中把 `yourname` 改成你的 GitHub 用户名。

## 第二步：写第一篇文章

在 `src/content/blog` 新建 `.md` 文件，例如 `my-first-post.md`：

```md
---
title: "文章标题"
description: "文章摘要"
pubDate: 2026-09-22
category: "随笔"
tags: ["生活", "思考"]
featured: false
draft: false
---

从这里开始写正文。支持标题、列表、引用、链接、图片和代码块等 Markdown 语法。
```

字段说明：

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `title` | 是 | 文章标题 |
| `description` | 是 | 列表和搜索结果中的摘要 |
| `pubDate` | 是 | 发布日期，格式为 `YYYY-MM-DD` |
| `category` | 否 | 文章分类 |
| `tags` | 否 | 标签数组 |
| `featured` | 否 | 是否作为首页推荐文章 |
| `draft` | 否 | 设为 `true` 后不会出现在生产构建中 |
| `updatedDate` | 否 | 最后修改日期 |
| `readingTime` | 否 | 手动指定阅读分钟数 |

示例文章位于 `src/content/blog`，可以直接删除或替换。

## 第三步：部署到 GitHub Pages

1. 登录 GitHub，创建一个名为 `dada303312.github.io` 的公开仓库。
2. 在项目根目录执行：

```bash
git add .
git commit -m "feat: create personal blog"
git branch -M main
git remote add origin https://github.com/你的用户名/dada303312.github.io.git
git push -u origin main
```

3. 打开仓库的 `Settings` → `Pages`。
4. 在 `Build and deployment` 中，将 `Source` 设为 **GitHub Actions**。
5. 等待 `Actions` 中的工作流完成，然后访问 `https://dada303312.github.io`。

以后只要推送新内容，网站就会自动更新：

```bash
git add .
git commit -m "post: add new article"
git push
```

## 自定义域名

在仓库的 `Settings` → `Pages` 中绑定域名，然后在 `public` 目录添加名为 `CNAME` 的文件，文件内容就是你的域名，例如：

```text
blog.example.com
```

同时把 `astro.config.mjs` 和 `public/robots.txt` 中的域名改成自定义域名。

## 目录结构

```text
.
├── .github/workflows/deploy.yml  # GitHub Pages 自动部署
├── .pages.yml                    # Pages CMS 可视化后台配置
├── public/                       # 头像、图标、robots.txt
├── src/
│   ├── components/              # 页头、页脚、文章卡片
│   ├── content/blog/            # Markdown 文章
│   ├── data/site.json           # 网站名称、作者和导航信息
│   ├── layouts/                 # 全站页面框架
│   ├── lib/posts.ts             # 文章处理函数
│   ├── pages/                   # 首页、归档、标签、搜索、文章页
│   ├── styles/global.css        # 全站样式
│   └── config.ts                # 个人信息和导航配置
├── astro.config.mjs             # 网站域名与构建配置
└── package.json
```

## 已包含的功能

- 响应式布局，手机与桌面端均可阅读
- 深色 / 浅色模式，并记住访客选择
- 按年份归档和主题标签
- 浏览器端全文搜索
- RSS 与站点地图
- 自动计算阅读时间
- Open Graph / SEO 基础信息
- GitHub Pages 自动构建与部署
- Pages CMS 可视化文章和网站设置管理







