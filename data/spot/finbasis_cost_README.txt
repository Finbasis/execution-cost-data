FINBASIS - EXECUTION COST BY ORDER SIZE (SPOT)

What this is
  Measured cost of a market order of a given USD size, derived from live order books of two venues: Hyperliquid (on-chain, DEX) and Bitget (centralised, CEX).
  In the venue column: hyperliquid (on-chain, DEX) or bitget (centralised, CEX), the same values in every file. Only derived indicators are published; no exchange raw data.
  Venue names say where the data comes from; Finbasis is not affiliated with either venue.

Measurement window
  2026-10-13T19:19:59Z .. 2026-10-27T19:55:56Z UTC (14.03 days), 12 coins, order sizes in USD: 500, 2,000, 10,000, 25,000, 100,000.
  Books were sampled about every 5 minutes.

Units
  All values are in basis points (bp) of the order size. 100 bp = 1%.

Columns - finbasis_cost_table.csv
  median_bp, p90_bp  Median and 90th percentile of price impact: average of the buy and the sell deviation of the
                     volume-weighted fill price from the mid price. Includes half of the bid-ask spread; fees are NOT included.
  censored_share     Share of measurements where the visible book ended before the order size was filled. Those
                     measurements are excluded from median_bp and p90_bp.
  n                  Number of measurements in the window.
  reliable           1 if at least 100 measurements had a value and censored_share is below 10%. Otherwise 0 and
                     median_bp / p90_bp are left empty.
  An empty cell means no data. Nothing is extrapolated below the smallest or above the largest measured size.

Round trip (open + close) = 2 x (taker fee + impact).
  Nominal taker fees without discounts used on the site: Hyperliquid 7.0 bp, Bitget 10.0 bp. Enter your own fee in the calculator.

Columns - finbasis_cost_by_hour.csv
  Median impact by UTC hour of day. ratio_to_window = the hour's median divided by the median of the whole window
  for the same venue, coin and size. n, n_days = measurements and distinct days behind the hour. Only hours with enough
  measurements (at least 60, over at least 7 days) and censored_share below 10% are listed.

Licence
  CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/). When you reuse these tables, please link to https://finbasis.app/.

Limitations
  - Two venues, one measurement window, sampling every 5 minutes; values describe that window, not the future.
  - Hyperliquid publishes an order book truncated to roughly 20 levels, so impact at large sizes is a lower bound.
  - Only the measured order sizes are published; nothing is estimated between them (the calculator offers the same sizes).
  - Historical measurement, not a quote and not financial advice.
