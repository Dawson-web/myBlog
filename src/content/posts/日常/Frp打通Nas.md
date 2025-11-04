---
title: Frp打通Nas
tags: [日常]
category: 日常
published: 2025-11-04
draft: false 
image: /日常/Frp打通Nas/cover.png
---

## 前言

在去年的时候热衷于折腾 Nas，当时安装了 FNOS 系统，虽然提供了免费的 FN Connect 远程访问服务但也仅限于单一的 FNOS 系统端口访问，
最初的想法是让 Nas 当作服务器，跑跑自己的项目，后面有开始研究别的应用安装在 Docker 上，但受限于 Nas 是放在家里面，我没法直接通过内网访问这些服务于是乎需要内网穿透从而实现访问。

## 实现

### 前置配置

我先给Nas上安装了 1Panel 面板方便后续操作，通过 Sakura Frp(收费的内网穿透，但免费提供两个隧道)先将 1Panle 的端口穿透出来，此外因为要实现 Frp 肯定要有穿透的服务端，
所以买了一个2核2G的轻量云服务。

此时我就拥有了 Nas(Frp客户端) 和 云服务器(Frp服务端)，并且在 Nas 中安装 frpc 在云服务器中安装 frps (建议在Docker中安装，面板的应用商店可能搜索不到 frpc 和 frps)

### Frps配置

因为 Frps 是部署在云服务器，所以我们可以直接访问到网站，默认使用端口是7500，可以在 Docker 对应的应用参数里查看登陆所需的账号和密码。

此时我们找到 Frps 对应的文件目录，打开 frps.toml 文件，会看见如图所示配置。

![Frp打通Nas](/日常/Frp打通Nas/1.png)

我们需要关注三项配置token、user、password，token是我们建立 Frps 与 Frpc 连接的密钥，user 和 password 是我们登陆 Frps 管理页面的账号和密码，此处请修改成个人的相关值到此我们的 Frps 配置已经完成了

### Frpc配置

我们前往 1Panel 平台，在应用商店中安装 Frpc，默认使用端口是7400，可以在 Docker 对应的应用参数里查看登陆所需的账号和密码。

此时我们找到 Frpc 对应的文件目录，打开 frpc.toml 文件，会看见如图所示配置：

![Frp打通Nas](/日常/Frp打通Nas/2.png)

我们需要关注如下几个参数：

serverAddr: 云服务器公务IP

auth.token: 对应 Frps 中填写的token

webServer.user: Frpc 登陆帐号

webServer.password: Frpc 登陆密码

上述配置确保能正常登陆并实现 Frpc 与 Frps 联系起来，但我们仍需配置下述参数实现隧道的建立：

```js
# ========== 映射 frpc 管理界面 ==========
[[proxies]]
name = "XXXXXXXX"
type = "tcp"
localIP = "XXXXXXXX"    # NAS IP（需要打通的内网的IP）
localPort = XXXXXXXX    # frpc 管理端口（要打通的内网对应的使用的端口）
remotePort = XXXXXXXX   # 云服务器端口（对应能访问的云服务公网IP的端口）
```

### 防火墙配置

我们在上述使用时使用了很多端口，如 7400，7500 等，又或者自行配置的remotePort，所以我们需要在云服务器厂商处放通对应的端口，如果你使用了如宝塔等面板请也放开对应端口，放开后就能正常访问了（若仍无法访问请检查配置是否正确，或尝试稍等一会重启对应应用服务）

![Frp打通Nas](/日常/Frp打通Nas/3.png)

![Frp打通Nas](/日常/Frp打通Nas/4.png)
