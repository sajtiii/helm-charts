# system-upgrade-controller

Helm chart for deploying [system-upgrade-controller](https://github.com/rancher/system-upgrade-controller) and configuring automated node upgrades for k3s, RKE2, or any distribution supported by the controller.

## Overview

This chart installs the Rancher system-upgrade-controller and configures two upgrade Plans:

- **server-plan** — upgrades control-plane nodes
- **agent-plan** — upgrades worker nodes, optionally waiting for server nodes to finish first

Upgrades are triggered automatically via a release channel or a pinned version, and restricted to a configurable maintenance window.

## Prerequisites

- Kubernetes 1.21+
- Helm 3+

## Installation

```bash
helm install system-upgrade-controller oci://ghcr.io/sajtii/helm-charts/system-upgrade-controller
```

Or from a local checkout:

```bash
helm install system-upgrade-controller ./charts/system-upgrade-controller
```

## Distribution-specific configuration

### k3s (default)

```yaml
channel: https://update.k3s.io/v1-release/channels/stable
upgrade:
  image: rancher/k3s-upgrade
  agentPrepare: true
```

### RKE2

```yaml
channel: https://update.rke2.io/v1-release/channels/stable
upgrade:
  image: rancher/rke2-upgrade
  agentPrepare: false
```

### Pin a specific version (any distro)

```yaml
version: v1.32.2+k3s1   # overrides channel
upgrade:
  image: rancher/k3s-upgrade
```

## Values

| Key | Default | Description |
|-----|---------|-------------|
| `controllerVersion` | `v0.19.0` | Image tag for `rancher/system-upgrade-controller` |
| `channel` | `https://update.k3s.io/v1-release/channels/stable` | Release channel URL. Ignored when `version` is set. |
| `version` | `""` | Pin Plans to a specific version (e.g. `v1.32.2+k3s1`). When set, `channel` is not used. |
| `upgrade.image` | `rancher/k3s-upgrade` | Distro-specific upgrade job image (`rancher/k3s-upgrade`, `rancher/rke2-upgrade`, etc.) |
| `upgrade.concurrency` | `1` | Number of nodes to upgrade simultaneously |
| `upgrade.cordon` | `true` | Cordon nodes before upgrading |
| `upgrade.agentPrepare` | `true` | Include a prepare step in the agent plan that waits for server-plan to complete. Required for k3s; not needed for RKE2 or single-node clusters. |
| `upgrade.volumes` | `[]` | Host paths to mount into the upgrade job container. See [Host path volumes](#host-path-volumes). |
| `upgrade.window.days` | `[monday]` | Days of the week the maintenance window is active |
| `upgrade.window.startTime` | `"04:00"` | Start of maintenance window (24h) |
| `upgrade.window.endTime` | `"05:00"` | End of maintenance window (24h) |
| `upgrade.window.timeZone` | `UTC` | Timezone for the maintenance window |

## Host path volumes

Some distributions store CA certificates in non-standard locations, which can cause the upgrade job to fail when fetching release information over HTTPS. For example, on RHEL/Fedora-based nodes `/etc/ssl` contains symlinks that point into `/etc/pki`, which isn't available inside the upgrade container by default.

Use `upgrade.volumes` to mount host paths into the upgrade job pod:

```yaml
upgrade:
  volumes:
    - name: host-pki
      source: /etc/pki
      destination: /etc/pki
    - name: host-run
      source: /run
      destination: /run
```

Each entry requires:

| Field | Description |
|-------|-------------|
| `name` | Volume name (must be unique within the list) |
| `source` | Absolute path on the host node |
| `destination` | Absolute path inside the upgrade container |

## Resources Created

| Kind | Name | Namespace |
|------|------|-----------|
| Namespace | `system-upgrade` | — |
| ServiceAccount | `system-upgrade` | `system-upgrade` |
| ConfigMap | `default-controller-env` | `system-upgrade` |
| Deployment | `system-upgrade-controller` | `system-upgrade` |
| Role | `system-upgrade-controller` | `system-upgrade` |
| ClusterRole | `system-upgrade-controller` | — |
| ClusterRole | `system-upgrade-controller-drainer` | — |
| RoleBinding | `system-upgrade` | `system-upgrade` |
| ClusterRoleBinding | `system-upgrade` | — |
| ClusterRoleBinding | `system-upgrade-drainer` | — |
| Plan (CRD) | `server-plan` | `system-upgrade` |
| Plan (CRD) | `agent-plan` | `system-upgrade` |

## Testing

```bash
helm unittest charts/system-upgrade-controller
```
