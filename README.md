# Shadowrocket 规则配置

这个仓库提供两份 Shadowrocket 可更新配置，适合 iOS / iPadOS 上使用。

## 配置文件

### cn-direct.conf

适合人在中国大陆或普通代理分流场景。

规则逻辑：

```text
局域网直连
中国域名直连
中国 IP 段直连
其他全部走代理
```

导入地址：

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/cn-direct.conf
```

适合用途：

- 国内 App / 网站不走代理
- 海外网站走代理
- 微信、支付宝、淘宝、京东、B 站等国内服务尽量直连

### back-cn.conf

适合人在海外，需要访问中国大陆网站、视频、音乐、网银、生活服务等“回国”场景。

规则逻辑：

```text
局域网直连
.cn 域名走回国代理
中国域名走回国代理
中国 IP 段走回国代理
其他全部直连
```

导入地址：

```text
https://raw.githubusercontent.com/PeterLooper/rules-config/main/back-cn.conf
```

适合用途：

- 海外访问国内视频、音乐、网银、政务、生活服务
- 国内网站 / App 走回国节点
- 国外网站保持本地直连

## Shadowrocket 导入方法

1. 打开 Shadowrocket
2. 进入「配置」
3. 点击右上角「+」
4. 选择「从 URL 导入」或「Download from URL」
5. 粘贴上面的配置地址
6. 保存后选择对应配置
7. 回到首页，选择合适的代理节点

## 如何更新

这两份配置文件里已经包含 `update-url`。

通过 URL 导入后，可以在 Shadowrocket 的配置列表里使用「更新」功能。更新后会重新拉取 GitHub 上的最新配置。

注意：如果是通过本地文件导入，可能不会显示更新功能。建议使用上面的 Raw URL 导入。

## 两份配置怎么选

```text
人在国内，想让国内直连、国外代理：选 cn-direct.conf
人在海外，想让国内服务走回国节点、国外直连：选 back-cn.conf
```

## 常见问题

### 为什么导入后不能更新？

通常是因为使用了本地文件导入。请使用 Raw URL 导入。

### 为什么国内网站还是没有走回国？

请确认当前选择的代理节点是中国大陆或适合回国的节点。规则只负责分流，节点质量和节点地区仍然取决于你选择的代理。

### 为什么有些国内 App 仍然异常？

部分 App 会使用海外 CDN、特殊域名、私有接口或风控检测。可以在 Shadowrocket 的「最近请求」里查看命中的域名，再按需补充规则。

### 可以同时启用两份配置吗？

不建议。两份配置方向相反：

- `cn-direct.conf` 是国内直连、其他代理
- `back-cn.conf` 是国内代理、其他直连

使用时选择其中一份即可。

## 规则来源

配置中引用了远程维护的中国域名和中国 IP 规则集，并使用 `GEOIP,CN` 作为兜底。

远程规则集更新后，Shadowrocket 更新配置时会同步获得更完整的分流效果。
