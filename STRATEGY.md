# Momentum v1.0

Status: **FROZEN**

Do not change these rules based on observed performance. Any changed rules must be introduced as a new version and tracked separately.

## Universe

- Nasdaq Stockholm Large Cap
- Nasdaq Stockholm Mid Cap
- OMXS30 is already covered through Large Cap and is not treated separately.

## Signal

- Rank stocks by **12–1 momentum**.
- Measure total return from 12 months ago to 1 month ago.
- Exclude the most recent month from the ranking signal.
- Use adjusted historical prices so splits and dividends are handled consistently.
- A stock must have at least 12 months of sufficient price history to qualify.

## Trend filter

- A stock is eligible for purchase only when Friday's closing price is above its 200-day moving average (MA200).

## Portfolio

- Maximum 10 stocks.
- Equal target weights: 10% of a fully invested model portfolio per position.
- Initial shadow portfolio value: 100,000 SEK.
- If fewer than 10 stocks qualify, leave the remaining allocation in cash.

## Entry

- Fill available portfolio slots with the highest-ranked eligible stocks.

## Hold / buffer rule

- Keep an existing holding while it remains within the top 20 of the momentum ranking and remains above MA200.

## Exit

Sell a holding at the next execution point if any of the following is true at Friday's close:

- Its momentum rank is worse than 20.
- Its closing price is below MA200.
- It has left the Large Cap / Mid Cap universe.
- It is affected by an acquisition, delisting, or equivalent event requiring exit.

## Timing

- Calculate signals using information available through Friday's market close.
- Planned trades are executed at Monday's market open.
- Never use information that became available after the signal timestamp when deciding the trade.

## Reporting

Each weekly run should record:

- Full ranking or sufficient snapshot to reproduce the decision.
- Current model portfolio.
- BUY / HOLD / SELL changes versus the previous run.
- 1m, 3m, 6m and 12m returns may be shown for context, but must not affect decisions.
- Cash allocation.
- Portfolio value.
- Benchmark value versus OMXSPI from the same start date.

## Shadow execution and costs

- Once Monday's opening price is available, use the actual opening price for the shadow execution record.
- Use one explicit and consistent assumption for spread/slippage and brokerage until real trades are used.
- If real trades are later reported, record actual fill prices and actual brokerage instead.

## Change control

Momentum v1.0 is frozen. Parameter changes are not edits to v1.0; they create a new strategy version, e.g. Momentum v2.0, which should preferably be shadow-tracked in parallel from its own start date.
