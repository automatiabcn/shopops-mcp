# ShopOps MCP

**AI-powered server that implements the Model Context Protocol (MCP) for managing Shopify and WooCommerce stores.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6.svg)](https://www.typescriptlang.org/)
[![MCP](https://img.shields.io/badge/MCP-1.29-2A9D8F.svg)](https://modelcontextprotocol.io/)

---

## Features

- **Store connectors** for Shopify and WooCommerce.
- **12 MCP tools** covering inventory, pricing, customers, orders, product performance and reporting.
- **4 MCP resources** exposing store overview, inventory, recent orders and top customers.
- Inventory forecasting using moving-average demand plus safety-stock calculation.
- RFM-based customer segmentation (7 distinct segments).
- Data-driven pricing analysis with margin-based optimization suggestions.
- Order anomaly / fraud detection.
- ABC analysis of product performance.
- Automated daily and weekly reports.
- Dual transport: local `stdio` and Streamable HTTP (MCPize).
- TypeScript, `@modelcontextprotocol/sdk` v1.29+, Zod v4.
- Free tier, plus a €29 lifetime Pro license.

---

## Quick Start

```bash
# 1. Install the package
npm i shopops-mcp

# 2. Create a .env file (see Configuration section)
cp .env.example .env

# 3. Run the server (local stdio mode)
npx shopops-mcp run --transport stdio

# 4. Or start the HTTP endpoint (MCPize deployment)
npx shopops-mcp run --transport http --port 8080
```

The server will read the environment variables, connect to the configured store(s), and expose the MCP tools and resources.

---

## MCP Tools

| Tool | Description |
|------|-------------|
| `store_connect` | Connects, lists, disconnects, or syncs a Shopify/WooCommerce store (`action: "connect" \| "list" \| "disconnect" \| "sync"`) and validates credentials. |
| `store_demo_seed` | Creates a realistic demo store (20 products, 40 customers, 150+ orders) so you can explore every tool without real store credentials. |
| `inventory_status` | Returns current stock levels, back-order flags and low-stock alerts. |
| `inventory_forecast` | Projects future inventory requirements using moving-average demand and safety-stock buffers. |
| `pricing_analyze` | Generates a price elasticity report and identifies under-/over-priced SKUs. |
| `pricing_optimize` | Suggests optimal price points based on margin analysis, sales velocity, and configurable pricing rules. |
| `customers_segment` | Performs RFM analysis and assigns customers to one of seven segments. |
| `customers_churn` | Scores customers for churn risk and provides retention recommendations. |
| `order_anomalies` | Detects potentially fraudulent or erroneous orders using pattern-recognition models. |
| `product_performance` | Conducts ABC analysis and returns contribution metrics per product class. |
| `report_daily` | Generates a JSON/CSV daily operations summary (sales, inventory, alerts). |
| `report_weekly` | Generates a weekly performance report with trend visualisations. |

---

## MCP Resources

| Resource | Description |
|----------|-------------|
| `store://overview` | High-level store metrics: product, order, and customer counts per connected store. |
| `store://inventory` | Low-stock alerts: active products with on-hand quantity ≤ 10, sorted lowest first. |
| `store://orders/recent` | The 20 most recent orders across all stores, with order number, total, status and date. |
| `store://customers/top` | Top 20 customers by total spending, with name, email and order count. |

---

## Configuration

ShopOps reads only a handful of environment variables. **Store credentials are not env vars** —
they are passed to the `store_connect` tool at runtime (one connection per store), so the same
server process can manage multiple Shopify/WooCommerce stores.

| Variable | Required | Description |
|----------|----------|-------------|
| `PORT` | No | If set (or `MCPIZE=true`), the server runs as a Streamable HTTP endpoint on this port (default `8080`) instead of local `stdio`. |
| `MCPIZE` | No | Set to `true` by the MCPize runtime to force HTTP transport. |
| `LEMONSQUEEZY_LICENSE_KEY` | No | Pro license key. Without it the server runs in Free tier (see [Pro License](#pro-license)). |

A minimal `.env` is provided in [`.env.example`](.env.example).

### Connecting a store

Shopify and WooCommerce credentials are supplied as parameters to `store_connect`, e.g.:

```jsonc
// Shopify
{ "action": "connect", "platform": "shopify",
  "store_domain": "myshop.myshopify.com", "access_token": "shpat_..." }

// WooCommerce
{ "action": "connect", "platform": "woocommerce",
  "store_url": "https://example.com", "consumer_key": "ck_...", "consumer_secret": "cs_..." }
```

`store_connect` returns a `store_id` that every other tool takes as input. To explore the server
without real credentials, call `store_demo_seed` instead.

> **Roadmap (not yet implemented):** report anonymization, S3 report export, and a configurable
> pricing-model / log-level are planned but not read by the current release.

---

## Pro License

ShopOps ships in **Free mode** — `store_demo_seed`, `store_connect`, `inventory_status`, `pricing_analyze`, `customers_segment`, `product_performance`, and `report_daily` are open. The following tools require a **Pro license**:

- `inventory_forecast` — moving-average demand forecasting + reorder points
- `pricing_optimize` — actionable price-change recommendations
- `customers_churn` — churn risk scoring + retention recommendations
- `order_anomalies` — fraud / anomaly detection
- `report_weekly` — week-over-week trend report + AI insights

**Buy a Pro License (€29, lifetime, 3 machines):** https://automatiabcn.lemonsqueezy.com/buy/cbbe44f0-a146-4c65-88c8-71f371037758

Or get the **[Indie MCP Stack Bundle](https://automatiabcn.lemonsqueezy.com/buy/55e932fd-8319-47f0-8e95-0b86a29f2617)** (€69, all 4 servers).

```bash
export LEMONSQUEEZY_LICENSE_KEY=YOUR-KEY-HERE
```

Or in your MCP client config:

```json
{
  "mcpServers": {
    "shopops-mcp": {
      "command": "npx",
      "args": ["-y", "shopops-mcp-server"],
      "env": { "LEMONSQUEEZY_LICENSE_KEY": "YOUR-KEY-HERE" }
    }
  }
}
```

Validation is cached locally for 24 h — fully offline-capable after first run.

---

## License

ShopOps MCP is released under the **MIT License**. See [LICENSE](LICENSE) for full terms.

---

*Author: Automatia BCN*
