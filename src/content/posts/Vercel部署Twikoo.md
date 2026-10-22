---
title: Vercel 部署 Twikoo
published: 2026-10-11
description: 博客想加个评论系统，挑了一圈，选了 Twikoo。
tags: [Twikoo]
category: 教程
slug: deploytwikoo
draft: false
---

# 前言

静态博客没法直接评论，得靠第三方评论系统，我推荐使用 Twikoo，开源免费，部署简单，数据存在自己手里。

统计已经跑在 Vercel 上了，Twikoo 也一并扔上去，数据库用 MongoDB Atlas，一分钱不用花。

# 创建数据库

Twikoo 用 MongoDB 存评论数据，打开 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas/register) 注册一个账号。

在首页菜单栏找到 `DATABASE` - `Clusters`，创建一个免费集群，运营商选 aws，地区选香港。

![](https://img.ax6b.cn/file/1792481939843_1.webp)

## 创建数据库用户

在首页菜单栏找到 `SECURITY` - `Security Quickstart`，创建一个数据库用户，用户名密码记好，后面要用。

![](https://img.ax6b.cn/file/1792482802917_2.webp)

接着往下看，添加一条 `0.0.0.0/0`，允许所有 IP 连接，确保 Vercel 可以正常连接。

![](https://img.ax6b.cn/file/1792483742131_3.webp)

## 获取数据库连接字符串

在首页菜单栏找到 `DATABASE` - `Clusters`，点击已创建集群旁边 `Connect`，选 `Drivers`。

![](https://img.ax6b.cn/file/1791437036503_16.webp)

记录下连接字符串，将 `<db_password>` 改成刚才创建的数据库密码，配置环境变量要用。

![](https://img.ax6b.cn/file/1792485115821_4.webp)

# 部署项目

数据库准备好了，接下来部署 Twikoo 后端。

点击 [Twikoo 官方提供的一键部署到 Vercel](https://vercel.com/import/project?template=https://github.com/twikoojs/twikoo/tree/main/templates/vercel-min)，名称随意。

![](https://img.ax6b.cn/file/1792324554976_1.webp)

## 配置环境变量

在项目菜单栏找到 `Environment Variables`，点击 `Add Environment Variable`。

Key 添加 `MONGODB_URI`，Value 填入数据库连接字符串。

![](https://img.ax6b.cn/file/1792487076803_7.webp)

## 重新部署

环境变量配置好了，需要重新跑一次才会生效。

在项目菜单栏找到 `Deployments`，点击 `Redeploy`。

![](https://img.ax6b.cn/file/1792326857218_5.webp)

等重新部署完成，访问分配的域名，前台显示云函数运行正常，说明成功了。

## 绑定自定义域名

在项目菜单栏找到 `Domains`，点击 `Add Existing`。

输入自有域名，根据提示去添加 CNAME 记录。

# 解决邮箱推送异常

项目默认开启 Deployment Protection，开启状态下外部请求会被拦截，导致功能失效。

在项目菜单栏找到 `Settings` - `Deployment Protection`，关闭 `Vercel Authentication`。

![](https://img.ax6b.cn/file/1792488816546_10.webp)

# 进入管理面板

Twikoo 没有独立的管理后台，得从博客前台进。

访问博客任一文章，点击评论区小齿轮图标，第一次进入需要设置管理员密码。

# 结尾

搞定完事，评论数据自己握着，想迁移随时能搬，后台够简单，审核、删除、导出几个按钮的事。
