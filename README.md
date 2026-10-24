# SilvEx DEX Node

Indexing, routing, and quote services for SilvEx DEX.

**Status: planned — scaffold only.** No chain indexer, route engine, quote API,
or cache exists in this repository yet. This is a read-only service: it never
holds keys, signs transactions, or settles trades.

## Purpose

Give the SilvEx DEX frontend a fast, consistent view of on-chain liquidity and
a route for a requested trade. The node indexes BTCS L2 market events, builds
a routing graph, and returns quotes with an explicit block reference and
expiry.

## Scope

Planned work, in dependency order:

1. **Market indexer** — follow BTCS L2 blocks, decode pool and order events,
   and maintain reserves, liquidity, and market metadata.
2. **Reorg handling** — track canonical block hashes, reverse orphaned events,
   and expose the indexed tip with every response.
3. **Routing engine** — build a token graph and search for candidate routes by
   output, fees, and hop count.
4. **Quote service** — return amount in, expected out, minimum out, price
   impact, route, block reference, and expiry.
5. **API** — read-only endpoints for tokens, markets, routes, quotes, and
   health, with pagination and rate limits.
6. **Operations** — backfill, reindex, cache warming, metrics, and a documented
   recovery procedure.

## Out of scope

- Custody, signing, transaction submission, or settlement.
- Centralized order books and matching (that is `SilvEx-CEX`).
- The swap interface (that is `SilvEx-DEX`).
- Consensus changes or a replacement BTCS L2 node.
- Presenting a quote as a guaranteed execution price.

## Planned components

| Component | Responsibility | Depends on |
| --- | --- | --- |
| `ingest/` | Block, log, and market event ingestion | BTCS L2 RPC |
| `store/` | Canonical market and liquidity state | Database |
| `router/` | Graph construction and route search | `store/` |
| `quotes/` | Quote calculation and expiry | `router/` |
| `api/` | Read-only HTTP endpoints | `quotes/`, `store/` |
| `ops/` | Backfill, metrics, and health | All |

The directory names are proposed. They are not created yet.

## Integration points

- [SilvEx-DEX](https://github.com/Silver-Labs-Technology-Development/SilvEx-DEX)
  — consumes quotes and market data.
- [btcs-l2](https://github.com/Silver-Labs-Technology-Development/btcs-l2)
  — EVM blocks, receipts, logs, and settlement state.
- [Silver-Scan](https://github.com/Silver-Labs-Technology-Development/Silver-Scan)
  — optional transaction and contract references for users.
- [SilverForge-Studio](https://github.com/Silver-Labs-Technology-Development/SilverForge-Studio)
  — token metadata for listed assets.
- [silver-labs-platform](https://github.com/Silver-Labs-Technology-Development/silver-labs-platform)
  — shared schema and client conventions.

## Local development

There is no runnable code yet. The intended loop is a disposable BTCS L2 node
with seeded test markets, indexed into a local database, plus a local quote
request against a known fixture. Development defaults bind to loopback.

## Safety and release status

This is a development scaffold with no implementation or audit. Quote services
are latency-sensitive and easy to misread; every response must carry its block
reference and expiry. Never commit RPC credentials, wallet data, `.env` files,
or production database dumps.

See [`SECURITY.md`](SECURITY.md).

## Repository layout

```text
.
├── README.md
├── LICENSE
├── SECURITY.md
├── .gitignore
└── docs/
    └── ARCHITECTURE.md
```

## License

No open-source license is granted. See [`LICENSE`](LICENSE). The repository is
public for review and development, not for reuse or redistribution.

## Related repositories

- [SilvEx DEX](https://github.com/Silver-Labs-Technology-Development/SilvEx-DEX)
- [SilvEx CEX](https://github.com/Silver-Labs-Technology-Development/SilvEx-CEX)
- [BTCS L2](https://github.com/Silver-Labs-Technology-Development/btcs-l2)
- [Silver Labs workspace](https://github.com/orgs/Silver-Labs-Technology-Development/repositories)
