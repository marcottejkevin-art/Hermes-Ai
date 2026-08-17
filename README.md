# Hermes AI Market Intelligence

Hermes is a full-stack market-analysis and backtesting project built as a hands-on IT/software project. The goal is to turn market data and technical indicators into a clear dashboard with actionable **BUY / WATCH / SELL** signals, risk levels, and historical strategy results.

> **Project status:** Working local web UI with FastAPI backend, Dockerized API, market analysis, and backtesting.

## What Hermes does

- Provides a React/Vite web dashboard.
- Uses a FastAPI backend for market-analysis and backtest endpoints.
- Retrieves market data for supported tickers.
- Calculates technical-analysis inputs used by the signal engine.
- Produces market signals such as `BUY`, `WATCH`, or `SELL`.
- Provides entry ranges, targets, stops, confidence, and score information when available.
- Runs historical strategy backtests and displays trade history and performance statistics.
- Runs the backend in Docker with Docker Compose.

## Architecture

```text
Browser
  │
  ▼
React + Vite frontend (:5173)
  │
  │ HTTP fetch
  ▼
FastAPI backend (:8000)
  │
  ├── /health
  ├── /market/{ticker}
  └── /backtest/{ticker}
        │
        ▼
Market data + analysis + backtesting services
```

## Local development

### Backend

The API is exposed on port `8000` through Docker Compose.

```bash
cd ~/hermes
docker compose up -d --build hermes-api
```

Verify the API:

```bash
curl -i http://127.0.0.1:8000/health
curl -i http://127.0.0.1:8000/market/AAPL
curl -i http://127.0.0.1:8000/backtest/AAPL
```

### Frontend

```bash
cd ~/hermes/frontend
npm run dev
```

The development UI runs at `http://localhost:5173`.

## API endpoints

| Endpoint | Purpose |
|---|---|
| `/health` | Confirms the API is online |
| `/market/{ticker}` | Returns market analysis for a ticker |
| `/backtest/{ticker}` | Runs the historical strategy backtest |

## Signal concept

Hermes is being designed around a signal card that makes the analysis easy to understand:

- **BUY / WATCH / SELL** signal
- Confidence / score
- Buy or entry zone
- Current price
- Target price
- Risk stop
- Risk/reward information
- Explanation of the technical factors behind the signal

The project is intended as an analysis and learning tool, not financial advice.

## Development story

This project was built iteratively by getting each layer working and then debugging the integration between them. The development process included fixing Python syntax and indentation issues, connecting the React frontend to FastAPI, resolving CORS, working with Docker Compose, rebuilding stale Docker images, and fixing a backtest `NoneType` error.

See [`docs/DEVELOPMENT_LOG.md`](docs/DEVELOPMENT_LOG.md) for the detailed history.

## Documentation

- [`Development Log`](docs/DEVELOPMENT_LOG.md) — what was built and fixed step by step
- [`Architecture`](docs/ARCHITECTURE.md) — how the frontend, API, Docker, and services fit together
- [`Backtesting`](docs/BACKTESTING.md) — strategy/backtest behavior and validation notes
- [`API`](docs/API.md) — endpoint documentation and verification commands
- [`Troubleshooting`](docs/TROUBLESHOOTING.md) — problems encountered and solutions
- [`Roadmap`](docs/ROADMAP.md) — next improvements planned for Hermes

## Current validation

The working local setup has successfully demonstrated:

- React/Vite frontend running on port `5173`
- Dockerized FastAPI backend running on port `8000`
- `/health` returning `200 OK`
- `/market/AAPL` returning `200 OK`
- CORS allowing the local frontend origin
- `/backtest/AAPL` returning `200 OK`
- Backtest results displayed in the web UI
- Market-analysis results displayed in the web UI

## Disclaimer

Hermes is an educational/software project for market analysis and experimentation. Backtests are historical simulations and do not guarantee future results. Nothing in this repository should be treated as financial advice or a recommendation to buy or sell securities.
