---
title: Vercel + Neon 部署 Umami
published: 2026-01-05
description: 统计工具那么多，我选了 Umami，因为简单。
tags: [Umami]
category: 教程
slug: deployumami
draft: false
---

# 前言

想着装个统计系统，Google Analytics 太重，Matomo 麻烦，Plausible 要钱，都不省心。

偶然发现 Umami，开源、隐私友好、界面清爽，部署到 Vercel，数据库用 Neon，一分钱不用花。

# 创建项目

打开 [Umami](https://github.com/umami-software/umami)，Fork 到仓库。

![](https://img.ax6b.cn/file/1791425448619_201.webp)

使用 GitHub 登录 Vercel，导入 Fork 的仓库。

![](https://img.ax6b.cn/file/1791425626564_202.webp)

点击 Import single project。

![](https://img.ax6b.cn/file/1791425307277_203.webp)

点击 Create Project。

![](https://img.ax6b.cn/file/1791425868827_204.webp)

点击 Deploy。

![](https://img.ax6b.cn/file/1791426016171_205.webp)

提示部署失败，是因为还没有数据库。

# 配置数据库

返回首页，找到 Storage，创建 Neon。

![](https://img.ax6b.cn/file/1791426501878_206.webp)

地区推荐选新加坡，名字随意。

![](https://img.ax6b.cn/file/1791427380200_207.webp)

进入数据库，点击 Connect to Project，选中项目，点击 Connect Project。

![](https://img.ax6b.cn/file/1791428141784_208.webp)

# 重新部署

连接数据库后，需要重新部署才会生效。

返回首页，找到 Deployments，点击已失败的记录，点击 Redeploy。

![](https://img.ax6b.cn/file/1791428424119_209.webp)

# 登录后台

部署成功后，访问分配的域名，进入登录页面。

默认账号 `admin`，默认密码 `ummai`，为了安全，务必改密码。

# 自定义域名

进入项目，点击 Domains -> Add Existing，输入域名，按提示添加 DNS 记录。

验证完成后，Vercel 会自动生成 SSL 证书，开启 https。

![](https://img.ax6b.cn/file/1791429804954_210.webp)

# 结尾

用了段时间，界面确实清爽，数据还在自己手里，不花钱，不用维护，真省心。
