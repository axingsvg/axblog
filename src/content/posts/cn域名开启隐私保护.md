---
title: CloudFlare 部署 imgBed
published: 2026-03-25
description: 图片没地方存，图床又贵又不稳，后来发现 Telegram 能白嫖存储，配上 CloudFlare 刚好凑一个图床。
tags: [imgbed]
category: 教程
slug: deployimgbed
draft: false
---

# 前言

博客写多了，图片存哪是个问题，放 git 仓库会膨胀，第三方图床又怕数据全丢，自己搞服务器成本又划不来。

最后选了 Telegram 存图，CloudFlare 做托管，一分钱不用花。

# 创建 Ttelegram Bot

打开 Telegram，搜索 [@BotFather](https://t.me/BotFather)，给它发送 `/newbot`，按提示为机器人起名称和用户名。

用户名必须以 `_bot` 或 `bot` 为结尾，然后保存好 Token。

![](https://img.ax6b.cn/file/1792670139811_1.webp)

# 获取频道 ID

Telegram 新建一个私密频道，添加机器人并设置为管理员，接着搜索 [@VersaToolsBot](https://t.me/VersaToolsBot)，给它发送 `/start`。

回到刚才创建的频道，随便发条信息转发给 `@VersaToolsBot`，将频道 ID 保存好。

![](https://img.ax6b.cn/file/1792671104659_2.webp)

# 克隆项目

打开 [CloudFlare-ImgBed](https://github.com/MarSeventh/CloudFlare-ImgBed)，点击 `Fork`，克隆项目到 GitHub 仓库。

# 导入项目

使用 GitHub 账号登录 [CloudFlare](https://www.cloudflare.com/)，在首页菜单栏找到 `Compute` - `Workers & Pages`，点击 `Create application`。

![](https://img.ax6b.cn/file/1792672196661_3.webp)

![](https://img.ax6b.cn/file/1792756244562_4.webp)

![](https://img.ax6b.cn/file/1792756362599_5.webp)

![](https://img.ax6b.cn/file/1792756548194_6.webp)

构建命令输入：`npm install`，构建输出目录输入：`frontend-dist`。

![](https://img.ax6b.cn/file/1792756864581_7.webp)

# 创建 KV 空间

部署完成后，在首页菜单栏找到 `Storage & databases` - `Workers KV`。

![](https://img.ax6b.cn/file/1792757307050_8.webp)

KV 空间名称输入：`img_url`。

![](https://img.ax6b.cn/file/1792757701581_9.webp)

在首页菜单栏找到 `Compute` - `Workers & Pages`，点击已创建项目，找到 `Settings` - `Bindings`。

![](https://img.ax6b.cn/file/1792758251329_10.webp)

![](https://img.ax6b.cn/file/1792758413176_11.webp)

变量名称必须是：`img_url`，然后选中刚才创建的 KV 空间。

![](https://img.ax6b.cn/file/1792758615532_12.webp)

# 重新部署

绑定 KV 空间后，需要重新部署才会生效。

在首页菜单栏找到 `Compute` - `Workers & Pages`，点击已创建项目，找到 `Deployments`。

![](https://img.ax6b.cn/file/1792759015196_13.webp)

等部署完成，访问默认分配域名，即可进入网站前台。

# 存储通道配置

去网站后台配置存储通道，才能上传文件。

![](https://img.ax6b.cn/file/1792759356286_14.webp)

找到上传设置 - 添加渠道，类型选择：`Telegram`，名称随意，输入 `Bot Token` 和 `频道 ID`。

![](https://img.ax6b.cn/file/1792760112611_15.webp)

配置存储通道后，即可上传文件。

# 自定义域名

为了方便访问，可以在项目 -> 自定义域，配置你的域名。

# 补充

# Workers 部署

Cloudflare Workers 部署是 Pages 之外的另一种无服务器部署方式，与 Pages 相比，Workers 提供更灵活的路由控制和更高的可定制性，适合需要对部署过程进行精细控制的用户。

# 克隆项目

打开 [CloudFlare-ImgBed](https://github.com/MarSeventh/CloudFlare-ImgBed)，点击 `Fork`，克隆项目到 GitHub 仓库。

# 获取账户 ID 和 API 令牌

打开 [Cloudflare](https://dash.cloudflare.com)，在首页菜单栏 `Compute` - `Workers & Pages`，找到账户 ID 并保存好。

![](https://img.ax6b.cn/file/1792792688345_16.webp)

接着点击顶部头像 - `Profile` - `API Tokens`，点击 `Create Token` 创建令牌。

![](https://img.ax6b.cn/file/1792793537418_17.webp)

选择 `Edit Cloudflare Workers` 模板。

![](https://img.ax6b.cn/file/1792793932733_18.webp)

账号资源添加账号，区域资源选择所有区域，然后点击 `Continue to summary` 继续下一步。

![](https://img.ax6b.cn/file/1792794641250_19.webp)

点击 `Create Token` 创建令牌。

![](https://img.ax6b.cn/file/1792794893553_20.webp)

这个 Token 只会显示这一次，务必保存好。

![](https://img.ax6b.cn/file/1792795243994_21.webp)

# 创建 KV 数据库

在首页菜单栏 `Storage & databases` - `Workers KV`，点击 `Create a KV namespace`。

命名必须是 `img_url`，点击 `Create` 创建，把 ID 保存好。

![](https://img.ax6b.cn/file/1792796114983_22.webp)

# 配置密钥

在你 Fork 的仓库顶部菜单栏，找到 `Settings` - `Secrets and variables` - `Actions`。

点击 `New repository secret`，名称输入 `CLOUDFLARE_ACCOUNT_ID`，内容输入账户 ID，再点击 `Add secret` 保存。

点击 `New repository secret`，名称输入 `CLOUDFLARE_API_TOKEN`，内容输入 API 令牌，再点击 `Add secret` 保存。

点击 `New repository secret`，名称输入 `KV_NAMESPACE_ID`，内容输入 KV 数据库 ID，再点击 `Add secret` 保存。

![](https://img.ax6b.cn/file/1792797375483_23.webp)

# 运行部署

在你 Fork 的仓库顶部菜单栏，找到 `Actions`，点击 `I understand my workflows, go ahead and enable them`。

点击左侧菜单栏 `Deploy to Cloudflare Workers`，再点击 `Run workflow`，分支默认 main，名称输入优先级：手动输入 - WORKER_NAME密钥 - cloudflare-imgbed。

部署完成后，在 Cloudflare 首页菜单栏 `Compute` - `Workers & Pages`，即可看到已部署的项目，访问默认分配的域名即可进入网站前台。

在网站后台配置存储通道就可以用了，如果不清楚怎么配置往上翻。

workers 部署需要通过路由配置自定义域名，如果不清楚百度一下。
