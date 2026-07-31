# EDtunnel

<p align="center">
  <img src="https://cloudflare-ipfs.com/ipfs/bafybeigd6i5aavwpr6wvnwuyayklq3omonggta4x2q7kpmgafj357nkcky" alt="EDtunnel" style="margin-bottom: -50px;">
</p>

VLESS over WebSocket proxy tunnel deployed on Cloudflare Workers. Based on [edgetunnel](https://github.com/zizifn/edgetunnel).

## Quick Deploy

### Deploy as Worker

1. Clone this repository
2. Update `wrangler.toml` with your configuration (UUID, WS_PATH, etc.)
3. Run deploy command:

```bash
npx wrangler deploy
```

4. Or click the button below to deploy directly:

   [![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/3Kmfi6HP/EDtunnel)

## Current Deployment

- **Worker name**: `your-worker-name`
- **Domain**: `https://your-worker-name.workers.dev`
- **WebSocket path**: `/ws` (configurable via `WS_PATH` env variable)

## Environment Variables

All variables are configured in the `[vars]` section of `wrangler.toml`:

| Variable | Description | Default |
|----------|-------------|---------|
| `UUID` | VLESS user ID, multiple UUIDs separated by comma | `880c366d-5855-47b4-94e0-86d4b050df6d` |
| `WS_PATH` | WebSocket connection path | `/ws` |
| `PROXYIP` | Proxy IP to bypass Cloudflare IP blocking | None |
| `DNS_RESOLVER_URL` | DoH resolver URL | `https://dns.google/dns-query` |

### UUID Configuration

Single UUID:

```toml
UUID = "your-uuid-here"
```

Multiple UUIDs (comma-separated):

```toml
UUID = "uuid1,uuid2,uuid3"
```

> Note: When using multiple UUIDs, the subscription link only uses the first UUID.

## Access Paths

| Path | Description |
|------|-------------|
| `/ws` | WebSocket VLESS proxy (WebSocket upgrade requests only) |
| `/{UUID}` | VLESS configuration page with vless:// links |
| `/sub/{UUID}` | Subscription link (vless:// format) |
| `/sub/{UUID}?format=clash` | Clash format subscription (base64 encoded) |
| `/cf` | Cloudflare request information |
| Other paths | Disguised reverse proxy |

## Subscription Links

- **VLESS**: `https://your-worker-name.workers.dev/sub/880c366d-5855-47b4-94e0-86d4b050df6d`
- **Clash**: `https://your-worker-name.workers.dev/sub/880c366d-5855-47b4-94e0-86d4b050df6d?format=clash`
- **Config page**: `https://your-worker-name.workers.dev/880c366d-5855-47b4-94e0-86d4b050df6d`

## VLESS Node Link

```
vless://880c366d-5855-47b4-94e0-86d4b050df6d@your-worker-name.workers.dev:443?encryption=none&security=tls&sni=your-worker-name.workers.dev&fp=randomized&type=ws&host=your-worker-name.workers.dev&path=%2Fws%3Fed%3D2048#your-worker-name
```

## Supported Ports

For a list of Cloudflare supported ports, please refer to the [official documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/ports).

```text
HTTP ports:  80, 8080, 8880, 2052, 2086, 2095
HTTPS ports: 443, 8443, 2053, 2096, 2087, 2083
```

## proxyIP (Optional)

Set `PROXYIP` in `wrangler.toml`:

```toml
PROXYIP = "your-proxy-ip-or-domain"
```

`proxyIP` is used to route traffic through a proxy rather than directly to a website using Cloudflare's CDN. If not set, connections to Cloudflare IPs may be blocked.

Reason: Outbound TCP sockets to Cloudflare IP ranges are temporarily blocked, see [tcp-sockets documentation](https://developers.cloudflare.com/workers/runtime-apis/tcp-sockets/#considerations).

## Local Development

```bash
npm install
npx wrangler dev
```

The local server listens on `http://127.0.0.1:8787`.

## DNS Resolution

This project uses DoH (DNS over HTTPS) for DNS resolution, defaulting to Google DNS (`https://dns.google/dns-query`). You can change it via the `DNS_RESOLVER_URL` environment variable. DoH encrypts DNS queries via HTTPS, effectively preventing DNS pollution.
