# Hermes Backtesting

## Purpose

The backtest service evaluates the Hermes strategy against historical market data and returns trade-level and summary information to the web UI.

## Output

The backtest response can include information such as:

- total trades
- wins and losses
- win rate
- total R
- average R
- profit factor
- maximum drawdown
- compounded return
- entry and exit details
- target and stop information
- trade history

## Validation

The endpoint was validated directly with:

```bash
curl -i http://127.0.0.1:8000/backtest/AAPL
```

A successful endpoint response returns:

```text
HTTP/1.1 200 OK
```

## Bug fixed during development

The backtest initially crashed when `start_date` was `None`.

The problematic logic compared a string date to `None`, producing:

```text
TypeError: '>=' not supported between instances of 'str' and 'NoneType'
```

The fix was to only perform the date comparison when `start_date` is present:

```python
if start_date:
    i = start_index

    for idx, date in enumerate(history.index):
        if str(date.date()) >= start_date:
            i = max(start_index, idx)
            break
else:
    i = start_index
```

After rebuilding the Docker image, `/backtest/AAPL` returned `200 OK` and the dashboard rendered the backtest.

## Important validation note

The dashboard and API should use the same source of truth for trade counts and performance statistics. During development, one UI view displayed `31` trades while an API summary reported `19`. This discrepancy should be investigated before using the displayed metrics as final performance figures.

## Backtest limitations

Historical backtests are simulations. Results depend on:

- data quality
- selected historical period
- strategy assumptions
- transaction-cost assumptions
- slippage assumptions
- entry/exit rules
- survivorship and look-ahead considerations

Before using the system for serious research, the strategy should be independently validated and tested for look-ahead bias and other methodological issues.
