# Shadowrocket / Clash / Mihomo 规则配置

提供两种分流方向，适用于 Shadowrocket 与采用 Clash / Mihomo 内核的客户端。

## 先选模式

| 使用场景 | 选择的配置 | 结果 |
| --- | --- | --- |
| 人在中国大陆，希望国内直连、海外走代理 | `cn-direct` | 中国域名和中国 IP 直连，其余流量走代理 |
| 人在海外，希望国内服务走回国节点、海外保持本地网络 | `back-cn` | 中国域名和中国 IP 走回国代理，其余流量直连 |

两种配置方向相反，只选择其中一种使用。

## Shadowrocket

在 Shadowrocket 的「配置」页点击右上角「+」，选择「扫描二维码」或「从 URL 导入」。通过 URL 或二维码导入的配置可使用客户端的更新功能。

### 国内直连，其他代理

![cn-direct QR](https://api.qrserver.com/v1/create-qr-code/?size=260x260&margin=12&data=https%3A%2F%2Fraw.githubusercontent.com%2FPeterLooper%2Frules-config%2Fmain%2Fcn-direct.conf)

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/cn-direct.conf
```

分流顺序：局域网直连 -> GFW / 代理域名走代理 -> 中国域名和中国 IP 直连 -> 其余走代理。

### 回国模式，其他直连

![back-cn QR](https://api.qrserver.com/v1/create-qr-code/?size=260x260&margin=12&data=https%3A%2F%2Fraw.githubusercontent.com%2FPeterLooper%2Frules-config%2Fmain%2Fback-cn.conf)

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/back-cn.conf
```

分流顺序：局域网直连 -> 指定服务直连例外 -> 中国域名和中国 IP 走回国代理 -> 其余直连。

导入后，在首页选择节点。回国模式必须选择中国大陆或可回国的节点。

## Clash / Mihomo

以下文件用于支持覆写、混入或合并配置的 Clash / Mihomo 客户端。二选一导入，并使用客户端的“替换规则”或同等功能；不要追加到原有 `MATCH` 规则之后。

### 国内直连，其他代理

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/clash-cn-direct.yaml
```

### 回国模式，其他直连

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/clash-back-cn.yaml
```

文件会创建 `PROXY` 策略组并收集订阅中的节点。导入或刷新后，在 `PROXY` 中选择节点；回国模式请选择中国大陆出口。

推荐使用「规则」模式，不要使用「全局」模式。全局模式会跳过分流规则并让所有流量经过当前节点。

配置包含 DNS 与虚拟网卡设置。若客户端自己的“DNS 覆写”覆盖了配置文件，请关闭该覆写、重新加载配置或重启内核。浏览器的“安全 DNS”是独立设置，必要时关闭或重启浏览器后再测试。

## 规则范围

- 中国域名和中国 IP 规则来自持续维护的远程规则集。
- 中国 IP 使用 `ChinaIPs` 规则集，覆盖中国 IPv4 与 IPv6 网段，并由 `GEOIP,CN` 补充兜底。
- Shadowrocket 已启用 IPv6；`prefer-ipv6 = false` 仅表示优先 IPv4，不会禁用 IPv6 连接或分流。
- Clash / Mihomo 的远程 `rule-providers` 默认每 24 小时更新一次。

## 回国模式直连例外

`back-cn` 配置会优先直连以下服务，避免它们被中国流量回国规则接管：

- 无忧行（JegoTrip）
- 微信、微信支付及常用图片/头像资源
- Apple 服务，包括 Apple ID、iCloud、App Store、推送与内容分发
- TikTok 与海外字节跳动服务

## 更新

- Shadowrocket：在「配置」列表中对通过 URL 导入的配置使用「更新」。
- Clash / Mihomo：刷新覆写或重新加载配置；远程规则集会按 24 小时周期更新。
- 本地文件导入可能没有更新入口，建议使用本 README 中的 Raw URL 或二维码。

## 常见问题

### 国内服务没有走回国节点

确认启用的是 `back-cn`，且 `PROXY` 或 Shadowrocket 当前节点为可用的中国大陆出口。规则决定路径，节点实际地区和可用性由所选节点决定。

### 某个应用仍然异常

应用可能使用海外 CDN、私有域名或特殊接口。请在客户端的请求记录中查找实际命中域名，再补充精确规则。

### 为什么 DNS 检测和出口国家不同

分流配置下，不同服务可能使用不同的出口；浏览器的安全 DNS 也可能绕开客户端 DNS 设置。请先确认客户端处于「规则」模式，再检查 DNS 覆写和浏览器安全 DNS。

## 更新记录

### 2026-09-03

- 回国模式新增无忧行、微信及微信支付、Apple 服务的优先直连例外。
- 同步更新 `back-cn.conf` 与 `clash-back-cn.yaml`。

### 2026-08-24

- 中国 IP 规则源升级为同时覆盖 IPv4 / IPv6 的 `ChinaIPs`。
- 新增通用 Clash / Mihomo 覆写文件。

### 2026-08-22

- 增加字节跳动国内 / 海外、抖音与 TikTok 的优先分流。
