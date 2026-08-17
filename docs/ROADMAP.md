# Hermes Roadmap

## Phase 1 — Accuracy and reliability

- [ ] Reconcile the frontend trade count with the API summary.
- [ ] Verify win/loss and win-rate calculations.
- [ ] Verify R-multiple calculations.
- [ ] Verify maximum drawdown calculations.
- [ ] Test multiple tickers such as AAPL, MSFT, NVDA, and TSLA.
- [ ] Add automated API tests.
- [ ] Add backtest regression tests.
- [ ] Check for look-ahead bias.

## Phase 2 — Better signal UI

- [ ] Create a dedicated Hermes Trade Signal card.
- [ ] Display BUY / WATCH / SELL prominently.
- [ ] Display confidence and score.
- [ ] Display entry zone.
- [ ] Display target.
- [ ] Display stop.
- [ ] Display risk/reward.
- [ ] Explain why Hermes generated the signal.

## Phase 3 — Better visualization

- [ ] Add price chart.
- [ ] Add entry and exit markers.
- [ ] Add target and stop lines.
- [ ] Add equity curve.
- [ ] Add drawdown chart.
- [ ] Improve responsive/mobile layout.

## Phase 4 — Engineering improvements

- [ ] Add structured logging.
- [ ] Add backend health checks.
- [ ] Add Docker restart policies.
- [ ] Add environment-based configuration.
- [ ] Add CI tests with GitHub Actions.
- [ ] Add API error handling that gives the frontend useful messages.
- [ ] Consider a development bind mount for faster Docker iteration.

## Phase 5 — Deployment

- [ ] Choose a production hosting provider.
- [ ] Configure production environment variables.
- [ ] Configure HTTPS.
- [ ] Deploy the frontend.
- [ ] Deploy the API.
- [ ] Add monitoring and backups.

## Long-term ideas

- Watchlists
- Saved strategies
- Portfolio tracking
- Alert notifications
- More technical indicators
- Strategy comparison
- Multiple backtest periods
- User accounts
- AI-generated explanations
