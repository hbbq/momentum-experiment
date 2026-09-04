# Universe snapshots

This directory contains the exact Nasdaq Stockholm Large Cap + Mid Cap security universe used by each weekly Momentum v1.0 signal calculation.

Create one file per signal date:

`YYYY-MM-DD.csv`

Required columns:

```text
signal_date,segment,company,exchange_symbol,yahoo_symbol,source_url,retrieved_at_utc
```

The dated CSV is immutable historical input. If a later correction is necessary, document the reason in the corresponding weekly snapshot rather than silently rewriting history.

See `DATA_SOURCES.md` for source priority, validation and ticker-mapping rules.
