# Flux Operator

Flux Operator is a k8s Controller that manages the lifecycle of Flux CD and an alternate method to `flux bootstrap`.

It assists in automating the installation, configuration, upgrades of Flux CD based on a declarative API by providing 2 main CRD’s:

- FluxInstance
- FluxReport

## Installation

The installation of this Controller is done using `helm`.

### Manual

```bash
helm install flux-operator \
    oci://ghcr.io/controlplaneio-fluxcd/charts/flux-operator \
    --namespace flux-system \
    --create-namespace
```

## Operator Components

### FluxInstance CRD

The parts we need to worry about in this part of the API is:

- `.spec.distribution` - Flux distribution to install
- `.spec.components`- List of Flux components to install
- `.spec.cluster`- Kubernetes cluster configuration
- `.spec.sync`- Flux sync configuration. When set, a Flux source and a Flux Kustomization are generated to sync the cluster state with the source repository.
- `.spec.kustomize`- Kustomize patches to apply to the Flux controllers.

The manifests for these are stored as below:

```bash
❯ tree clusters .
.
clusters
├── flux-kind-k8s/flux-system
    └── flux-instance.yaml
```

To learn more about it visit the documentation of _FluxInstance_ [Here](https://fluxcd.control-plane.io/operator/fluxinstance/)

### FluxReport CRD

It reflects the observed state of a Flux installation. It assists in monitoring and troubleshooting Flux by providing information about the installed components via FluxInstance.

To learn more about it visit the documentation of _FluxReport_ [Here](https://fluxcd.control-plane.io/operator/fluxreport/)

## Authentication to GitHub via GitHub App

> Note: Required if the Repository is Private

For authentication to GitHub we can use a GitHub App with the required permissions.

Now, we will use the manifests which are structured as below

```bash
❯ tree setup .
setup
├── base
│   ├── cronjob.yaml
│   ├── kustomization.yaml
│   ├── rbac.yaml
│   └── serviceaccount.yaml
└── overlays
    ├── flux-kind-k8s/flux-system
        └── kustomization.yaml
```

These can be applied in 2 ways as described below:

### Manual Apply

```bash
kustomize build ./setup/overlays/flux-kind-k8s/flux-system | kubectl apply -f -
```
