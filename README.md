# Health Check Lab

## Setup
```bash
docker compose up -d
```

## Verify Service
```bash
docker compose ps
curl -i localhost:3000/health
curl -i localhost:3000/ready
curl localhost:3000/orders
```

`/health` is a shallow liveness check and does not use the database. `/ready` checks the database and returns `503 NOT READY` when Postgres is unavailable. The Compose healthcheck requires both endpoints, so dependency failures appear as `unhealthy`.

## Stop Database
```bash
docker compose stop db
```

## Observe Behaviour
```bash
docker compose ps
```

Restore the database and verify recovery:

```bash
docker compose start db
docker compose ps
curl -i localhost:3000/ready
docker inspect --format '{{json .State.Health.Status}}' orders-api
```

See [CLOUD_RUN.md](CLOUD_RUN.md) for the Cloud Run and Kubernetes probe mapping.
