---
title: OpenWrt 安装 Shadowsocks Server
categories: [Tutorial, Shadowsocks]
tags: [Shadowsocks]
layout: post
seo:
  date_modified: 2020-03-02 22:56:24 +0800
---

在 OpenWrt 路由器上安装 server 版 Shadowsocks，使得客户端设备能够以 VPN 的形式远程连接到家庭局域网。

## 在 OpenWrt 上安装 Shadowsocks Server

### Step 1 - 安装 shadowsocks-libev-server 软件包

前往作者[项目主页](https://github.com/shadowsocks/openwrt-shadowsocks/releases)获取最新版本的 shadowsocks-libev-server 并安装，安装之前用如下命令查询自己路由器 CPU 所属的平台架构

```
opkg print-architecture
```

也可以使用软件源的方法安装，参考 [OpenWrt Shadowsocks 安装&配置指南](https://linhongbo.com/posts/shadowsocks-on-openwrt/#通过软件源安装) 添加软件源，然后执行以下命令安装

```
opkg install shadowsocks-libev-server
```

### Step 2 - 创建配置文件

```sh
vim /etc/shadowsocks-server.json

{
   "server":["[::0]","0.0.0.0"],
    "server_port":8888,
    "password":"xxxxxxx",
    "timeout":60,
    "method":"aes-128-gcm",
    "fast_open":true
}
```

### Step 3 - 创建开机启动脚本

```sh
vim /etc/rc.local

## 在文件最后， exit 0 之前（如果有的话）加此行启动命令
ss-server -u -c /etc/shadowsocks-server.json &
```

### Step 4 - 添加防火墙规则

点击 `Firewall - Traffic Rules` 添加一条防火墙规则，使得 WAN 侧（即 Internet）能够访问到 Shadowsocks 监听的端口

![openwrt shadowsocks server firewall](/assets/img/post/openwrt-shadowsocks-server-firewall.png)

到此所有安装配置工作完成。

## 远程连接到 Shadowsocks Server

以 Android 客户端为例：路由规则选择`全局`，让手机全局连接到路由器以访问内网服务，此外为了让手机能正常解析域名和上网，还需要将 `远程 DNS` 选项修改为 127.0.0.1（OpenWrt 的 dnsmasq DNS 服务）并勾选 `使用 UDP DNS`。

![Shadowsocks Clent Config](/assets/img/post/client-configuration-for-openwrt-shadowsocks-server.jpg)
