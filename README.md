# Shadowrocket / Clash / Mihomo 规则配置

提供两种分流方向：国内直连、其他代理；或中国流量走回国代理、其他直连。包含 Shadowrocket 配置与可用于 Clash / Mihomo 内核应用的规则覆写。

最近更新：2026-08-24

## 扫码导入

打开 Shadowrocket，进入「配置」，点击右上角「+」，选择「扫描二维码」。

### 国内直连，其他代理

适合人在中国大陆，或普通代理分流场景。

![cn-direct QR](https://api.qrserver.com/v1/create-qr-code/?size=260x260&margin=12&data=https%3A%2F%2Fraw.githubusercontent.com%2FPeterLooper%2Frules-config%2Fmain%2Fcn-direct.conf)

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/cn-direct.conf
```

规则逻辑：

```text
局域网直连
GFW / 代理域名走代理
中国域名直连
中国 IP 段直连
其他全部走代理
```

适合用途：

- 微信、支付宝、淘宝、京东、B 站等国内服务直连
- Google、YouTube、Telegram、ChatGPT 等海外服务走代理
- 日常使用时减少国内 App 异常

### 回国模式，其他直连

适合人在海外，需要访问中国大陆网站、视频、音乐、网银、生活服务等场景。

![back-cn QR](https://api.qrserver.com/v1/create-qr-code/?size=260x260&margin=12&data=https%3A%2F%2Fraw.githubusercontent.com%2FPeterLooper%2Frules-config%2Fmain%2Fback-cn.conf)

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/back-cn.conf
```

规则逻辑：

```text
局域网直连
.cn 域名走回国代理
中国域名走回国代理
中国 IP 段走回国代理
其他全部直连
```

适合用途：

- 海外访问国内视频、音乐、网银、政务、生活服务
- 国内网站 / App 走回国节点
- 国外网站保持本地直连

## 手动导入

如果扫码无法识别，可以手动导入：

1. 打开 Shadowrocket
2. 进入「配置」
3. 点击右上角「+」
4. 选择「从 URL 导入」或「Download from URL」
5. 粘贴上面的配置地址
6. 保存后选择对应配置
7. 回到首页，选择合适的代理节点

## 如何更新

两份配置文件都已经包含 `update-url`。

通过 URL 或二维码导入后，可以在 Shadowrocket 的配置列表里使用「更新」功能。更新后会重新拉取 GitHub 上的最新配置。

注意：如果是通过本地文件导入，可能不会显示更新功能。建议使用二维码或 Raw URL 导入。

## Clash / Mihomo 使用

两份 YAML 采用 Clash / Mihomo 标准的 `rule-providers` 与 `rules` 字段，可用于采用 Clash / Mihomo 内核的应用。它们是完整的分流规则段，不包含节点或订阅信息。

使用前，请确保你的完整配置中有名为 `PROXY` 的代理策略组；如果实际名称不同，请将文件中的 `PROXY` 全部替换成你的策略组名。

### 国内直连，其他代理

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/clash-cn-direct.yaml
```

### 回国模式，其他直连

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/clash-back-cn.yaml
```

将 YAML 中的 `rule-providers` 与 `rules` 合并到完整配置的对应字段。因为两份文件均以 `MATCH` 结束，应替换完整配置原有的 `rules`，不能追加在已有 `MATCH` 之后。使用客户端的覆写、混入或合并功能时，选择“替换规则”或同等选项。

二选一使用：国内直连模式的末尾规则是 `MATCH,PROXY`；回国模式的末尾规则是 `MATCH,DIRECT`。

两份规则通过 `rule-providers` 每 24 小时更新中国域名、中国 IP 和 GFW 规则源；字节跳动国内 / 海外、抖音和 TikTok 的规则始终位于通用规则之前。

## 两份配置怎么选

```text
人在国内，想让国内直连、国外代理：选 cn-direct.conf
人在海外，想让国内服务走回国节点、国外直连：选 back-cn.conf
```

不建议同时启用两份配置，因为两份配置方向相反。

## 更新记录

### 2026-08-24

- 将 YAML 文件统一为通用名称：`clash-cn-direct.yaml` 与 `clash-back-cn.yaml`
- 改用 Clash / Mihomo 标准 `rules` 与 `rule-providers` 字段，策略组统一使用 `PROXY`
- 支持国内直连 / 其他代理与回国 / 其他直连两种方向，并保留字节跳动国内 / 海外、抖音、TikTok 的优先分流
- README 改为通用 Clash / Mihomo 使用说明，明确策略组替换、规则替换和远程规则自动更新方式

### 2026-08-22

- 新增字节跳动国内 / 海外分流优先规则，避免被通用远程规则集或域名兜底规则覆盖
- 国内字节与抖音域名：`douyin.com`、`iesdouyin.com`、`douyinpic.com`、`douyinvod.com`、`douyinstatic.com`、`bytedance.com`、`byteimg.com`、头条 / 西瓜 / 豆包 / 扣子 / 火山引擎相关域名
- TikTok 与海外字节域名：`tiktok.com`、`tiktokv.com`、`tiktokcdn.com`、`tik-tokapi.com`、`musical.ly`、`byteoversea.com`、`bytedance.net`、`ibytedtos.com` 等
- `cn-direct.conf`：国内字节 / 抖音直连，TikTok / 海外字节走代理
- `back-cn.conf`：国内字节 / 抖音走回国代理，TikTok / 海外字节直连
- 保留两份配置的 `update-url` 与二维码导入地址不变，Shadowrocket 可直接使用「更新」拉取本次规则

### 2026-07-31

- 补充国内云服务与 CDN 域名：腾讯云、阿里云、京东云、百度智能云/对象存储、火山引擎等常见域名
- 补充国内 AI / 内容平台域名：豆包、扣子、抖音/头条图片域名、快手 App、小红书备用域名等
- 补充国内生活服务与电商相关域名：菜鸟、顺丰、滴滴静态资源、什么值得买、拼多多图片域名等
- 同步更新 `cn-direct.conf` 与 `back-cn.conf`，两份配置的规则方向保持不变
- 保留远程 `proxy.txt`、`direct.txt`、`cncidr.txt` 规则源和两份配置的 `update-url`，Shadowrocket 可继续直接更新

### 2026-07-19

- 更新 GFW/代理域名分流：在 `cn-direct.conf` 中新增远程 `proxy.txt` 规则集，并放在中国直连规则之前，避免 GFW 域名被误判为直连
- 更新中国域名分流说明：继续引用 `direct.txt`、`apple.txt`、`icloud.txt` 作为远程中国域名/常用直连规则源
- 更新中国 IP 分流说明：继续引用 `cncidr.txt`，并保留 `GEOIP,CN` 作为中国 IP 兜底
- 同步更新 `cn-direct.conf` 和 `back-cn.conf` 的更新时间为 `2026-07-19`
- 保留两份配置原有 `update-url` 不变，已通过 URL/二维码导入的 Shadowrocket 配置可继续使用「更新」功能

### 2026-07-17

- 补充腾讯 CDN / 微信服务域名：`gtimg.com`、`qpic.cn`、`qqmail.com`、`servicewechat.com`
- 补充腾讯云域名：`myqcloud.com`、`qcloud.com`
- 补充今日头条 / 西瓜视频：`toutiao.com`、`ixigua.com`
- 补充饿了么 / 滴滴：`ele.me`、`elemecdn.com`、`didi.cn`、`didiglobal.com`
- 补充 WPS / 金山文档：`wps.cn`、`wps.com`、`kdocs.cn`、`kingsoft.com`
- 补充飞书：`feishu.cn`、`feishu.net`
- 补充银联：`unionpay.com`、`95516.com`
- 同步更新 `cn-direct.conf` 和 `back-cn.conf`，并保留原有 `update-url` 与 Raw 链接不变

## 常见问题

### 扫码后没有自动导入怎么办？

复制二维码下面的 Raw URL，在 Shadowrocket 里选择「从 URL 导入」。

### 为什么导入后不能更新？

通常是因为使用了本地文件导入。请使用二维码或 Raw URL 导入。

### 为什么国内网站还是没有走回国？

请确认当前选择的代理节点是中国大陆或适合回国的节点。规则只负责分流，节点质量和节点地区仍然取决于你选择的代理。

### 为什么有些国内 App 仍然异常？

部分 App 会使用海外 CDN、特殊域名、私有接口或风控检测。可以在 Shadowrocket 的「最近请求」里查看命中的域名，再按需补充规则。

## 规则来源

配置中引用了远程维护的中国域名和中国 IP 规则集，并使用 `GEOIP,CN` 作为兜底。

远程规则集更新后，Shadowrocket 更新配置时会同步获得更完整的分流效果。
