# Data sources and reproducibility

This document defines the operational market-data sources for Momentum v1.0. It does **not** change the strategy rules in `STRATEGY.md`; it only makes their inputs reproducible.

## Universe

The strategy universe is Nasdaq Stockholm **Large Cap + Mid Cap**.

### Authoritative definition

Use Nasdaq's Stockholm segment indexes as the authoritative definition of the two segments:

- Large Cap: `OMXSLCPI` / OMX Stockholm Large Cap PI
- Mid Cap: `OMXSMCPI` / OMX Stockholm Mid Cap PI

Nasdaq describes these indexes as containing all companies in the corresponding Stockholm market-cap segment. Nasdaq's annual Nordic market-cap segment review is the authoritative source for segment transfers.

### Operational constituent source

Nasdaq's public index overview can be used to validate the index identity and current component count, but its complete constituent table may require authentication. For an unattended run, a publicly accessible current component list may therefore be used as a retrieval source, provided that:

1. it explicitly identifies itself as the component list for `OMXSLCPI`/`OMXSLCGI` or `OMXSMCPI`;
2. the source URL and retrieval timestamp are recorded;
3. the resulting security list is saved in `universe/YYYY-MM-DD.csv` before any signal calculation;
4. obvious discrepancies against Nasdaq's published index description/component count are treated as a data error, not silently ignored.

Current public fallback pages known to expose component lists are the Investing.com component pages for OMX Stockholm Large Cap GI and OMX Stockholm Mid Cap.

The **committed universe CSV is the actual input of record** for a weekly run. Later changes to an external website must never change an old run.

### Universe CSV format

`universe/YYYY-MM-DD.csv` must contain one row per listed security/share class:

```text
signal_date,segment,company,exchange_symbol,yahoo_symbol,source_url,retrieved_at_utc
2026-09-04,Large Cap,Example AB,EXAMPLE B,EXAMPLE-B.ST,https://...,2026-09-04T18:00:00Z
```

Share classes are separate securities when separately listed and separately present in the source universe.

Ticker mapping must be explicit. Never guess a Yahoo symbol during ranking: if a mapping cannot be verified, fail the run or exclude the security with a documented data error in the snapshot.

## Historical prices

Primary price source: **Yahoo Finance**.

Yahoo defines Adjusted Close as closing price adjusted for applicable stock splits and dividend distributions. This matches the strategy's requirement to calculate 12–1 **total-return momentum** consistently across splits and dividends.

For each security:

- **12–1 momentum:** use Yahoo `Adjusted Close`.
- **Friday close:** use the ordinary historical close for the signal date (or the latest trading day on/before Friday when Friday is not a trading day).
- **MA200:** calculate a 200-trading-day moving average from the ordinary historical close series. The close series must be split-consistent; if the retrieval method exposes unadjusted pre-split prices, use a split-adjusted close series instead.
- Record the Yahoo symbol, first/last price date used, retrieval timestamp and source URL in the weekly snapshot.

The 12–1 calculation uses the adjusted close nearest to, but not after, the two required observation dates: approximately 12 months before the signal date and approximately 1 month before the signal date. Do not use data published after the signal timestamp.

## Benchmark

Use OMXSPI as the benchmark, as specified in `STRATEGY.md`. Record the benchmark source and dates used in each snapshot.

## Required weekly preflight

A weekly run may calculate signals only after all of the following are true:

1. A dated universe CSV for the signal date has been created and committed or is ready to be committed with the run.
2. Every ranked security has an explicit price-source symbol mapping.
3. Sufficient adjusted-price history exists to calculate 12–1 momentum.
4. Sufficient close history exists to calculate MA200.
5. Data is available through the relevant Friday close without using future information.

If any material preflight check fails, the run status is `RUN_FAILED` and **no BUY/HOLD/SELL conclusion may be emitted**.

## Run status semantics

Weekly snapshots must contain one of:

- `OK_TRADES` — calculation succeeded and at least one BUY or SELL exists.
- `OK_NO_TRADES` — calculation succeeded and no BUY/SELL changes exist.
- `RUN_FAILED` — the signal could not be calculated reproducibly.

`RUN_FAILED` is not equivalent to "no new signals". Portfolio/history files that imply a completed strategy calculation must not be updated for a failed run. A failure snapshot may still be written so the problem is auditable.

## Snapshot provenance

Every successful snapshot should record at least:

```json
{
  "run_status": "OK_NO_TRADES",
  "signal_date": "2026-09-04",
  "universe_file": "universe/2026-09-04.csv",
  "universe_source": "...",
  "universe_retrieved_at_utc": "...",
  "price_source": "Yahoo Finance",
  "price_retrieved_at_utc": "...",
  "price_last_date": "2026-09-04"
}
```

A failed snapshot should use `RUN_FAILED` and include a `data_errors` array describing the blocking problem(s).

## Source changes

If Yahoo Finance or the operational constituent source stops being usable, do not silently substitute another source. Document the replacement here first, including how its adjusted prices/dividends/splits are interpreted, then use it for subsequent runs. This is an input-provider change, not a Momentum v1.0 parameter change, but it must still be auditable.
