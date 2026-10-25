# 写作规范

这份文档只服务一件事：让"想写点东西"到"出现在网站上"之间没有决策成本。

## 一、新建文章

```sh
pnpm new-post 文件名        # 生成 src/content/posts/文件名.md
pnpm dev                    # 边写边看，改动自动刷新
```

文件名用英文小写加连字符，它会变成 URL：`pnpm new-post limit-pitfalls` → `/posts/limit-pitfalls/`，之后改文件名等于改网址，尽量一次定好。

需要给文章配封面或截图时，改成同名目录 + `index.md`：

```
src/content/posts/limit-pitfalls/
├── index.md
└── cover.jpg
```

## 二、Frontmatter 字段

```yaml
---
title: 数列极限：三个我反复踩的坑
published: 2026-10-26
description: 一句话摘要，会出现在首页卡片和 RSS 里，别超过 60 字
image: ./cover.jpg      # 可选，相对文章文件的路径
tags: [极限, 数列, 错题]
category: 数学一
draft: false            # true = 草稿，生产构建不产出
---
```

- `category` 只能一个值，从四个里挑：`数学一` / `408` / `技术` / `复盘`。不要再加新的，否则归档页会碎成一地。
- `tags` 可以多个，用来做细分：`[数据结构, 二叉树, 递归]`、`[极限, 等价无穷小]`。
- `updated` 可选，填了会在文章里显示更新时间。

## 三、公式

行内公式用单个美元符号，独立公式用两个美元符号且**单独成行**：

```markdown
当 $x \to 0$ 时，$\sin x \sim x$。

$$
\lim_{x \to 0} \frac{\tan x - \sin x}{x^3} = \frac{1}{2}
$$
```

注意两件事：

1. 正文里出现成对的 `$`（比如价格、变量名）会被当作公式，用反引号 `` `$` `` 包起来。
2. 公式里的大括号要写 `\{`；`\\` 是换行。

## 四、代码块与提示块

代码块标语言即可，会自动加行号、复制按钮、可折叠段：

````markdown
```cpp
int main() { return 0; }
```
````

提示块（Fuwari 扩展语法）：

```markdown
:::note
普通提示
:::

:::warning
容易错的地方
:::
```

还支持 `:::tip`、`:::important`、`:::caution`。

## 五、图片

- 放文章同名目录里，用相对路径 `./xxx.png`，构建时会自动压成多种尺寸。
- 截图先压到宽度 ≤ 1200px，否则仓库会越来越大（GitHub 仓库有 1GB 软限制）。
- 不要外链别人的图床，图会挂。

## 六、发布

```sh
git add -A
git commit -m "post: 数列极限三个坑"
git push
```

推送后 GitHub Actions 自动构建部署，约 1–2 分钟。看进度：仓库的 `Actions` 标签页。

## 七、内容红线（给自己定的）

- 不写真名、学号、宿舍、具体院校（写"某 985 计算机"就够）。
- 不放未公开的他人信息、聊天截图里出现的人名要打码。
- 参考了别人的文章/代码，链接放文末。

## 八、常见问题

| 现象 | 原因 |
|---|---|
| `pnpm dev` 里搜索是空的 | Pagefind 只索引构建产物，用 `pnpm build && pnpm preview` 验证 |
| 文章不显示 | `draft: true`，或文件名以 `_` 开头 |
| 样式全丢、链接 404 | `astro.config.mjs` 的 `site` / `base` 与仓库名不匹配 |
| 构建报 frontmatter 错 | `title` 或 `published` 缺失，`published` 要能被解析成日期 |
