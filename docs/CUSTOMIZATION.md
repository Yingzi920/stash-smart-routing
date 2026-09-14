# 自定义指南

## AI 与 Apple Intelligence 强制代理

模板把以下三类规则放在国内直连规则之前：

```yaml
- GEOSITE,apple-intelligence,⚡ 自动选择
- GEOSITE,category-ai-!cn,⚡ 自动选择
- GEOSITE,google-gemini,⚡ 自动选择
```

`apple-intelligence` 包含 Apple Intelligence 的 ChatGPT Extension 中继端点；`category-ai-!cn` 覆盖 OpenAI、Anthropic、GitHub Copilot、Perplexity、xAI 等国外 AI 服务。配置中还保留了关键域名兜底规则。

根据随附的苹果 AI 教程，模板还加入了 `guzzoni.apple.com`、`mask-api.*`、`mask*.icloud.com`、`cp4.cloudflare.com` 和 `smoot.apple.com` 等 Siri / iCloud Mask 兼容端点。教程中的 `gspel-ssl.ls.apple.com` 疑似把数字 `1` 写成了字母 `l`；模板采用 Apple Intelligence 社区域名清单中的 `gspe1-ssl.ls.apple.com`。

教程里的 `DOMAIN-SUFFIX,ls.apple.com` 与 `DOMAIN-SUFFIX,apps.mzstatic.com` 范围过大，会让普通 Apple 定位服务或 App Store 静态资源也走代理，因此模板没有照搬。

不要把这些规则移到 `GEOSITE,cn` 或 `GEOIP,CN` 后面，否则部分 Apple 或 CDN 端点可能被提前判定为直连。

分流只负责确保这些连接经过代理，并不能单独保证 Apple Intelligence 可用。设备型号、购买地区、Apple 账户地区、系统版本、设备与 Siri 语言，以及代理出口地区仍需满足 Apple 的可用性要求。自动组会选全体可用节点中延迟最低者；如最低延迟节点所在地区不支持目标服务，请在订阅端筛选节点，或另建带地区 `filter` 的策略组。

## 添加必须直连的域名

把规则放在 `GEOSITE,cn,DIRECT` 之前：

```yaml
- DOMAIN-SUFFIX,example.cn,DIRECT
```

`DOMAIN-SUFFIX` 同时匹配根域名和其子域名，适合 APP 使用的一组服务域名。

## 强制某个网站走代理

强制代理规则必须放在所有国内通用规则之前，否则会先被 `GEOSITE,cn` 或 `GEOIP,CN` 命中：

```yaml
- DOMAIN-SUFFIX,example.com,⚡ 自动选择
```

## 调整测速频率

`interval` 单位为秒。默认模板为 300 秒：

```yaml
proxy-groups:
  - name: '⚡ 自动选择'
    type: url-test
    interval: 300
```

更短的间隔能更快响应线路变化，但会增加耗电和测速流量。移动设备通常不建议设置得过短。

## 使用多个订阅

在 `proxy-providers` 下新增 provider，再把名称加入 `use`：

```yaml
proxy-providers:
  provider-a:
    url: https://example.com/a.yaml
    interval: 3600
  provider-b:
    url: https://example.com/b.yaml
    interval: 3600

proxy-groups:
  - name: '⚡ 自动选择'
    type: url-test
    use:
      - provider-a
      - provider-b
    interval: 300
```

## 隐私建议

订阅 URL 常带有账户令牌。请只在设备本地填写，或放入私有仓库；不要提交到公开 Issue、日志或截图中。
