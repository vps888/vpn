# 主流代理客户端设置链式代理的方法
[![Status](https://img.shields.io/badge/Status-Active-brightgreen)]()
[![Updated](https://img.shields.io/badge/Updated-May_2026-blue)]()

>【更新】2026.06.17 [【新增今年最具性价比的CN2 VPS，2C4G40G 2T，年388RMB】](https://github.com/vps888/vps2605)
> 
> 如果你想自己购买搭建 VPS，[参考： 5 分钟手把手教你把 VPS 搭建成 VPN代理](https://github.com/vps888/vpn/blob/main/5%20%E5%88%86%E9%92%9F%E6%89%8B%E6%8A%8A%E6%89%8B%E6%95%99%E4%BD%A0%E6%8A%8A%20VPS%20%E6%90%AD%E5%BB%BA%E6%88%90%20VPN%E4%BB%A3%E7%90%86%EF%BC%882026%EF%BC%89.md) 

假设你有一个**代理节点**（比如[自己搭建的VPS](https://github.com/vps888/vpn/blob/main/5%20%E5%88%86%E9%92%9F%E6%89%8B%E6%8A%8A%E6%89%8B%E6%95%99%E4%BD%A0%E6%8A%8A%20VPS%20%E6%90%AD%E5%BB%BA%E6%88%90%20VPN%E4%BB%A3%E7%90%86%EF%BC%882026%EF%BC%89.md)、购买的VPN代理等），还有一个**出口节点**（比如购买的静态IP）。

由于直连出口节点网络质量很差，经常丢包，所以希望通过代理节点中转一下，这种路由方式称为链式代理。

本文主要介绍代理+静态IP的链接方法。

## 1. V2RayN

适用于Windows、MacOS

* 第一步：配置项 -> （提前复制代理链接信息）从剪切板导入分享链接
* 第二步：配置项 -> 添加[SOCKS] -> 填写IP、端口号、用户名和密码即可
* 第三步：配置项 -> 添加链式代理 -> 选择自建节点  -> 下方白框中右键“添加子配置”（选择代理节点） -> 再次右键“添加子配置”（选择出口节点）

<img src="https://github.com/vps888/vpn/blob/main/d3dd70a903541f37116786f3b4d19a17.png" alt="V2RayN链式代理" style="zoom:50%;" />

## 2. Clash内核系列

主要适用于Windows、MacOS、Android等。Clash工具主要是通过yaml配置文件设置，链式代理设置方式如下：

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

* US-HostDare-IP 是代理节点信息，使用的VLESS协议；
* US-StaticIP-via-HostDare 是出口节点（静态IP）信息，使用的socks5协议，重点是：**dialer-proxy: "US-HostDare-IP"**，意思就是通过**US-HostDare-IP**链接。

## 3. Shadowrocket（小火箭）

主要适用于iOS、iPadOS等。

配置方式和V2rayN一样简单，首先还是先添加代理节点和出口节点信息，不赘述。

链式代理配置方式：编辑出口节点信息 -> 代理通过 -> 选择代理节点。

<img src="https://github.com/vps888/vpn/blob/main/image-20260508213100284.png" alt="Shadowrocket链式代理设置" style="zoom:50%;" />

<img src="https://github.com/vps888/vpn/blob/main/image-20260508213242668.png" alt="Shadowrocket链式代理效果" style="zoom:50%;" />
