# 主流代理客户端设置链式代理的方法：V2RayN、Clash 与 Shadowrocket

> 【更新】2026.06.17：[三网优化高性价比 VPS 推荐](https://zhetengxia.com/blog/cn2-vps-recommendations-2026)

> 如果你不想自己搭 VPS，想直接找稳定的 VPN 机场或代理平台，可以看：[稳定优质的 VPN 机场推荐](https://zhetengxia.com/blog/claude-chatgpt-vpn-airport-recommendations-2026)

> 如果你想自己购买并搭建 VPS，可以参考：[5 分钟手把手教你把 VPS 搭建成 VPN 代理](https://zhetengxia.com/blog/vps-vpn-proxy-setup-2026)

假设你有一个**代理节点**，比如[自己搭建的 VPS](https://zhetengxia.com/blog/vps-vpn-proxy-setup-2026)或购买的 VPN 代理，还有一个**出口节点**，比如购买的静态 IP。

由于直连出口节点的网络质量很差，经常丢包，所以希望通过代理节点中转一下。这种路由方式称为链式代理。

本文主要介绍代理节点加静态 IP 出口节点的连接方法。

## V2RayN

适用于 Windows、macOS。

* 第一步：配置项 → 提前复制代理链接信息 → 从剪贴板导入分享链接。
* 第二步：配置项 → 添加 SOCKS → 填写 IP、端口号、用户名和密码。
* 第三步：配置项 → 添加链式代理 → 选择自建节点 → 在下方白框中右键“添加子配置”（选择代理节点）→ 再次右键“添加子配置”（选择出口节点）。

![V2RayN 链式代理](https://zhetengxia.com/article-assets/chain-proxy-setup-guide/v2rayn-chain-proxy.png)

## Clash 内核系列

主要适用于 Windows、macOS、Android 等系统。Clash 工具主要通过 YAML 配置文件设置，链式代理的配置方式如下：

```yaml
# ... 省略 ...
proxies:
  - {name: "US-HostDare-IP", type: vless, server: xx.xx.xx.xx, port: 443, uuid: xxxxxxxxxxxxx, network: tcp, udp: true, tls: true, flow: xtls-rprx-vision, servername: addons.mozilla.org, client-fingerprint: firefox, reality-opts: {public-key: xxxxxxxxx, short-id: ""}, smux: { enabled: false, protocol: 'h2mux', padding: false, max-connections: '8', min-streams: '16', statistic: true, only-tcp: false }, brutal-opts: { enabled: false, up: '1000 Mbps', down: '1000 Mbps' } }
  - {name: "US-StaticIP-via-HostDare", type: socks5, server: xx.xx.xx.xx, port: xxx, username: xxxx, password: xxxx, dialer-proxy: "US-HostDare-IP" }
  # ... 省略 ...
proxy-groups:
  - name: 🚀 节点选择
    type: select
    proxies:
      - US-HostDare-IP
      - US-StaticIP-via-HostDare
  # ... 省略 ...
rules:
  # ... 省略 ...
```

* `US-HostDare-IP` 是代理节点信息，使用 VLESS 协议。
* `US-StaticIP-via-HostDare` 是出口节点（静态 IP）信息，使用 SOCKS5 协议。重点是 `dialer-proxy: "US-HostDare-IP"`，意思是通过 `US-HostDare-IP` 连接。

## Shadowrocket（小火箭）

主要适用于 iOS、iPadOS 等系统。

配置方式和 V2RayN 一样简单，首先添加代理节点和出口节点信息。

链式代理配置方式：编辑出口节点信息 → 代理通过 → 选择代理节点。

![Shadowrocket 链式代理设置](https://zhetengxia.com/article-assets/chain-proxy-setup-guide/shadowrocket-chain-proxy-settings.png)

![Shadowrocket 链式代理效果](https://zhetengxia.com/article-assets/chain-proxy-setup-guide/shadowrocket-chain-proxy-result.png)

## 原文链接

https://zhetengxia.com/blog/chain-proxy-setup-guide

