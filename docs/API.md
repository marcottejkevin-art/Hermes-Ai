# Hermes API

## Base URL

Local development:

```text
http://127.0.0.1:8000
```

## `GET /health`

Checks whether the FastAPI service is running.

Example:

```bash
curl -i http://127.0.0.1:8000/health
```

Expected result:

```text
HTTP/1.1 200 OK
```

## `GET /market/{ticker}`

Returns market analysis for a ticker.

Example:

```bash
curl -i http://127.0.0.1:8000/market/AAPL
```

The response is JSON used by the frontend to populate the market-analysis and Hermes-signal sections.

## `GET /backtest/{ticker}`

Runs the historical strategy backtest for a ticker.

Example:

```bash
curl -i http://127.0.0.1:8000/backtest/AAPL
```

The response contains trade data and summary information used by the dashboard.

## CORS verification

To verify that the API allows the local Vite frontend:

```bash
curl -i \
  -H "Origin: http://localhost:5173" \
  http://127.0.0.1:8000/market/AAPL
```

The response should contain:

```text
access-control-allow-origin: http://localhost:5173
```

## Debugging rule

When the browser reports `Failed to fetch`, test the endpoint directly first. A direct `curl` test can distinguish:

- API unavailable
- API returning an HTTP error
- CORS configuration problem
- frontend request problem

This approach was used to diagnose Hermes during development.
