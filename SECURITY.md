# Security policy

## Scope

This repository is a **planned development scaffold** for a read-only DEX
indexing, routing, and quote service. There is no runnable service, production
deployment, or audit.

## Reporting

Do not open a public issue containing exploit details, credentials, or wallet
data. Report privately to the Silver Labs maintainers through the organization
account <https://github.com/Silver-Labs-Technology-Development>.

## Security boundaries for future work

- The service never holds keys, signs transactions, or submits settlement.
- Every quote includes its block reference, indexed tip, and expiry.
- Stale or lagging indexes are labeled and must not serve confident quotes.
- Reorg reversal is transactional and tested.
- Route search is bounded by hops, candidates, and a time budget.
- Never commit RPC credentials, `.env` files, wallet data, or production
  database dumps.

## Known limitations

- No implementation, test suite, load test, or audit exists.
- No routing or quote path has been validated against a live market.
