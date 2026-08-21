# Cloud Run Health Checks

The local endpoints map to Cloud Run container health checks as follows:

- `/health` is the liveness check. It only confirms that the Node.js process can accept HTTP requests and does not call Postgres.
- `/ready` is the readiness check. It runs `SELECT 1` against Postgres and returns `200 READY` when the dependency is reachable or `503 NOT READY` when it is unavailable.

Cloud Run startup and liveness probes can use `/health` to determine whether the container has started and remains responsive. A startup probe prevents liveness checks from running before the application has initialized. For a deployment where traffic must be withheld while a dependency is unavailable, configure the platform or ingress to use `/ready` as the readiness signal. Cloud Run does not use a Docker Compose restart policy; container instances are managed by the platform, and readiness determines whether an instance should receive traffic.

The local Compose `healthcheck` uses `/ready` so dependency failure is visible as `unhealthy`. The `unless-stopped` policy governs container restarts after process/container exit; Docker Compose does not automatically restart a still-running container solely because its health status is `unhealthy`.