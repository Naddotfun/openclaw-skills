# Agent API Skill - REST API

REST API for AI agents and external services.

## Rate Limits

| Request Source               | Rate Limit              |
| ---------------------------- | ----------------------- |
| nad.fun, localhost           | None                    |
| External (no API Key)        | 10 req/min (IP-based)   |
| External (with API Key)      | 100 req/min (Key-based) |

## Setup

```typescript
const NETWORK = "mainnet" // 'testnet' | 'mainnet'
const API_URL = NETWORK === "mainnet" ? "https://api.nadapp.net" : "https://dev-api.nad.fun"
const headers = { "X-API-Key": "nadfun_xxx" } // optional but recommended
```

## Endpoints

| Endpoint                           | Method | Description             |
| ---------------------------------- | ------ | ----------------------- |
| `/agent/chart/:token_id`           | GET    | OHLCV chart data        |
| `/agent/swap-history/:token_id`    | GET    | Transaction history     |
| `/agent/market/:token_id`          | GET    | Current market data     |
| `/agent/metrics/:token_id`         | GET    | Trading metrics         |
| `/agent/token/:token_id`           | GET    | Token information       |
| `/agent/holdings/:account_id`      | GET    | User holdings           |
| `/agent/token/image`               | POST   | Upload image            |
| `/agent/token/metadata`            | POST   | Upload metadata         |
| `/agent/token/created/:account_id` | GET    | Created tokens list     |
| `/agent/salt`                      | POST   | Mine salt for address   |

## Examples

```typescript
// Chart data
const { t, o, h, l, c, v } = await fetch(`${API_URL}/agent/chart/${tokenId}?resolution=60&from=${fromTs}&to=${toTs}`, { headers }).then(r => r.json())

// Market data
const { market_info } = await fetch(`${API_URL}/agent/market/${tokenId}`, { headers }).then(r => r.json())

// Token info
const { token_info } = await fetch(`${API_URL}/agent/token/${tokenId}`, { headers }).then(r => r.json())

// Holdings
const { tokens } = await fetch(`${API_URL}/agent/holdings/${accountId}`, { headers }).then(r => r.json())

// Upload image (max 5MB)
const { image_uri } = await fetch(`${API_URL}/agent/token/image`, { method: "POST", headers: { ...headers, "Content-Type": "image/png" }, body: imageBuffer }).then(r => r.json())

// Upload metadata
const { metadata_uri } = await fetch(`${API_URL}/agent/token/metadata`, { method: "POST", headers: { ...headers, "Content-Type": "application/json" }, body: JSON.stringify({ image_uri, name, symbol, description }) }).then(r => r.json())

// Mine salt
const { salt, address } = await fetch(`${API_URL}/agent/salt`, { method: "POST", headers: { ...headers, "Content-Type": "application/json" }, body: JSON.stringify({ creator, name, symbol, metadata_uri }) }).then(r => r.json())
```

## API Key Management

Requires session cookie. Max 5 keys per account. **Full `api_key` shown ONLY ONCE at creation.**

```typescript
// Create
const { api_key } = await fetch(`${API_URL}/api-key`, { method: 'POST', headers: { Cookie: 'nadfun-v3-api=<token>', 'Content-Type': 'application/json' }, body: JSON.stringify({ name: 'My Bot' }) }).then(r => r.json())

// List (only returns key_prefix, not full key)
const { api_keys } = await fetch(`${API_URL}/api-key`, { headers: { Cookie: 'nadfun-v3-api=<token>' } }).then(r => r.json())

// Delete
await fetch(`${API_URL}/api-key/${id}`, { method: 'DELETE', headers: { Cookie: 'nadfun-v3-api=<token>' } })
```

## Error Codes

| Code | Description       |
| ---- | ----------------- |
| 400  | Bad Request       |
| 401  | Auth/API Key fail |
| 404  | Not found         |
| 413  | File too large    |
| 429  | Rate limit        |
