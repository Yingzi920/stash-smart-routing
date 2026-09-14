# Stash 智能分流规则

一套面向中国大陆网络环境的 Stash 配置模板：微信、腾讯及国内网站/APP 优先直连，其他流量使用自动测速代理组，并始终选择当前延迟最低的可用节点。

## 分流逻辑

规则自上而下匹配：

1. 局域网、回环与链路本地地址直连。
2. 微信/腾讯核心域名显式直连，保证优先级。
3. `GEOSITE,tencent` 和 `GEOSITE,cn` 覆盖腾讯生态及常见国内网站/APP。
4. `GEOIP,CN` 让解析到中国大陆 IP 的连接直连。
5. 最终 `MATCH` 将其余流量交给 `url-test` 自动代理组。

自动代理组每 300 秒测速一次，自动选择延迟最低的健康节点。

## 快速开始

1. 打开 [`config/stash.yaml`](config/stash.yaml)。
2. 将 `https://example.com/your-stash-provider.yaml` 替换为你的 Stash/Clash YAML 节点订阅地址。订阅返回内容必须包含顶层 `proxies` 字段。
3. 将修改后的配置导入 Stash。
4. 首次启用时确保能访问 GitHub；Stash 的 `GEOSITE` 数据会在首次使用时按需下载。
5. 在 Stash 的策略页确认 `⚡ 自动选择` 已包含订阅节点。

> 不要把包含私人订阅地址、令牌或节点密码的配置提交到公开仓库。

## 关键配置

```yaml
proxy-groups:
  - name: '⚡ 自动选择'
    type: url-test
    use:
      - subscription
    interval: 300
    lazy: false

rules:
  - GEOSITE,tencent,DIRECT
  - GEOSITE,cn,DIRECT
  - GEOIP,CN,DIRECT
  - MATCH,⚡ 自动选择
```

## 自定义

常见调整方法见 [`docs/CUSTOMIZATION.md`](docs/CUSTOMIZATION.md)。测试用的本地节点集模板见 [`examples/provider.example.yaml`](examples/provider.example.yaml)。

## 设计依据

本项目按 Stash 官方文档编写：

- [配置文件示例](https://stash.wiki/configuration/example-config)
- [策略组与 `url-test`](https://stash.wiki/proxy-protocols/proxy-groups)
- [远程代理集合](https://stash.wiki/proxy-protocols/proxy-providers)
- [规则类型与 `GEOSITE` / `GEOIP`](https://stash.wiki/rules/rule-types)
- [高效配置建议](https://stash.wiki/faq/effective-stash)

## 注意事项

- 这是分流模板，不提供代理节点或订阅服务。
- `GEOSITE` 数据由社区维护，首次加载依赖 GitHub 可达性。
- APP 的连接域名可能随版本变化；如发现误分流，请先查看 Stash 请求日志，再添加精确规则。
- 规则顺序决定优先级，新增直连规则应放在 `MATCH` 之前。

## License

[MIT](LICENSE)
