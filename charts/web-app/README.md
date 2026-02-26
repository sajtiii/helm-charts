# web-app

A general-purpose Helm chart for deploying web applications to Kubernetes. Supports standard rolling deployments as well as advanced strategies (canary, blue/green) via Argo Rollouts, service mesh integration via Linkerd, secret injection via HashiCorp Vault, and automated TLS certificate management via cert-manager.

## Requirements

| Dependency | Required | Notes |
|------------|----------|-------|
| [Traefik](https://traefik.io/traefik/) | **Yes** | Must have CRDs installed |
| [cert-manager](https://cert-manager.io/) | **Yes** | Must have a `letsencrypt` Issuer configured |
| [Metrics Server](https://github.com/kubernetes-sigs/metrics-server) | No | Required for HPA to function |
| [Linkerd](https://linkerd.io/) | No | Required for service mesh features |
| [HashiCorp Vault](https://www.vaultproject.io/) | No | Required for secret injection; needs RBAC configured |
| [Prometheus](https://prometheus.io/) | No | Required for canary analysis; needs Traefik metrics configured |
| [Argo Rollouts](https://argoproj.github.io/rollouts/) | No | Required for canary and blue/green deployment strategies |

## Installation

```sh
helm repo add sajtiii https://sajtiii.github.io/helm-charts
helm repo update
helm install my-app sajtiii/web-app -f my-values.yaml
```

## Configuration

All available configuration options are documented inline in [values.yaml](values.yaml). The file covers:

- **Image** — repository, tag, pull policy, command/args overrides, and ArgoCD Image Updater integration
- **Replicas & scaling** — static replica count or HPA with CPU/memory targets
- **Rollout strategy** — `rolling`, `recreate`, `canary` (Argo Rollouts), or `bluegreen` (Argo Rollouts)
- **Ingress** — hostname and path routing via Traefik IngressRoute, per-route middleware support
- **TLS** — automatic certificate provisioning via cert-manager
- **Health checks** — configurable liveness and readiness probes (HTTP, exec, or custom)
- **Resources** — CPU and memory requests and limits
- **Security** — pod-level and container-level security contexts
- **Secrets** — HashiCorp Vault agent injection for environment secrets and TLS certificates
- **Service mesh** — Linkerd sidecar injection with port exclusion support
- **Volumes** — PersistentVolumeClaim definitions with configurable storage class and access modes
- **Disruption budget** — PodDisruptionBudget with configurable minAvailable/maxUnavailable
- **Priority** — pod scheduling priority class

## Example

A minimal `values.yaml` to deploy an application:

```yaml
name: my-app

image:
  repository: ghcr.io/my-org/my-app
  tag: "1.0.0"

hostnames:
  - "my-app.example.com"
```

For a more complete example including autoscaling, canary rollout, and Vault secrets, see the inline comments in [values.yaml](values.yaml).
