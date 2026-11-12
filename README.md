# Golden Path Template

A runnable Go service scaffold that deploys through the platform's golden path.
Use it when you need a new service with CI/CD, TLS, secrets, metrics, and rollback
already wired.

**Status:** scaffold in progress.

## What you get

- Go service with `/healthz`, `/readyz`, `/metrics`, and a sample `GET /notes`
- Postgres (ClusterIP) with a simple migration
- Dockerfile
- Helm chart: Deployment, Service, Ingress, HPA, PDB, ServiceMonitor, ExternalSecret
- GitHub Actions: test → Syft SBOM → Grype → cosign → GHCR → digest bump
- `deploy/staging/values.yaml` and `deploy/prod/values.yaml`

## Create a service

1. Click **Use this template** and create a new public repo.
2. Update `deploy/staging/values.yaml` and `deploy/prod/values.yaml`:
   - `image.repository`
   - `ingress.host`
   - `externalSecret.remoteRef.key`
3. Push to `main`. CI builds and updates the staging digest.
4. Ask the platform team to add an Argo CD Application (v0.1), or wait for the
   ApplicationSet (v0.2).
5. Merge the promotion PR to deploy to prod.

## Local development

```bash
go test ./...
go run ./cmd/api
```

Requires a local Postgres, or an optional Docker Compose file.

## Configuration

| Value | Purpose |
|---|---|
| `image.repository` | GHCR image path |
| `image.digest` | Immutable image digest; set by CI |
| `ingress.host` | Public hostname |
| `externalSecret.remoteRef.key` | SSM parameter path for database credentials |
| `replicaCount` | Pod count |

## Known limitations

- The chart and CI workflow are copied into each repo; fixes require updating repos
  until v0.2's shared chart and reusable workflow.
- No auth.
- Single Postgres; no backups or HA.
- NetworkPolicy is not enforced on Flannel in v0.1.
