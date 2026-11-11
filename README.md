# 许玖的博客

基于 **Hugo + PaperMod** 的个人博客，部署在 GitHub Pages。

---

## 快速开始

### 1. 改配置

打开 `hugo.toml`，把这两处换成你自己的：

```toml
baseURL = "https://你的用户名.github.io/"
```

```toml
[[params.socialIcons]]
  name = "github"
  url = "https://github.com/你的用户名"
```

### 2. 本地预览

```bash
hugo server -D
```

打开 http://localhost:1313

### 3. 写文章

```bash
hugo new content posts/文章标题.md
```

编辑生成的文件，把 front matter 里的 `draft: true` 改成 `false`。

### 4. 发布

```bash
git add .
git commit -m "新文章：xxx"
git push
```

推送到 GitHub 后会自动构建部署（约 1-2 分钟）。

---

## 目录结构

```
.
├── .github/workflows/hugo.yml   # 自动部署配置
├── archetypes/                  # 新建文章的模板
├── content/
│   ├── posts/                   # 文章
│   ├── about.md                 # 关于页
│   ├── archives.md              # 归档页
│   └── search.md                # 搜索页
├── themes/PaperMod/             # 主题
├── hugo.toml                    # 站点配置
└── README.md
```

---

## 首次部署到 GitHub

### 1. 建仓库

在 GitHub 新建仓库，命名为：

```
你的用户名.github.io
```

必须用这个命名，否则 Pages 地址会是 `用户名.github.io/仓库名/`。

### 2. 推送

```bash
git init
git add .
git commit -m "init blog"
git branch -M main
git remote add origin https://github.com/你的用户名/你的用户名.github.io.git
git push -u origin main
```

### 3. 开启 Pages

仓库页面 → **Settings** → **Pages**

- **Source** 选 `GitHub Actions`

保存后等待第一次构建完成，访问 `https://你的用户名.github.io`。

---

## 常用命令

| 命令 | 作用 |
|---|---|
| `hugo server -D` | 本地预览（含草稿） |
| `hugo new content posts/名字.md` | 新建文章 |
| `hugo --minify` | 构建到 `public/` |
| `hugo --gc` | 构建并清理缓存 |

---

## 换主题

```bash
# 1. 下载新主题到 themes/ 目录
# 2. 修改 hugo.toml 里的 theme = "主题名"
```

主题库：https://themes.gohugo.io/

---

## 常见问题

**中文标题显示异常**
确认 `hugo.toml` 里有 `hasCJKLanguage = true`。

**部署后没样式**
`baseURL` 必须以 `/` 结尾，且和实际访问地址完全一致。

**搜索没反应**
确认 `hugo.toml` 里有：

```toml
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

并且 `content/search.md` 里指定了 `layout: "search"`。

---

## 许可

文章内容采用 CC BY-NC-SA 4.0。

---

## 环境要求

| 组件 | 版本 | 说明 |
|---|---|---|
| Hugo | **0.147.9**（extended） | 更高版本会因主题兼容性报错 |
| Git | 任意较新版本 | 推送用 |

安装 Hugo：

```powershell
winget install Hugo.Hugo.Extended --version 0.147.9
```

> **注意**：主题 `themes/PaperMod` 已做过两处兼容性修补
> （移除 `partial "partials/..."` 多余前缀、删除访问 `site.Social` 的旧兼容分支）。
> 升级主题时会被覆盖，需要重新打补丁。