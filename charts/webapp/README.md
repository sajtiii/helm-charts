# Website chart

This cart creates a simple deployment which can be used to deploy web app.

## Requirements
- [CertManager](https://cert-manager.io/) **Required** with a `letsencrypt` named Issuer.
- [Traefik](https://traefik.io/traefik/) **Required** with CRSs installed.
- [Metrics Server](https://github.com/kubernetes-sigs/metrics-server) *Optional*, for HPA to work.
- [Linkerd](https://linkerd.io/) *Optional*
- [Vault](https://www.vaultproject.io/) *Optional*, for storing secrets (like env vars).
- [Prometheus](https://prometheus.io/) *Optional* with `Traefik` setup, for canary release to work.
- [Argo Rollouts](https://argoproj.github.io/rollouts/) *Optional*, for canary to work. 
