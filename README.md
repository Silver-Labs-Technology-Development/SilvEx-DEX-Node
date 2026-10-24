# SilvEx DEX Node

> **Status: Planned — repository and scope only.**
> No indexer, router, quote service, database, or deployable node exists here.

## Purpose

SilvEx DEX Node is the infrastructure boundary for DEX-specific indexing,
routing, pool data, and quote services. It exists only to support a DEX
architecture that has been selected and reviewed; it is not a generic indexer
and it is not the SilvEx DEX user application.

## Proposed scope

- Index pool creation, liquidity, swaps, and relevant contract events.
- Maintain canonical pool state with explicit reorganization handling.
- Serve bounded pool, route, and quote APIs.
- Validate and cache RPC reads with stale-data and failure semantics.
- Expose health, readiness, lag, and last-processed-block information.
- Support restart and replay from a defined checkpoint.

## Explicitly out of scope until justified

- Building services before the DEX design selects a chain and protocol.
- Custody, signing, or submission of user transactions.
- Promising executable quotes when pool data is stale or unavailable.
- Replacing the BTCS L2 execution client or BTCS Core RPC.
- A separate chain indexer that duplicates Silver Scan without a documented
  boundary.

## Integration points

- **SilvEx DEX** — the only consumer whose design justifies these services.
- **BTCS Core / BTCS L2 RPC** — canonical chain reads.
- **Silver Scan** — shared indexing boundaries where responsibilities overlap.
- **silver-labs-platform** — schemas and client contracts.
- **SilverPay** — wallet-side display only; no node-held keys.

## Decisions required before implementation

1. The DEX chain and contract design this node must serve.
2. Indexed entities, retention, pagination, and API compatibility.
3. Reorganization depth, rollback behavior, and stale-data policy.
4. Quote semantics, price impact, and failure modes.
5. Deployment, monitoring, and restart/replay requirements.

## First milestone

A design note that maps each required service to a DEX capability, followed by a
local indexer and quote API tested against deterministic fixtures and a
reorganization scenario. No production endpoint.

## Repository facts

- Local workspace path: `~/Silver-Labs/SilvEx-DEX-Node`
- Default branch: `main`
- Implementation: none
- Tests run: none (no code)
- License: no license selected; no reuse rights are granted
- Visibility: public planning repository

This repository is labeled planned so that its existence is not mistaken for a
running service.
