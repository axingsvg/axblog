---
title: Vercel 部署 Umami
published: 2026-10-05
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

打开 [Umami](https://github.com/umami-software/umami)，点击 `Fork`，克隆项目到自己的仓库。

![](https://img.ax6b.cn/file/1791425448619_201.webp)

# 部署项目

使用 GitHub 登录 Vercel，找到 `Add New` - `Project`，点击 `Import` 导入仓库。

![](https://img.ax6b.cn/file/1791425626564_202.webp)

目录中被检测含有多个应用程序，点击 `Import single project` 导入单个程序。

![](https://img.ax6b.cn/file/1791425307277_203.webp)

接着点击 `Create Project` 创建项目，然后点击 `Deploy` 开始部署。

![](https://img.ax6b.cn/file/1791426016171_205.webp)

部署失败是正常的，因为还没数据库，Umami 启动的时候连不上 PostgreSQL。

# 连接数据库

回到 Vercel 首页，在菜单栏找到 `Storage`，创建 `Neon`。

![](https://img.ax6b.cn/file/1791426501878_206.webp)

地区推荐选新加坡，名称随意。

![](https://img.ax6b.cn/file/1791427380200_207.webp)

创建并进入数据库，点击 `Connect to Project`，然后选中项目，再点击 `Connect Project` 连接项目。

![](https://img.ax6b.cn/file/1791428141784_208.webp)

Vercel 会自动把所需环境变量注入项目，不需要再手动配置任何东西。

# 重新部署

数据库连上了，之前部署失败了，得重新跑一次。

回到 Vercel 首页，在菜单栏找到 `Deployments`，点击已失败的部署记录，然后点击 `Redeploy`，再点击 `Redeploy`。

![](https://img.ax6b.cn/file/1791791814558_8.webp)

# 登录后台

访问 Vercel 给你分配的域名，会看到 Umami 登录页面，默认账号 `admin`，默认密码 `ummai`。

第一件事改密码，默认密码谁都知道，点击右下角 `Settings` - `Profile` 进行更改。

# 自定义域名

Vercel 分配的域名又长又丑，自己如果有域名的话可以绑上去。

进入项目，点击 `Domains` - `Add Existing`，输入域名并按提示添加 DNS 记录，验证完成后，会自动帮你配好 HTTPS。

# 结尾

Umami 跑在 Vercel，数据库在 Neon 上，两个都免费，不用管服务器，不用管证书。

用了几个月，最直观的感受是后台加载快，数据一目了然，不像 Google Analytics 那样找个页面要翻三层菜单。
