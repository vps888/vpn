---
title: "5分钟手把手教你把 VPS 搭建成 VPN 代理（2026）"
description: "从 VPS 准备、SSH 登录、系统初始化、BBR 到代理脚本安装，记录一套小白可照做的自建代理流程。"
slug: "vps-vpn-proxy-setup-2026"
pubDate: 2026-05-08
updatedDate: 2026-06-27
category: "机场代理"
tags: ["VPS", "代理配置", "Sing-box", "网络稳定性"]
featured: false
---
> 【更新】2026.06.27：有些朋友不想自己搭 VPS，我补了一篇 [VPN 机场推荐](https://zhetengxia.com/blog/claude-chatgpt-vpn-airport-recommendations-2026)，适合先短周期试用。

**本教程目的**：针对小白朋友，手把手教你如何搭建自己的 VPN 代理服务器，很简单，5 分钟基本能搞定。

**自建 VPN 的好处**：安全，独享，稳定，自由，灵活。

如果你更在意独享和可控，再继续看下面的自建方案。

## 0. 准备物料

* VPS（或服务器）一台：如果没有，参考：[三网优化 VPS 推荐](https://zhetengxia.com/blog/cn2-vps-recommendations-2026) ，至少要有公网 IP 地址、端口号（默认 22）、用户名、密码；
* 支持 SSH 的 Shell 工具，Mac/Linux 操作系统自带 Shell 工具，Windows 朋友可以下载一个 xShell，相信大家都能搞定；
* VPN 客户端：

## 1. 安装 VPN 代理

1. 从 VPS 服务商管理台，建议安装 Debain12 操作系统，当然也可以选择 Ubuntu 等 Linux 系统，本教程以 Debain 12 系统为例，适合低配置的环境。

2. SSH 远程登录操作系统

   ```shell
   # SSH 端口号通常默认 22
   ssh 用户名@IP地址
   # 或者有其他端口号
   ssh 用户名@IP地址 -p 端口号
   # 输入密码
   ```

3. 更新系统配置

   ```shell
   apt update && apt upgrade -y
   apt install curl wget vim socat ntpdate -y
   # 强制同步系统时间
   ntpdate -u pool.ntp.org
   ```

4. 开启原生 BBR 加速

   ```shell
   # 写入配置
   cat > /etc/sysctl.conf << EOF
   net.core.default_qdisc=fq
   net.ipv4.tcp_congestion_control=bbr
   EOF
   
   # 使其生效
   sysctl -p
   
   # 检查是否成功 (输出应包含 bbr)
   lsmod | grep bbr
   
   # 效果
   net.core.default_qdisc = fq
   net.ipv4.tcp_congestion_control = bbr
   tcp_bbr                20480  1
   ```

5. 安装代理工具

   ```shell
   # 执行此命令进入交互式菜单
   bash <(wget -qO- https://raw.githubusercontent.com/fscarmen/sing-box/main/sing-box.sh)
   ```

6. 等一小会，自动安装完成，会列出各种工具的订阅链接，如果没记住没关系，这个脚本会创建 sb 命令，通过执行 sb 命令，输入 1，还可以查看订阅链接。详细步骤如下：
![step1](https://zhetengxia.com/article-assets/vps-vpn-proxy-setup-2026/step1.png)

![step2](https://zhetengxia.com/article-assets/vps-vpn-proxy-setup-2026/step2.jpg)

![step3](https://zhetengxia.com/article-assets/vps-vpn-proxy-setup-2026/step3.jpg)

![step4](https://zhetengxia.com/article-assets/vps-vpn-proxy-setup-2026/step4.png)

## 2. 客户端使用

上述会生成三种链接，初学者只关注 vless 协议的即可。红色字体限制的就是客户端类型，我们只介绍两种主流的客户端工具：

![两类主流 VPN 客户端](https://zhetengxia.com/article-assets/vps-vpn-proxy-setup-2026/client.png)

1. 安装客户端

下载安装上述三类客户端中的一个即可，下面每个下载页面都会详细你告诉你，根据你的操作系统应该选择哪个安装包。

**V2rayN 系列：**

（1）V2rayN官方下载：https://github.com/2dust/v2rayN/releases ，适用于 **Windows**、Linux、MacOS，**推荐 Windows 安装这个；**

（2）V2rayNG官方下载：https://github.com/2dust/v2rayNG/releases ，适用于**安卓、纯血鸿蒙**

**Clash Verge系列：**

（1）Clash Verge rev官方下载：https://github.com/clash-verge-rev/clash-verge-rev/releases ，适用于**Windwos、Linux**、MacOS，**推荐 Linux、Windows 安装这个**

（2）ClashMac：https://clashmac.app/ ，适用于 MacOS，**强烈推荐 MacOS 安装这个**

（3）ShadowRocket：AppleStore，需要非大陆 Apple ID 才可以下载，收费软件（可以借周边朋友已经付费过的账号安装），适用于 **iOS、iPadOS**

2. 导入链接

打开客户端，通常会有很明显的标记如何新增一个订阅链接。也就是你只需要复制V2rayN 或者 Clash Verge 的 vless 链接，上述客户端通常支持自动从剪贴板导入。

## 3. 链式代理

如果你还买了静态住宅 IP（ISP），可以参考本站这篇 [主流代理客户端设置链式代理的方法](https://zhetengxia.com/blog/chain-proxy-setup-guide)。因为通常大陆到静态住宅 IP 的网络质量不好，所以需要通过 VPN 代理服务器中转一下网络请求。

如果还没有静态住宅 IP，可以参考 [如何在IPRoyal上选择一个干净的静态住宅IP（ISP）](https://zhetengxia.com/blog/iproyal-static-isp-clean-ip-guide)

## GitHub 原文参考

- [三网优化 VPS 推荐（GitHub）](https://github.com/vps888/vps2605)
- [5 分钟手把手教你把 VPS 搭建成 VPN代理教程（GitHub）](https://github.com/vps888/vpn/blob/main/5%20%E5%88%86%E9%92%9F%E6%89%8B%E6%8A%8A%E6%89%8B%E6%95%99%E4%BD%A0%E6%8A%8A%20VPS%20%E6%90%AD%E5%BB%BA%E6%88%90%20VPN%E4%BB%A3%E7%90%86%EF%BC%882026%EF%BC%89.md)

## 原文链接

https://zhetengxia.com/blog/vps-vpn-proxy-setup-2026

