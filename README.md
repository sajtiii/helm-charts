# Helm Charts

A collection of Helm charts for deploying web applications to Kubernetes.

## Charts

| Chart | Description |
|-------|-------------|
| [web-app](charts/web-app/) | General-purpose chart for deploying web applications with support for advanced deployment strategies, service mesh, secrets management, and autoscaling |

## Usage

[Helm](https://helm.sh) must be installed to use the charts. Please refer to Helm's [documentation](https://helm.sh/docs) to get started.

Add the repository:

```sh
helm repo add sajtiii https://sajtiii.github.io/helm-charts
helm repo update
```

Search available charts:

```sh
helm search repo sajtiii
```

Install a chart:

```sh
helm install my-app sajtiii/web-app -f my-values.yaml
```

Upgrade a release:

```sh
helm upgrade my-app sajtiii/web-app -f my-values.yaml
```

Uninstall a release:

```sh
helm uninstall my-app
```

## Development

Charts are linted and tested automatically on pull requests using [chart-testing](https://github.com/helm/chart-testing).
Releases are published automatically on merge to `main` using [chart-releaser](https://github.com/helm/chart-releaser).
