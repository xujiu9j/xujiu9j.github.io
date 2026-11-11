---
title: "用 Hugo 在 GitHub Pages 搭一个博客"
date: 2026-11-11
draft: false
tags: ["Hugo", "GitHub", "教程"]
categories: ["技术"]
summary: "从零开始，用 Hugo + PaperMod + GitHub Actions 搭一个免费的个人博客。"
ShowToc: true
TocOpen: true
---

## 为什么选这套

| 方案 | 优点 | 缺点 |
|---|---|---|
| **Hugo + GitHub Pages** | 构建快、免费、纯静态 | 要懂一点命令行 |
| Hexo | 生态大、主题多 | 依赖 Node，构建慢 |
| WordPress | 后台可视化 | 要服务器、要维护 |

静态博客最大的好处：**没有数据库、没有后端、不会被拖库**。托管在 GitHub Pages 上连服务器都省了。

## 第一步：安装 Hugo

Windows 用 winget 最省事：

```powershell
winget install Hugo.Hugo.Extended
```

或者直接下载解压，把 `hugo.exe` 加进 PATH 就能用。

验证：

```bash
hugo version
# hugo v0.169.0 ... extended windows/amd64
```

> 一定要装 **extended** 版本，普通版不支持 SCSS。

## 第二步：创建站点

```bash
hugo new site myblog
cd myblog
```

## 第三步：装主题

PaperMod 是个人博客里最平衡的一个，暗色模式、搜索、归档都自带：

```bash
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

不想用 submodule 的话，直接下载 zip 解压到 `themes/PaperMod` 也一样。

## 第四步：改配置

编辑 `hugo.toml`，最少改这三行：

```toml
baseURL = "https://你的用户名.github.io/"
title   = "你的博客名"
theme   = "PaperMod"
```

## 第五步：写第一篇文章

```bash
hugo new content posts/hello-world.md
```

生成的文件长这样：

```markdown
---
title: "hello world"
date: 2026-11-11
draft: true
---
正文写在这里
```

把 `draft: true` 改成 `false`，文章才会发布。

## 第六步：本地预览

```bash
hugo server -D
```

打开 `http://localhost:1313`，改文件会实时刷新。

## 第七步：推送到 GitHub

1. 在 GitHub 建一个仓库，名字叫 `你的用户名.github.io`
2. 推送代码

```bash
git add .
git commit -m "init blog"
git branch -M main
git remote add origin https://github.com/你的用户名/你的用户名.github.io.git
git push -u origin main
```

3. 仓库 **Settings → Pages → Source** 选 **GitHub Actions**

## 第八步：自动部署

在仓库里加一个 `.github/workflows/hugo.yml`，每次推送自动构建发布：

```yaml
name: Deploy Hugo site to Pages
on:
  push:
    branches: ["main"]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - name: Build
        run: |
          wget -O hugo.deb https://github.com/gohugoio/hugo/releases/download/v0.169.0/hugo_extended_0.169.0_linux-amd64.deb
          sudo dpkg -i hugo.deb
          hugo --minify
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v4
```

推上去之后，每次 `git push` 都会自动重新构建，一两分钟后新文章就上线了。

## 常用命令

```bash
hugo new content posts/新文章.md   # 新建文章
hugo server -D                    # 本地预览（含草稿）
hugo --minify                     # 构建静态文件到 public/
```

## 踩过的坑

**1. 中文标题显示成方块**
在 `hugo.toml` 里加 `hasCJKLanguage = true`。

**2. 部署后样式全丢**
`baseURL` 写错了。必须以 `/` 结尾，且和实际访问地址一致。

**3. 搜索功能没反应**
需要在 `hugo.toml` 里加 `[outputs] home = ["HTML","RSS","JSON"]`，
并且创建 `content/search.md` 指定 `layout: "search"`。

**4. 页面 404**
检查文章 front matter 里的 `draft` 是不是还写着 `true`。

---

搭完之后就是这样，剩下的就是**持续写**。工具不是重点，内容才是。
