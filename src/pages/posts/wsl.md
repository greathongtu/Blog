---
layout: '../../layouts/MarkdownPost.astro'
title: 'wsl 网络问题踩坑'
pubDate: 2023-03-19
description: 'wsl 使用vs code 的时候插件一直遇到网络问题，排查了一整天最终解决。'
author: 'Aaron'
cover:
    url: '/preview/wsl.jpg'
    alt: 'cover'
tags: ["wsl", "network"]
theme: 'light'
featured: false
---

# 问题
win11 使用 vs code 连接 wsl 的时候，一些 vs code 的拓展无法连接网络，报错：
```
Failed to establish a socket connection to proxies: ["PROXY 127.0.0.1:7890"]
```

# 可能原因
开启 Clash for Windows 之后，Windows 系统代理一般被设置为 127.0.0.1:7890，VS Code 会继承这个代理设置，Ubuntu 子系统中的 vscode-server 也继承了这个代理设置。但是 Ubuntu 子系统在 127.0.0.1:7890 上并没有代理服务，导致 vscode-server 联网失败。所以 Ubuntu 子系统中正确设置 "http_proxy" 和 "https_proxy" 环境变量应该可以解决问题。

# 解决办法

1. 在 Ubuntu 子系统的 ~/.bashrc 文件末尾加上下面代码：
```bash
# 获取 Host IP
WINDOWS_IP=$(ip route | grep default | awk '{print $3}')
PROXY_HTTP="http://${WINDOWS_IP}:7890"

# 设置环境变量
export http_proxy="${PROXY_HTTP}"
export https_proxy="${PROXY_HTTP}"
```

或者使用 fish 的话在 ~/.config/fish/config.fish 文件末尾加上下面代码：
```bash
set -l WINDOWS_IP (ip route | grep default | awk '{print $3}')
set -l PROXY_HTTP "http://$WINDOWS_IP:7890"
set -gx http_proxy $PROXY_HTTP
set -gx https_proxy $PROXY_HTTP
```
wsl 的话似乎是只需要在shell 的配置文件里设置环境变量就是永久的了

2. Clash for Windows 保持打开状态，并打开 allow lan （表示允许局域网（Local Area Network, LAN）访问。当启用 "allow lan" 选项时，局域网内的其他设备可以连接到运行 Clash 的设备，并通过其代理服务访问互联网。这样，你可以将运行 Clash 的设备作为一个代理服务器，供局域网内的其他设备使用。）

3. 为防止 Windows 防火墙阻止 Ubuntu 子系统访问 Host IP，使用管理员权限打开powershell，在 Windows 防火墙中增加一条规则：
```shell
New-NetFirewallRule -DisplayName "WSL" -Direction Inbound -InterfaceAlias "vEthernet (WSL)" -Action Allow
```

可能需要重启一下，至此问题解决。
