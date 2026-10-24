# SilvEx DEX Node architecture

> **Status: candidate design note.** The scope and decisions in
> [`README.md`](../README.md) remain authoritative. This document does not
> authorize implementation.


This document describes the intended indexing and routing boundary. It is a
design note, not a description of running software.

## Design principles

1. **Read-only.** The node never signs, submits, or custodies.
2. **Quote with provenance.** Every quote names the block, the indexed tip, the
   route, and an expiry.
3. **Canonical state only.** Orphaned events are reversed transactionally.
4. **Deterministic routing.** The same market snapshot and request produce the
   same candidate routes.
5. **Bounded work.** Route search, graph size, and response time have explicit
   limits.
6. **Stale is labeled.** If the indexer falls behind, the API says so instead
   of serving a confident old price.

## Proposed data flow

```text
BTCS L2 RPC ──► ingester ──► canonical market store ──► routing graph
                                                          │
client ──► quote API ──► quote service ──► route search ──┘
                │
                └──► health, indexed tip, expiry
```

## Components

### Ingester

Follows blocks and logs, decodes market events, and writes idempotently keyed
records. It records the block hash and log index for every event. On restart it
resumes from a checkpoint and verifies the parent hash.

### Canonical market store

Holds pools, reserves, markets, tokens, and the indexed chain tip. Reorg
reversal removes orphaned events and recalculates affected reserves before the
replacement chain is applied.

### Router

Builds a token graph from indexed markets. Route search is bounded by maximum
hops, maximum candidates, and a time budget. Fees and gas are included in the
comparison; the best output is not always the shortest route.

### Quote service

Calculates expected output, minimum output after slippage, price impact, route,
block reference, and expiry. It refuses to quote an unknown token, an empty
market, or a stale index.

### API and operations

Read-only endpoints with pagination and rate limits. Health reports the indexed
tip, lag, last successful ingestion, and database status. Metrics cover ingest
latency, route search time, quote count, and error rate.

## Open decisions

- Database and cache technology.
- Whether routing is computed on demand or precomputed per block.
- How many hops and candidates are allowed by default.
- Which quote fields the frontend treats as mandatory.
- Whether the service exposes a WebSocket market stream in the first release.

## Threat model (initial)

| Risk | Impact | Initial control |
| --- | --- | --- |
| Stale index | Users trade at a bad price | Tip and lag in every response; refuse stale quotes |
| Reorg mishandling | Wrong reserves | Hash-based canonicality and transactional reversal |
| Route search DoS | Service outage | Hop, candidate, and time limits |
| Malformed logs | Corrupt state | Schema validation and bounded decoding |
| Quote manipulation | User loss | Route transparency and on-chain minimum output |
| Credential leak | Node access | Loopback defaults and secret scanning |

## Non-goals

The node does not custody funds, sign orders, settle trades, run a centralized
order book, or replace the chain. It is a derived, read-only view for the DEX
frontend.
