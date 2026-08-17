# Hermes Development Log

This document records the major steps taken while building Hermes and getting the local web UI working.

## 1. Project foundation

Hermes was developed as a hands-on IT/software project with two major application layers:

- A React/Vite frontend for the dashboard.
- A FastAPI backend for market analysis and backtesting.

The backend was organized into service modules for market analysis and backtesting.

## 2. API endpoints

The FastAPI application exposes:

- `GET /health`
- `GET /market/{ticker}`
- `GET /backtest/{ticker}`

The API was tested directly with `curl` while debugging the UI.

## 3. Frontend integration

The React application was configured to call:

```js
const API = "http://127.0.0.1:8000";
```

The UI requests market analysis and backtest data for the selected ticker.

## 4. Debugging frontend syntax and runtime issues

During development, several Python syntax/indentation problems were encountered. The debugging process involved checking the exact line reported by the interpreter, correcting indentation, and rerunning the application rather than changing unrelated code.

This was an important part of the project because it demonstrated how Python block structure affects `if`, `else`, and loop execution.

## 5. Docker discovery

The backend was discovered to be running inside a Docker Compose service named `hermes-api` rather than as a manually launched Uvicorn process.

The container published:

```text
0.0.0.0:8000 -> 8000/tcp
```

This explained why starting another Uvicorn process produced an `Address already in use` error.

## 6. CORS debugging

The frontend ran on `localhost:5173` while the API ran on port `8000`. The browser initially reported:

```text
HERMES ERROR — Failed to fetch
```

Direct `curl` requests to the API returned `200 OK`, so the API itself was reachable.

A request with an Origin header showed that the response was missing the required CORS header. FastAPI was updated with `CORSMiddleware` to allow:

- `http://localhost:5173`
- `http://127.0.0.1:5173`

The CORS change had to be rebuilt into the Docker image because the running container did not mount the local backend source code.

## 7. Docker image rebuild

The Dockerfile copies the backend application into the image. Therefore, changes made to the host source files do not automatically change an existing container.

The working rebuild process became:

```bash
docker compose build --no-cache hermes-api
docker compose up -d --force-recreate hermes-api
```

CORS was then verified with:

```bash
curl -i -H "Origin: http://localhost:5173" \
  http://127.0.0.1:8000/market/AAPL
```

The response included:

```text
access-control-allow-origin: http://localhost:5173
```

## 8. Backtest failure

After CORS was fixed, the UI still reported `Failed to fetch` because the second request, `/backtest/AAPL`, was returning an HTTP 500 error.

The Docker traceback identified:

```text
TypeError: '>=' not supported between instances of 'str' and 'NoneType'
```

The problem was in the date-selection logic of `backtest.py`.

The code was effectively allowing the loop to execute even when `start_date` was `None`.

## 9. Backtest indentation fix

The affected logic was corrected so that the date comparison loop only runs when `start_date` exists:

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

This was then rebuilt into Docker.

## 10. Successful backtest

After rebuilding, the endpoint was tested directly:

```bash
curl -i http://127.0.0.1:8000/backtest/AAPL
```

The response changed from an HTTP 500 failure to:

```text
HTTP/1.1 200 OK
```

The endpoint returned JSON containing trade history and summary statistics.

## 11. Successful UI integration

After the API returned successful responses for both market analysis and backtesting, the React UI was refreshed.

The dashboard successfully displayed:

- API Connected status
- AAPL market analysis
- Hermes signal information
- strategy backtest results
- trade history
- performance statistics

This completed the first working end-to-end Hermes prototype.

## 12. Current known issue

The dashboard previously displayed a different trade count than the raw API summary. The backend response reported `total_trades: 19` during validation while the UI displayed `31` in one view. This should be reconciled before treating the dashboard statistics as final.

## 13. Next development goal

The next major UI improvement is a clearer trade-signal card that turns the backend analysis into an easy-to-read instruction:

- BUY
- WATCH
- SELL
- confidence
- entry zone
- target
- stop
- risk/reward
- explanation of the technical reasons behind the signal

## Lessons learned

1. A successful API request from `curl` does not guarantee a browser request will work; CORS matters.
2. Docker containers can keep running older copies of source files until an image is rebuilt.
3. When an endpoint returns HTTP 500, the browser may surface it as a generic fetch failure.
4. Python indentation is executable program structure, not just formatting.
5. Debugging each layer independently makes full-stack problems much easier to isolate.
