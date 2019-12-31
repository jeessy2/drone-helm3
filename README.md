# Drone plugin for Helm 3

[![Build Status](https://cloud.drone.io/api/badges/pelotech/drone-helm3/status.svg)](https://cloud.drone.io/pelotech/drone-helm3)
[![Go Report](https://goreportcard.com/badge/github.com/pelotech/drone-helm3)](https://goreportcard.com/report/github.com/pelotech/drone-helm3)
[![](https://images.microbadger.com/badges/image/pelotech/drone-helm3.svg)](https://microbadger.com/images/pelotech/drone-helm3 "Get your own image badge on microbadger.com")

This plugin provides an interface between [Drone](https://drone.io/) and [Helm 3](https://github.com/kubernetes/helm):

* Lint your charts
* Deploy your service
* Delete your service

The plugin is inpsired by [drone-helm](https://github.com/ipedrazas/drone-helm), which fills the same role for Helm 2. It provides a comparable feature-set and the configuration settings are backwards-compatible.

## Example configuration

The examples below give a minimal and sufficient configuration for each use-case. For a full description of each command's settings, see [docs/parameter_reference.md](docs/parameter_reference.md).

### Linting

```yaml
steps:
  - name: lint
    image: pelotech/drone-helm3
    settings:
      helm_command: lint
      chart: ./
```

### Installation

```yaml
steps:
  - name: deploy
    image: pelotech/drone-helm3
    settings:
      helm_command: upgrade
      chart: ./
      release: my-project
    environment:
      API_SERVER: https://my.kubernetes.installation/clusters/a-1234
      KUBERNETES_TOKEN:
        from_secret: kubernetes_token
```

### Uninstallation

```yaml
steps:
  - name: uninstall
    image: pelotech/drone-helm3
    settings:
      helm_command: uninstall
      release: my-project
    environment:
      API_SERVER: https://my.kubernetes.installation/clusters/a-1234
      KUBERNETES_TOKEN:
        from_secret: kubernetes_token
```

## Upgrading from drone-helm

drone-helm3 is largely backwards-compatible with drone-helm. There are some known differences:

* You'll need to migrate the deployments in the cluster [helm-v2-to-helm-v3](https://helm.sh/blog/migrate-from-helm-v2-to-helm-v3/)

* `prefix` must be supplied via the `settings` block, not `environment`.
* Several settings no longer have any effect:
    * `purge` -- this is the default behavior in Helm 3
    * `recreate_pods`
    * `tiller_ns`
    * `upgrade`
    * `canary_image`
    * `client_only`
    * `stable_repo_url`

Since helm 3 does not require Tiller, we also recommend switching to a service account with less-expansive permissions.
