# 用 Hugo 建立博客


## 1 简介

这篇文章是我使用 Hugo 搭建博客的一个流程记录，这里不会涉及思路、原理之类，仅仅是方便快速回顾。


## 2 工具选择

- Hugo - 建站框架
- Github page - 部署静态网站

## 3 项目管理

### 3.1 两个 github 仓库

- `Hugo-Blog` - Hugo 博客项目，用于构建静态网站。
- `<YourGithubName>.github.io` - 部署到 Github Page 的项目。

### 3.2 `Hugo-Blog` 项目结构

```bash
Hugo-Blog
├── archetypes
│   └── default.md # 新建 Post 的 Markdown 模版
├── assets # 不定资源，例如 images/avatar.png
├── config.toml # Hugo 配置文件
├── content # 页面内容
│   ├── about.md
│   └── posts
│   		└── first-post.md
├── public # Hugo 构建的网站
│   ├── index.html
│   └── index.json # algolia 搜索需要的索引文件
├── static # 固定资源，例如 favicon.ico
│   └── favicon.ico
└── themes # 主题
```

### 3.3 `<YourGithubName>.github.io` 项目结构

```bash
public
└── index.html # 网站主入口
```

### 3.4 git submodule 管理方案

```bash
Hugo-Blog # 主项目
├── public # 子模块: <YourGithubName>.github.io | 发布的网站
└── themes
		└── Coder # 子模块: Coder | 第三方主题
```

`git submodule` 可以让主项目不管子模块，有需要时才获取子模块的更新。

`submodule` 的文件夹不需要添加到 `.gitignore`，因为它不用主项目来管。

## 4 `Hugo-Blog` 项目搭建

### 4.1 创建 Hugo 项目

```bash
# 安装 Hugo
brew install hugo
# 新建 Hugo 项目
hugo new site Hugo-Blog
# 进入项目目录
cd Hugo-Blog
```

### 4.2 安装主题

```bash
# 以 submodule 安装 theme
git init
git submodule add https://github.com/<theme>/<theme>.git themes/<theme>
```

### 4.3 配置主题

根据主题的要求去配置，不同主题有不同的配置，可以参考主题下的 exampleSite 去配置。

配置文件是 `config.toml`，基础配置如下：

```toml
baseURL = "http://example.org/"
# 网站标题
title = "My Blog"
# 主题
theme = "Coder"
```

### 4.4 新建 Post

```bash
hugo new posts/my-first-post.md
```

### 4.5 启动本地服务

```bash
# 启动本地服务
hugo serve
# -D 表示草稿 Post 也展示
hugo serve -D
```

### 4.5 构建网站

```bash
# 构建网站到 public 文件夹
hugo
# -D 表示草稿 Post 也构建
hugo -D
```

## 5 `<YourGithubName>.github.io` 项目搭建

### 5.1 新建 github repository 

新建 `<YourGithubName>.github.io` 的公开仓库，这里的`<YourGithubName>` 是指你的 github 账户名，例如 `alex.github.io`。

### 5.2 Push 到 repository 

```bash
# 从 Hugo-Blog 进入 public
cd public
# Push 到 repository 
git init
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/<YourGithubName>/<YourGithubName>.github.io.git
git push -u origin main
```

## 6 `Hugo-Blog` 项目 Push 到 Github

```bash
# 从 public 进入 Hugo-Blog
cd ..

# 删除 public
rm -rf public

# 以 submodule 添加 public
git submodule add https://github.com/<YourGithubName>/<YourGithubName>.github.io.git public

# Push 到 repository 
git add .
git commit -m "Init Hugo-Blog"
git branch -M main
git remote add origin https://github.com/<YourGithubName>/Hugo-Blog.git
git push -u origin main
```

## 7 添加 algolia 搜索

### 7.1 生成 index.json

注册 [algolia](https://www.algolia.com/) 并创建一个 `application`， `Indices` 里 创建一个新的 index，命名例如 `index_zh-cn`。

它需要的是一个 index.json 文件，文件内容大概如下：

```json
[
  {
    "categories": ["Test"],
    "content": "Markdown 语法测试。",
    "date": "2021-03-25",
    "objectID": "/markdown-test/",
    "tags": ["test"],
    "title": "Markdown 测试",
    "uri": "/markdown-test/"
  }
]
```

Hugo 可以生成这么一个 index.json 文件，在配置里添加：

```toml
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

构建网站时，生产 index.json

```bash
hugo
```

文件位置

```bash
public
├── index.json # Hugo 生成的 Algolia 需要的文件
└── index.html
```

### 7.2 搜索配置

根据主题的要求配置搜索引擎

```toml
[params.search]
  enable = true
  # 搜索引擎的类型
  type = "algolia"
  [params.search.algolia]
    index = "index_zh-cn"
    appID = "XXXXXXXX"
    searchKey = "YYYYYYYYYYYYY"
```

### 7.3 使用 Algolia Atomic 上传 index.json

安装 Algolia Atomic

```bash
npm init
npm install atomic-algolia
```

添加运行命令到 `package.json`

```json
"scripts": {
	"algolia": "atomic-algolia"
},
```

新建环境变量文件 `.env`，把 `Algolia` - `API Keys` 存放到里面。

**注意**：`Admin Key` 是很重要的，不要把它暴露出来，记得要在 `.gitignore` 里添加 `.env`。

```shell
ALGOLIA_APP_ID="XXXXXXXX"
ALGOLIA_ADMIN_KEY="ZZZZZZZZZZZZZZZZZ"
ALGOLIA_INDEX_NAME="index_zh-cn" # Algolia - Indices - index_zh-cn
ALGOLIA_INDEX_FILE="public/index.json" # hugo 生产的 index.json
```

上传文件到 algolia

```bash
npm run algolia
```

完成！


