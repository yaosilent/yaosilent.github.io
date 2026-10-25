---
title: 这个博客是怎么来的：Astro + Fuwari + GitHub Pages
published: 2026-10-25
description: 从模板到上线：为什么选 Fuwari、三个真实踩到的坑，以及一条 push 即部署的流水线。
tags: [Astro, Fuwari, GitHub Pages, 部署]
category: 技术
draft: false
---

想写博客这件事拖了很久，卡住的从来不是内容，是"要不要自己写一个"。最后的结论是：**先让别人把壳做好，我只负责写字**。

## 为什么是 Fuwari

选型的标准只有三条：能渲染数学公式、中文阅读体验说得过去、改配置不用碰源码。按这个标准筛下来，Fuwari（Astro 5 + Tailwind）几乎全中：

- KaTeX 内置，`$...$` 和 `$$...$$` 开箱可用
- 深色模式、右侧目录、RSS、站内搜索（Pagefind）都是现成的
- 配一个 `src/config.ts` 就能改完站点身份

代价也要说清楚：仓库里写了 `"preinstall": "npx only-allow pnpm"`，**用 npm 装依赖会直接报错**；主题上游更新不勤，所以策略是"不追上游，只改自己的"。

## 部署链路

整个流程就三件事：写 Markdown → `git push` → GitHub Actions 构建并发布。

```yaml
# .github/workflows/deploy.yml 的骨架
jobs:
  build:
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - run: pnpm install --frozen-lockfile
      - run: pnpm build          # astro build + pagefind 索引
      - uses: actions/upload-pages-artifact@v3
  deploy:
    needs: build
    steps:
      - uses: actions/deploy-pages@v4
```

一次性设置只有一处：仓库 `Settings → Pages → Source` 选 **GitHub Actions**。

## 踩到的三个坑

### 1. 仓库名决定 base 路径

站点是 `https://yaosilent.github.io/`，仓库名就得是 `yaosilent.github.io`，此时 `base: "/"`。

如果仓库叫 `blog`，网址会变成 `/blog/`，必须同步改 `astro.config.mjs` 里的 `site` 和 `base`，否则 CSS 和 JS 全部 404——构建不会报错，只是页面裸奔。

### 2. Pages 的 Source 默认不是 Actions

建完仓库直接 push，Actions 会绿，部署那一步会红，报 404。原因就是 `Source` 还停在 `Deploy from a branch`。这一步必须在网页上点，没有 CLI 捷径。

### 3. Vercel 在国内用不了

实测：`github.com` 666ms、`*.github.io` 1.2s，而 `*.vercel.app` 直接超时。Fuwari 的官方 demo 就挂在 Vercel 上，所以不要把演示链接当成"国内能打开"的证明。

## 接下来

- 评论系统（Giscus）先不做，没人看的博客不需要评论区
- 等国内打开速度真成问题时，再上自定义域名
- 内容节奏：学什么写什么，不为了更新而更新
