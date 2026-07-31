# EDtunnel

<p align="center">
  <img src="https://cloudflare-ipfs.com/ipfs/bafybeigd6i5aavwpr6wvnwuyayklq3omonggta4x2q7kpmgafj357nkcky" alt="图片描述" style="margin-bottom: -50px;">
</p>

基于 [edgetunnel](https://github.com/zizifn/edgetunnel) 的 VLESS over WebSocket 代理隧道，部署在 Cloudflare Workers 上。

## 快速部署

### Worker 方式部署

1. 克隆本仓库
2. 修改 `wrangler.toml` 中的配置（UUID、WS_PATH 等）
3. 执行部署命令：

```bash
npx wrangler deploy
```

4. 或者直接点击按钮一键部署：

   [![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/3Kmfi6HP/EDtunnel)

## 当前部署信息

- **Worker 名称**: `wgnew`
- **访问域名**: `https://wgnew.freedomuat.workers.dev`
- **WebSocket 路径**: `/ws`（可通过 `WS_PATH` 环境变量修改）

## 环境变量配置

所有配置均在 `wrangler.toml` 的 `[vars]` 部分设置：

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `UUID` | VLESS 用户 ID，支持多个用逗号分隔 | `880c366d-5855-47b4-94e0-86d4b050df6d` |
| `WS_PATH` | WebSocket 连接路径 | `/ws` |
| `PROXYIP` | 代理 IP，用于绕过 Cloudflare IP 封锁 | 无 |
| `DNS_RESOLVER_URL` | DoH 解析器地址 | `https://dns.google/dns-query` |

### UUID 设置示例

单个 UUID：

```toml
UUID = "your-uuid-here"
```

多个 UUID（逗号分隔）：

```toml
UUID = "uuid1,uuid2,uuid3"
```

> 注意：设置多个 UUID 时，订阅链接仅使用第一个 UUID。

## 访问路径说明

| 路径 | 说明 |
|------|------|
| `/ws` | WebSocket VLESS 代理连接（仅接受 WebSocket 升级请求） |
| `/{UUID}` | VLESS 配置页面，显示 vless:// 链接 |
| `/sub/{UUID}` | 订阅链接（返回 vless:// 格式） |
| `/sub/{UUID}?format=clash` | Clash 格式订阅（base64 编码） |
| `/cf` | Cloudflare 请求信息 |
| 其他路径 | 伪装反向代理 |

## 订阅链接

- **VLESS 订阅**: `https://wgnew.freedomuat.workers.dev/sub/880c366d-5855-47b4-94e0-86d4b050df6d`
- **Clash 订阅**: `https://wgnew.freedomuat.workers.dev/sub/880c366d-5855-47b4-94e0-86d4b050df6d?format=clash`
- **配置页面**: `https://wgnew.freedomuat.workers.dev/880c366d-5855-47b4-94e0-86d4b050df6d`

## VLESS 节点链接

```
vless://880c366d-5855-47b4-94e0-86d4b050df6d@wgnew.freedomuat.workers.dev:443?encryption=none&security=tls&sni=wgnew.freedomuat.workers.dev&fp=randomized&type=ws&host=wgnew.freedomuat.workers.dev&path=%2Fws%3Fed%3D2048#wgnew
```

## 支持端口

有关 Cloudflare 支持的端口列表，请参阅[官方文档](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/ports)。

```text
http 端口: 80, 8080, 8880, 2052, 2086, 2095
https 端口: 443, 8443, 2053, 2096, 2087, 2083
```

## proxyIP（可选）

在 `wrangler.toml` 中设置 `PROXYIP` 变量：

```toml
PROXYIP = "your-proxy-ip-or-domain"
```

`proxyIP` 用于通过代理路由流量，而不是直接连接到 Cloudflare IP。如果不设置此变量，连接到 Cloudflare IP 可能会被阻止。

原因：Cloudflare 暂时阻止了对 Cloudflare IP 范围的主动 TCP 套接字连接，请参考 [tcp-sockets 文档](https://developers.cloudflare.com/workers/runtime-apis/tcp-sockets/#considerations)。

## 本地开发

```bash
npm install
npx wrangler dev
```

本地服务监听 `http://127.0.0.1:8787`。

## DNS 解析

本项目使用 DoH（DNS over HTTPS）进行域名解析，默认使用 Google DNS（`https://dns.google/dns-query`），可通过 `DNS_RESOLVER_URL` 环境变量更换。DoH 通过 HTTPS 加密传输，可有效防止 DNS 污染。
