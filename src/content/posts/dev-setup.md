---
title: 我的开发环境：Git、Node、pnpm 与 DSH
published: 2026-10-24
description: 一台 Windows 11 机器上的完整工具链清单，连同装错、装重、版本对不上时的排查思路。
tags: [Git, Node, pnpm, 工具链, Windows]
category: 技术
draft: false
---

换机器或者重装系统时，最烦的不是装软件，是想起"上次那个坑是怎么绕过去的"。这篇当成自己的备忘录，也顺便给同样在 Windows 上折腾的人省点时间。

## 当前版本

| 工具 | 版本 | 备注 |
|---|---|---|
| Windows | 11 | 默认终端是 PowerShell 5.1，不是 7 |
| Node.js | v24.11.1 | 装完自带 npm 11.x |
| pnpm | 9.14.4 / 12.15.0 | 两套并存，见下面说明 |
| Git | 2.50.1 | 自带凭据管理器，push 会弹浏览器授权 |

## 安装与验证

```powershell
node -v
npm -v
git --version
npm i -g pnpm
pnpm -v
```

一条容易忽略的细节：仓库里的 `.npmrc` 写了 `manage-package-manager-versions = true`，`package.json` 又固定了 `packageManager: pnpm@9.14.4`。所以即使全局装的是 pnpm 12，在仓库里执行命令时 pnpm 会自动切到 9.14.4——这是特性，不是 bug。看到"Update available"提示可以直接忽略。

## Git 身份与推送凭据

```powershell
git config --global user.name "Yaosilent"
git config --global user.email "你的邮箱"
```

第一次 `git push` 会弹出浏览器让你登录 GitHub，登录后凭据由 Git Credential Manager 保存，以后不用再输密码。**不需要手工创建 PAT**——PAT 只在写脚本调 GitHub API、或配置 CI 访问别的私有仓库时才用得上。

:::warning
`git config` 里的邮箱会写进每一次 commit，公开仓库里能被人看到。介意的话用 GitHub 提供的 `no-reply` 邮箱。
:::

## 那些年踩过的环境坑

### PowerShell 5.1 和 7 不是一回事

Win11 自带的 `PowerShell` 是 5.1，很多网上抄来的命令会报"找不到参数"，比如 `Invoke-WebRequest -SkipHttpErrorCheck`（这是 7 才有的）。判断方法：

```powershell
$PSVersionTable.PSVersion
```

想用 7 就单独装 `pwsh`，然后注意两条命令跑在不同的进程里，`cd` 不会互相影响。

### npm 全局安装后命令找不到

通常是 npm 的全局 bin 目录不在 `PATH` 里。查一下位置：

```powershell
npm config get prefix
```

把输出的路径拼上 `\` 加进用户环境变量 `Path`，重开终端。

### 中文路径和空格

把项目放在 `D:\dsh-workspace\` 这种纯英文路径下。路径里有中文或空格时，某些构建工具（尤其涉及 C++ 编译的）会报莫名其妙的错，排查成本远高于改个目录名。

## DSH 是什么

DSH（DeepSeek Harness）是我现在用的 AI 工作环境：它把命令执行、文件读写、技能（skill）和长期记忆串在一起，能直接在我的工作区里干活。

对这个博客来说，它的作用很具体：装依赖、改配置、写部署脚本、生成文章骨架这些事由它做，我只负责判断和写内容。比如这篇文章的 frontmatter 就是 `pnpm new-post` 生成的。

:::note
工具的意义是缩短"想到"到"做到"的距离。凡是需要我记住十几个步骤才能完成的事，都值得想办法自动化掉。
:::
