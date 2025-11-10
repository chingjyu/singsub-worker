# singsub Worker API 文档

## 概述

singsub Worker 是一个部署在 Cloudflare Workers 上的轻量级订阅转换工具。它可以将各种代理协议的分享 URL 转换为不同客户端可用的订阅链接。本文档概述了 API 端点及其用法。

## 基础 URL

所有 API 请求应发送至:

```
https://your-worker-domain.workers.dev
```

将 `your-worker-domain` 替换为您实际的 Cloudflare Workers 域名。

## 端点

### 1. 生成配置

#### sing-sox 配置

- **URL**: `/singbox`
- **方法**: GET
- **参数**:
  - `config` (必需): URL 编码的字符串,包含一个或多个代理配置
  - `selectedRules` (可选): 预定义规则集名称或自定义规则的 JSON 数组
  - `customRules` (可选): 自定义规则的 JSON 数组
  - `pin` (可选): 布尔值，是否将自定义规则置于预定义规则之上
  - `configId` (可选): 字符串，使用保存的配置ID。详见[保存自定义配置](#4-保存自定义配置)

**示例**:
```
/singbox?config=vmess%3A%2F%2Fexample&selectedRules=balanced&customRules=%5B%7B%22site%22%3A%22example.com%22%2C%22ip%22%3A%22192.168.1.1%22%2C%22domain_suffix%22%3A%22.com%22%2C%22ip_cidr%22%3A%2210.0.0.0%2F8%22%2C%22name%22%3A%22MyCustomRule%22%7D%5D
```


#### Xray 配置

- **URL**: `/xray`
- **方法**: GET
- **参数**:
  - `config` (必需): URL 编码的字符串,包含一个或多个代理配置

### 2. 缩短 URL

- **URL**: `/shorten`
- **方法**: GET
- **参数**:
  - `url` (必需): 需要缩短的原始 URL

**示例**:
```
/shorten?url=https%3A%2F%2Fexample.com%2Fvery-long-url
```

**响应**:
```json
{
  "shortUrl": "https://your-worker-domain.workers.dev/s/abcdefg"
}
```

### 3. 重定向短 URL

- **URL**: `/s/{shortCode}`
- **方法**: GET
- **描述**: 重定向到与短代码关联的原始 URL

### 4. 保存自定义配置

- **URL**: `/config`
- **方法**: POST
- **Content-Type**: application/json
- **请求体**:

  ```json
  {
    "type": "singbox",  // 配置类型
    "content": "配置内容"  // 字符串格式的配置内容
  }
  ```

- **响应**: 
  - 成功: 返回配置ID (字符串)
  - 失败: 返回错误信息 (400 状态码)

**说明**:
- 配置内容会进行格式验证
- Clash配置支持YAML和JSON格式
- SingBox配置必须是有效的JSON格式
- 配置将保存30天
- 配置ID可以通过URL参数`configId`使用

**示例**:

``` bash
curl -X POST https://your-worker-domain.workers.dev/config \
-H "Content-Type: application/json" \
-d '{
"type": "clash",
"content": "port: 7890\nallow-lan: false\nmode: Rule"
}'
```

**使用保存的配置**:
将返回的配置ID添加到URL参数中即可使用保存的配置：
```
https://your-worker-domain.workers.dev/clash?config=vmess://xxx&configId=clash_abc123
```

详情请参考[使用说明](#使用说明)

## 预定义规则集

API 支持以下预定义规则集:

- `minimal`: 基本规则集
- `balanced`: 适中规则集
- `comprehensive`: 完整规则集

这些可以在 Sing-Box 和 Clash 配置的 `selectedRules` 参数中使用。

下面是目前支持的预定义规则集：

| Rule Name | Used Site Rules | Used IP Rules |
|---|---|---|
| Ad Block | category-ads-all |  |
| AI Services | category-ai-!cn |  |
| Bilibili | bilibili |  |
| Youtube | youtube |  |
| Google | google | google |
| Private |  | private |
| Location:CN | geolocation-cn | cn |
| Telegram |  | telegram |
| Microsoft | microsoft |  |
| Apple | apple |  |
| Bahamut | bahamut |  |
| Social Media | facebook, instagram, twitter, tiktok, linkedin |  |
| Streaming | netflix, hulu, disney, hbo, amazon |  |
| Gaming | steam, epicgames, ea, ubisoft, blizzard |  |
| Github | github, gitlab |  |
| Education | coursera, edx, udemy, khanacademy, category-scholar-!cn |  |
| Financial | paypal, visa, mastercard, stripe, wise |  |
| Cloud Services | aws, azure, digitalocean, heroku, dropbox |  |

Singbox 的规则集来自 [https://github.com/lyc8503/sing-box-rules](https://github.com/lyc8503/sing-box-rules), 感谢 lyc8503 的贡献!

## 自定义规则

除了使用预定义规则集,您还可以在 `customRules` 参数中提供自定义规则列表作为 JSON 数组。每个自定义规则应包含以下字段:

- `site`: 域名规则，逗号分隔的字符串
- `ip`: IP 规则，逗号分隔的字符串
- `domain_suffix`: 域名后缀规则，逗号分隔的字符串
- `domain_keyword`: 域名关键词规则，逗号分隔的字符串
- `ip_cidr`: IP CIDR 规则，逗号分隔的字符串
- `protocol`: 协议规则，逗号分隔的字符串
- `name`: 出站名称

示例:

```json
[
  {
    "site": "google,anthropic",
    "ip": "private,cn",
    "domain_suffix": ".com,.org",
    "domain_keyword": "Mijia Cloud,push.apple",
    "ip_cidr": "192.168.0.0/16,10.0.0.0/8",
    "protocol": "http,tls,dns",
    "name": "MyCustomRule"
  }
]
```
您还可以使用 `pin` 参数将自定义规则置于预定义规则之上，以便自定义规则生效。

## 错误处理

API 在出现问题时将返回适当的 HTTP 状态码和错误消息:

- 400 Bad Request: 当缺少必需参数或参数无效时
- 404 Not Found: 当请求的资源(如短 URL)不存在时
- 500 Internal Server Error: 服务器端错误

## 使用说明

1. `config` 参数中的所有代理配置应进行 URL 编码。
2. 可以在单个请求中包含多个代理配置,方法是在 URL 编码的 `config` 参数中用换行符 (`%0A`) 分隔它们。
3. 使用自定义规则时,确保规则名称与自定义规则部分列出的名称完全匹配。
4. 缩短的 URL 旨在临时使用,可能在一定时间后过期。

## 示例

1. 生成带有平衡规则集的 Sing-Box 配置:
   ```
   /singbox?config=vmess%3A%2F%2Fexample&selectedRules=balanced
   ```


2. 缩短 URL:
   ```
   /shorten?url=https%3A%2F%2Fyour-worker-domain.workers.dev%2Fsingbox%3Fconfig%3Dvmess%253A%252F%252Fexample%26selectedRules%3Dbalanced
   ```

## 结论

singsub Worker API 提供了一种灵活而强大的方式来生成和管理代理配置。它支持多种代理协议、各种客户端类型和可自定义的路由规则。URL 缩短功能允许轻松共享和管理复杂的配置。
