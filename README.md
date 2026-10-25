# About Yaosilent

个人博客源码。站点地址：<https://yaosilent.github.io/>

写什么：考研数学一、408、工程与工具链笔记。技术上是 [Astro](https://astro.build) + [Fuwari](https://github.com/saicaca/fuwari) 主题，静态构建后由 GitHub Actions 发布到 GitHub Pages。

## 本地开发

要求：Node ≥ 20、pnpm ≥ 9（本仓库只能用 pnpm，`package.json` 里有 `only-allow pnpm` 检查）。

```sh
pnpm install      # 首次安装依赖
pnpm dev          # 本地预览 http://localhost:4321
pnpm build        # 构建到 dist/（含 Pagefind 搜索索引）
pnpm preview      # 预览构建产物，搜索功能只有在这里才生效
```

写新文章：

```sh
pnpm new-post 文章文件名      # 在 src/content/posts/ 下生成骨架
```

写作规范见 [docs/writing.md](docs/writing.md)。

## 部署

推送到 `main` 分支即触发 `.github/workflows/deploy.yml`：装依赖 → `pnpm build` → 发布到 GitHub Pages。

一次性设置：仓库 `Settings` → `Pages` → `Build and deployment` → Source 选 **GitHub Actions**。

路径约定：仓库名是 `yaosilent.github.io`，站点在根路径，因此 `astro.config.mjs` 中 `base: "/"`。若换成别的仓库名（如 `blog`），必须把 `site` 改成 `https://yaosilent.github.io/blog/`、`base` 改成 `/blog`，否则样式和脚本会 404。

## 目录结构

```
src/
├── config.ts            站点、导航、个人信息（改这里最多）
├── content/
│   ├── posts/           文章（Markdown）
│   └── spec/about.md    关于页正文
├── pages/               首页、归档、文章页路由
├── styles/              全局样式
└── assets/images/       头像、封面等图片
astro.config.mjs         site / base / 集成配置
tailwind.config.cjs      字体栈（已加中文字体回退）
```

## 致谢

主题基于 [Fuwari](https://github.com/saicaca/fuwari)（MIT License），做了站点配置、中文字体栈与部署链路改造。
