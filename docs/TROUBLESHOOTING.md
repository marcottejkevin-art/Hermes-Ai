# Hermes Troubleshooting

## `Failed to fetch` in the browser

### Check the API directly

```bash
curl -i http://127.0.0.1:8000/health
curl -i http://127.0.0.1:8000/market/AAPL
curl -i http://127.0.0.1:8000/backtest/AAPL
```

If these fail, fix the backend before debugging the frontend.

### Check CORS

```bash
curl -i \
  -H "Origin: http://localhost:5173" \
  http://127.0.0.1:8000/market/AAPL
```

Look for:

```text
access-control-allow-origin: http://localhost:5173
```

### Docker source changes

If the backend is running inside Docker and the source directory is not bind-mounted, editing a local Python file does not change the running container.

Rebuild the image:

```bash
docker compose build --no-cache hermes-api
docker compose up -d --force-recreate hermes-api
```

To verify the file inside the container:

```bash
docker exec hermes-api sed -n '35,55p' /app/app/services/backtest.py
```

## `Address already in use` on port 8000

The Hermes API may already be running through Docker. Check:

```bash
docker ps
sudo ss -ltnp | grep :8000
```

Do not start a second Uvicorn server if Docker already owns port `8000`.

## Vite starts on port 5174

If Vite says port `5173` is already in use, another development server may already be running.

Check the port:

```bash
fuser 5173/tcp
```

Stop the unwanted frontend process and restart Vite:

```bash
cd ~/hermes/frontend
npm run dev
```

Keep the frontend on `5173` when using the documented CORS configuration.

## Backtest returns HTTP 500

Inspect Docker logs:

```bash
docker logs --tail 80 hermes-api
```

During development, the relevant error was:

```text
TypeError: '>=' not supported between instances of 'str' and 'NoneType'
```

The fix was to keep the date-comparison loop inside the `if start_date:` block.

## Debugging workflow

1. Test the API with `curl`.
2. Check HTTP status codes.
3. Check CORS headers.
4. Check Docker logs.
5. Inspect the code actually running inside the container.
6. Rebuild the Docker image after source changes.
7. Refresh the browser only after the API is healthy.

This sequence avoids changing multiple layers at the same time and makes failures easier to isolate.
