# Momentum Experiment

A forward-tested shadow portfolio for a simple momentum strategy on Nasdaq Stockholm Large Cap and Mid Cap.

The strategy definition is frozen in `STRATEGY.md`. Operational market-data sources and reproducibility rules are defined separately in `DATA_SOURCES.md`. Historical runs are appended under `history/`, exact dated security universes under `universe/`, and full weekly snapshots under `snapshots/`.

Start value for the shadow portfolio: **100,000 SEK**.

The purpose is to evaluate the strategy prospectively without tuning parameters to past outcomes.

A weekly run is valid only when its universe and price inputs can be reproduced. Failed data retrieval is recorded as `RUN_FAILED` and must never be reported as "no new BUY/SELL signals".
