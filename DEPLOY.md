# 部署指南 — 英语学习空间

## 本地预览

### 1. 安装 Hugo

macOS:
```bash
brew install hugo
```

Windows (使用 Scoop):
```bash
scoop install hugo-extended
```

或从 https://gohugo.io/installation/ 下载对应系统的版本。

### 2. 本地运行

进入项目目录，启动开发服务器：

```bash
cd hugo-site
hugo server -D
```

打开浏览器访问 `http://localhost:1313` 即可预览网站。

### 3. 添加新内容

**方式一：命令行**

```bash
hugo new methods/my-new-method.md
hugo new reading/my-reading-note.md
hugo new listening/my-listening-material.md
```

**方式二：Decap CMS 可视化后台**（需先完成部署）

访问 `https://你的域名/admin`，通过 GitHub 登录后即可在可视化界面中添加和编辑文章。

---

## 部署到 GitHub Pages

### 1. 创建 GitHub 仓库

在 GitHub 上创建一个新仓库，例如 `english-hub`（公开仓库）。

### 2. 初始化本地 Git 仓库

```bash
cd hugo-site
git init
git add .
git commit -m "Initial commit — English learning hub"
git branch -M main
git remote add origin https://github.com/你的用户名/english-hub.git
git push -u origin main
```

### 3. 配置 GitHub Actions 自动部署

在项目根目录创建 `.github/workflows/hugo.yml`：

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 4. 启用 GitHub Pages

1. 进入仓库 → Settings → Pages
2. Source 选择 **GitHub Actions**
3. 推送代码后，Actions 会自动构建和部署

你的网站将在 `https://你的用户名.github.io/english-hub/` 上线。

### 5. 更新 baseURL

编辑 `config.toml`，将 `baseURL` 改为你的实际地址：

```toml
baseURL = "https://你的用户名.github.io/english-hub/"
```

---

## 部署到 Netlify（推荐，更简单）

1. 访问 https://app.netlify.com 并登录
2. 点击 **Add new site** → **Import an existing project**
3. 选择 GitHub，授权并选择你的 `english-hub` 仓库
4. 配置构建设置：
   - Build command: `hugo`
   - Publish directory: `public`
5. 点击 **Deploy site**

Netlify 会在每次你推送代码时自动重新部署。

---

## 配置 Decap CMS

### 1. 启用 Git Gateway

Decap CMS 需要 Git Gateway 来通过 GitHub 认证：

1. 进入 Netlify 站点 → Settings → Identity
2. 点击 **Enable Identity**
3. 在 Registration 中选择 **Invite only**（个人站点）
4. 滚动到 **Git Gateway** 并点击 **Enable Git Gateway**

### 2. 添加自己为管理员

1. 在 Identity 页面点击 **Invite users**
2. 输入你的邮箱
3. 接受邀请邮件并设置密码

### 3. 更新 config.yml

编辑 `static/admin/config.yml`，将 `repo` 替换为你的实际仓库：

```yaml
backend:
  name: git-gateway
  branch: main
  repo: 你的用户名/english-hub
```

### 4. 访问后台

访问 `https://你的域名/admin`，点击 **Login with GitHub** 或输入邮箱登录。

---

## 部署到 Vercel

1. 访问 https://vercel.com 并登录
2. 点击 **New Project** → 选择你的 GitHub 仓库
3. Framework Preset 选择 **Hugo**
4. 点击 **Deploy**

Vercel 同样会在每次推送时自动部署。

---

## 常见问题

**Q: 文章添加了但没有显示？**
A: 确保 front matter 中的 `date` 不超过当前日期，且文件在正确的文件夹中（methods/reading/listening）。

**Q: 图片/音频无法显示？**
A: 将文件放在 `static/uploads/` 目录下，在文章中使用 `/uploads/文件名` 引用。

**Q: 本地 hugo server 报错？**
A: 确保安装了 Hugo Extended 版本（支持 SCSS），可以用 `hugo version` 检查。
