# Finbasis execution cost data

Open data: what it costs to trade crypto perpetual futures and spot on **Hyperliquid** (on-chain, DEX) and **Bitget** (centralised, CEX), measured from live order books.

For each coin, order size and venue you get the **price impact** of a market order (the order-book part of slippage), and by hour of day how that cost moves. Add the exchange fee and you have the round-trip cost of opening and closing a trade.

- Website with the map, calculator and charts: <https://finbasis.app/>
- Calculator (your own size, order type and fees): <https://finbasis.app/calculator>
- Plain-English guide to fees, slippage and funding: <https://finbasis.app/trading-costs-explained>

## What is in here

| File | What it contains |
| --- | --- |
| `data/perp/finbasis_cost_table.csv` | Perpetual futures: price impact by coin, venue and order size ($500, $2,000, $10,000) |
| `data/perp/finbasis_cost_by_hour.csv` | Perpetual futures: median price impact by UTC hour of day |
| `data/perp/finbasis_cost_README.txt` | Column definitions, window, limitations (perpetual futures) |
| `data/spot/finbasis_cost_table.csv` | Spot: the same by coin, venue and order size ($500 to $100,000) |
| `data/spot/finbasis_cost_by_hour.csv` | Spot: median price impact by UTC hour of day |
| `data/spot/finbasis_cost_README.txt` | Column definitions, window, limitations (spot) |

These are the same files that are published on the website, unchanged.

## Measurement windows

- **Perpetual futures:** 12 October 2026 20:29 UTC to 26 October 2026 20:23 UTC (14.00 days). 165 coins in the table; the website shows the 162 that pass its reliability rules on both venues.
- **Spot:** 13 October 2026 19:19 UTC to 27 October 2026 19:55 UTC (14.03 days), 12 coins.
- Order books were sampled about every 5 minutes (up to 4,032 snapshots per coin and venue for perpetual futures).

New measurement windows are added as we measure them; see the website for the latest.

## Columns in short

`finbasis_cost_table.csv`: `market, venue, coin, size_usd, median_bp, p90_bp, censored_share, n, reliable`

`finbasis_cost_by_hour.csv`: `market, venue, coin, hour_utc, size_usd, median_bp, ratio_to_window, n, n_days`

- All values are in **basis points** (bp) of the order size: 100 bp = 1%.
- `median_bp` is the median price impact: how far the average fill price of a market order of that size is from the mid price, averaged over a buy and a sell, with half of the bid-ask spread included and **fees excluded**.
- An empty cell means no data. Nothing is extrapolated.
- Details and exact rules are in the `finbasis_cost_README.txt` next to each table.

## Round-trip cost

Round trip (open and close) = 2 x (taker fee + price impact). The website uses nominal base taker fees without discounts: Hyperliquid 4.5 bp and Bitget 6.0 bp for perpetual futures (7.0 bp and 10.0 bp for spot). Your own fee can differ.

```python
import pandas as pd

t = pd.read_csv("data/perp/finbasis_cost_table.csv")
fee_bp = {"hyperliquid": 4.5, "bitget": 6.0}   # nominal taker fee, bp per side

x = t[(t.size_usd == 2000) & (t.reliable == 1)].copy()
x["round_trip_bp"] = 2 * (x.venue.map(fee_bp) + x.median_bp)
print(x.groupby("venue").round_trip_bp.median())
```

The website counts only coins that are reliable on both venues, so figures from this snippet can differ from the website in the third decimal place (for example, the Bitget median is 0.265% here and 0.264% on the website).

## Limitations

- This is a model, not your fills. The cost is calculated by walking the order book on paper. Real orders also depend on speed, order type and how fast the market moves, and are often worse in a hurry.
- Two venues, one measurement window of about two weeks in ordinary market conditions. Sharp moves, new listings and liquidations are not measured and are usually dearer.
- Hyperliquid publishes an order book truncated to roughly 20 levels, so impact at large sizes is a lower bound.
- Only the measured order sizes are published.
- Historical measurement, not a quote. Information only: not investment, legal or tax advice and not a recommendation to buy, sell or hold any asset or to use any venue. Finbasis is not affiliated with Hyperliquid or Bitget.

## License and attribution

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). You may use, share and adapt the data, including commercially, as long as you give credit and link to <https://finbasis.app/>. For example:

> Source: Finbasis, https://finbasis.app/ (CC BY 4.0)

Only our own derived figures are published here. No raw exchange data.

Contact: contact@finbasis.app
