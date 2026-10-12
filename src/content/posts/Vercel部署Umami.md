---
title: Vercel 部署 Umami
published: 2026-01-05
description: 统计工具那么多，我选了 Umami，因为简单。
tags: [Umami]
category: 教程
slug: deployumami
draft: false
---

# 前言

博客上线大半年，统计工具的事一直拖着，直到偶然发现 Umami。

开源、自托管、数据在自己手里，后台界面也干净，这就是我想要的东西，直接部署到 Vercel，免费还省事。

# 克隆项目

打开 [Umami](https://github.com/umami-software/umami)，点击右上角 `Fork`，拿到一份副本。

![](https://img.ax6b.cn/file/1791425448619_201.webp)

# 导入项目

用 GitHub 账号登录 Vercel，点击 `Add New` - `Project`，找到你刚 Fork 的仓库，点击 `Import` 导入项目。

![](https://img.ax6b.cn/file/1791425626564_202.webp)

目录中检测到多个应用程序，点击 `Import single project` 导入 app 项目，接着点击 `Create Project` 创建项目。

![](https://img.ax6b.cn/file/1791425307277_203.webp)

然后点击 `Deploy` 部署项目。

![](https://img.ax6b.cn/file/1791426016171_205.webp)

这时候部署大概率会失败，这是因为还没有数据库，Umami 启动的时候连不上 PostgreSQL 自然就挂了。

# 创建数据库

回到 Vercel 首页，在菜单栏找到 `Storage` 页面，选 `Neon`。

![](https://img.ax6b.cn/file/1791426501878_206.webp)

地区推荐选新加坡，名称随意。

![](https://img.ax6b.cn/file/1791427380200_207.webp)

创建完成并进入数据库，点击 `Connect to Project`，选中项目，点击 `Connect Project` 连接项目。

![](https://img.ax6b.cn/file/1791428141784_208.webp)

这一步做完，Vercel 会自动把数据库连接所需的环境变量注入到项目，不需要再手动配置任何 DATABASE_URL 之类的东西。

# 重新部署

数据库连上了，但之前部署已经失败了，得重新跑一次。

回到 Vercel 首页，在菜单栏找到 `Deployments` 页面，点击已失败的记录，接着点击 `Redeploy`，再点击 `Redeploy`。

![](https://img.ax6b.cn/file/1791791814558_8.webp)

等个几分钟，状态变成 Ready，说明 Umami 已经跑起来了。

# 登录后台

访问 Vercel 给你分配的域名，会看到 Umami 的登录页面，默认账号 `admin`，默认密码 `ummai`。

第一件事就是改密码，这东西默认密码谁都知道，点击右下角 `Settings` - `Profile` 更改密码。

# 自定义域名

Vercel 分配的域名又长又丑，自己如果有域名的话可以绑上去。

进入项目，点击 `Domains` - `Add Existing`，输入域名，按提示添加 DNS 记录。

验证完成后，Vercel 会自动帮你配好 HTTPS。

# 结尾

Umami 跑在 Vercel，数据库在 Neon 上，两个都免费，不用管服务器，不用管证书。

用了几个月，最直观的感受是后台加载快，数据一目了然，不像 Google Analytics 那样找个页面要翻三层菜单。
