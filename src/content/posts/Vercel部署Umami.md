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

打开 [Umami](https://github.com/umami-software/umami)，点击 `Fork`，克隆项目到 GitHub 仓库。

# 导入项目

使用 GitHub 账号登录 Vercel，在首页菜单栏找到 `Projects`，点击 `Add New` - `Project`，从列表里找到刚克隆的仓库，点击 `Import` 导入。

![](https://img.ax6b.cn/file/1792661680067_1.webp)

目录中被检测含有多个应用程序，导入单个程序。

![](https://img.ax6b.cn/file/1791425307277_203.webp)

接着点击 `Create Project`，然后开始部署。

![](https://img.ax6b.cn/file/1791426016171_205.webp)

马上会部署失败，因为还没数据库，Umami 启动的时候连不上 PostgreSQL。

# 连接数据库

在首页菜单栏找到 `Storage`，创建 `Neon`。

![](https://img.ax6b.cn/file/1791426501878_206.webp)

地区选新加坡，名称随意。

![](https://img.ax6b.cn/file/1791427380200_207.webp)

进入数据库，点击 `Connect to Project`，选中项目，再点击 `Connect Project`。

![](https://img.ax6b.cn/file/1792663362132_8.webp)

# 重新部署

数据库连上了，得重新跑一次才会生效。

在首页菜单栏找到 `Deployments`，点击已失败的部署记录，点击 `Redeploy`。

![](https://img.ax6b.cn/file/1792662966809_7.webp)

等重新部署完成，访问分配的域名，即可进入登录后台页面，默认账号 `admin`，默认密码 `ummai`。

上去第一件事改密码，默认密码谁都知道，点击右下角 `Settings` - `Profile` 进行更改。

# 自定义域名

默认分配的域名又长又丑，自己如果有域名的话可以绑上去。

在项目菜单栏找到 `Domains`，点击 `Add Existing`，输入自由域名，按提示添加 DNS 记录。

# 结尾

用了几个月，最直观的感受是后台加载快，数据一目了然，不像 Google Analytics 那样找个页面要翻三层菜单。
