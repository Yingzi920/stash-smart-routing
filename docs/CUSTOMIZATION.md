# 自定义指南

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

`interval` 单位为秒。默认模板为 300 秒。更短的间隔能更快响应线路变化，但会增加耗电和测速流量。

## 使用多个订阅

在 `proxy-providers` 下新增 provider，再把名称加入自动选择组的 `use` 列表即可。

## 隐私建议

订阅 URL 常带有账户令牌。请只在设备本地填写，或放入私有仓库；不要提交到公开 Issue、日志或截图中。
