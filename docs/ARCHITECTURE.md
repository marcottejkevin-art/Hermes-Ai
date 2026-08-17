# Hermes Architecture

## High-level design

Hermes is a small full-stack application with a browser-based frontend and a containerized API.

```text
┌──────────────────────────────┐
│ Browser                      │
│ React + Vite                 │
│ localhost:5173               │
└──────────────┬───────────────┘
               │ HTTP fetch
               ▼
┌──────────────────────────────┐
│ Docker Compose               │
│ hermes-api                   │
│ FastAPI :8000                │
└──────────────┬───────────────┘
               │
       ┌───────┴────────┐
       ▼                ▼
 Market analysis     Backtesting
 service             service
       │                │
       └───────┬────────┘
               ▼
        Market data source
```

## Frontend

The frontend is a React/Vite application located under `frontend/`.

It is responsible for:

- rendering the Hermes dashboard
- accepting a ticker symbol
- calling the API
- displaying market analysis
- displaying the Hermes signal
- displaying backtest statistics
- displaying trade history

The local development server uses port `5173`.

## Backend

The backend is a FastAPI application located under `backend/`.

Primary routes:

```text
GET /health
GET /market/{ticker}
GET /backtest/{ticker}
```

The backend is packaged into the `hermes-api` Docker image and exposed on host port `8000`.

## Docker

Docker Compose manages the API service. The Dockerfile copies the application into the image, which means source changes require an image rebuild unless a development bind mount is added later.

Recommended development rebuild:

```bash
docker compose build --no-cache hermes-api
docker compose up -d --force-recreate hermes-api
```

## CORS

Because the frontend and API use different local origins/ports, FastAPI uses `CORSMiddleware` to allow the Vite development origin.

Allowed development origins include:

```text
http://localhost:5173
http://127.0.0.1:5173
```

## Data flow

A typical UI request works like this:

1. User selects a ticker such as `AAPL`.
2. React calls `/market/AAPL`.
3. FastAPI calls the market-analysis service.
4. The service obtains market data and calculates analysis inputs.
5. JSON is returned to React.
6. React displays the signal and analysis.
7. React calls `/backtest/AAPL`.
8. FastAPI runs the historical strategy.
9. JSON containing trades and summary statistics is returned.
10. React renders the backtest section.

## Design goals

The architecture is intentionally simple so each layer can be tested independently:

- API endpoints can be tested with `curl`.
- Docker can be inspected independently of the frontend.
- The frontend can be run independently with Vite.
- Backtesting logic lives in a service module rather than in UI code.

This separation also made debugging the CORS and backtest failures much easier.
