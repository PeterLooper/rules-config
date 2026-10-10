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

分流顺序：局域网直连 -> Apple Intelligence 等专项规则 -> GFW / 代理域名走代理 -> 中国域名和中国 IP 直连 -> 其余走代理。

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
- 中国 IPv4 使用 `ChinaIPs`，另用 `Loyalsoldier/geoip` 的双栈规则集补充 IPv4/IPv6 网段；`GEOIP,CN` 继续兜底。规则源可能遗漏或误判，不能保证所有中国 IP 都被识别。
- Shadowrocket 已启用 IPv6；`prefer-ipv6 = false` 仅表示优先 IPv4，不会禁用 IPv6 连接或分流。
- Clash / Mihomo 的远程 `rule-providers` 默认每 24 小时更新一次。

## Apple Intelligence

Apple Intelligence 的专用中继域名优先于普通 Apple 服务、中国域名和 IP 规则匹配，四份配置保持一致：

| 模式 | 路径 | 节点要求 |
| --- | --- | --- |
| `cn-direct` | `PROXY` | 选择中国大陆以外的节点 |
| `back-cn` | `DIRECT` | 使用所在地网络，适用于人在中国大陆以外 |

覆盖 Apple Intelligence 中继与 Private Cloud Compute 已列出的入口，包括 `apple-relay.apple.com`、Cloudflare / Fastly 中继及 `cp4.cloudflare.com`。普通 Apple 服务沿用既有规则。

请使用「规则」模式。回国模式的直连不等于固定海外节点；如果本地网络在中国大陆，直连就不是海外出口。规则只控制网络路径，不改变 Apple Intelligence 的设备、账号或地区可用条件，也不保证覆盖所有第三方 AI 集成请求。

端点参考：[Apple 企业网络使用清单](https://support.apple.com/en-us/101555)。

## Shadowrocket 失败处理

两份 `.conf` 配置设置 `udp-policy-not-supported-behaviour = REJECT`，当所选代理不支持 UDP 时拒绝对应代理 UDP 请求，不回退为直连；设置 `dns-direct-fallback-proxy = false`，禁止直连 DNS 失败后转用代理解析。失败时相关请求可能不可用，这两项不是 DNS 泄漏检测通过的保证。

这两个字段仅用于 Shadowrocket，不写入 Clash / Mihomo YAML。

## 海外 AI 与数字货币交易平台

海外 AI 和交易平台的专项域名规则优先于通用中国域名与 IP 规则：`cn-direct` 走 `PROXY`，`back-cn` 走 `DIRECT`。海外 AI 平台上提供的中国模型也按平台域名分流；直接访问中国 AI 产品则沿用中国 AI 规则。

覆盖 ChatGPT / OpenAI、Claude、Gemini / AI Studio、Copilot、Grok、Perplexity、Mistral、Cohere、Poe，以及模型 API、AI 编程、图像、视频、音频和内容创作平台。交易平台在既有 Binance、OKX、Bybit、Coinbase、Kraken 等基础上，补充 Gate 新域名、BitMart、CoinEx、BingX、BitMEX、Deribit、Bitunix、LBank、Coincheck、bitbank 和 Poloniex。

本次新增 61 条海外 AI 域名规则和 15 条交易平台域名规则，四份配置同步。额外覆盖 Bitkub（`bitkub.com`）、Binance TH（`binance.th`）、Orbix（`orbixtrade.com`）和 Bitazza（`bitazza.com`），包含这些域名下的 API、登录和其他子域名；保留 Binance、Bybit、OKX/OKEx 的既有专项规则。只匹配列出的域名，不能保证穷尽全部平台、第三方登录、共享 CDN 和新增接口。不将共享云 ASN、通用登录/CDN 域名或宽泛关键词整段接管。专项规则随本仓库配置更新，现有中国域名/IP 远程规则集继续按原机制更新。

核对来源为各平台官方主页，以及 [OpenAI](https://github.com/blackmatrix7/ios_rule_script/tree/master/rule/Shadowrocket/OpenAI)、[Claude](https://github.com/blackmatrix7/ios_rule_script/tree/master/rule/Shadowrocket/Claude)、[Gemini](https://github.com/blackmatrix7/ios_rule_script/tree/master/rule/Shadowrocket/Gemini) 清单中的专用端点；未整体引用这些清单。

## 中国 AI 产品

DeepSeek、豆包/扣子、Kimi、千问、腾讯元宝、智谱、文心/千帆、讯飞星火和硅基流动的已核实产品域名及部分 API 地址，优先于通用域名与 IP 规则匹配：`cn-direct` 直连，`back-cn` 走回国代理。这里只匹配列出的域名及其子域名；第三方登录、搜索、云存储或 CDN 请求仍可能按其他规则分流。

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

### 2026-10-11

- 补充 Bitkub、Binance TH、Orbix 和 Bitazza；保留币安、Bybit、欧易的既有域名规则，四份配置策略一致。
- 四份配置同步补充海外 AI 应用、专用 API 和数字货币交易平台域名，回国模式直连，国内直连模式走代理。
- 专项规则优先于通用中国域名和 IP 规则；保留中国 AI、微信、Apple Intelligence 和既有 Web3 分流。
- 采用精确域名与域名后缀匹配，避免共享云网段和宽泛关键词影响无关服务。

### 2026-10-04

- 增加 Apple Intelligence 专用优先规则：国内直连模式走代理，回国模式直连；四份配置同步覆盖中继和 Private Cloud Compute 入口。
- Shadowrocket 明确代理 UDP 不支持时拒绝请求，并关闭直连 DNS 失败后的代理回退。
- 两种方向的 Shadowrocket 与 Clash / Mihomo 规则同步补充中国 AI 产品及 API 域名；豆包、扣子和火山引擎沿用已有规则。
- AI 专项规则置于通用 GFW、中国域名及 IP 规则之前，不改变微信、Apple、Web3 等既有优先规则。

### 2026-09-26

- Shadowrocket 与 Clash / Mihomo 两种模式均补充动态维护的中国 IPv6 网段规则；保留原有 IPv4 规则与 GeoIP 兜底。
- 更正说明：原 `ChinaIPs` 规则集只包含 IPv4，启用 IPv6 不等于已覆盖中国 IPv6 网段。

### 2026-09-03

- 回国模式新增无忧行、微信及微信支付、Apple 服务的优先直连例外。
- 同步更新 `back-cn.conf` 与 `clash-back-cn.yaml`。

### 2026-08-24

- 引入 `ChinaIPs` 作为中国 IPv4 规则源，并启用 IPv6；中国 IPv6 网段当时主要依赖 GeoIP 兜底。
- 新增通用 Clash / Mihomo 覆写文件。

### 2026-08-22

- 增加字节跳动国内 / 海外、抖音与 TikTok 的优先分流。
