---
description: PR Pipelines Quickstart
---

# Quickstart

This guide presents a basic introduction to Harness GitOps PR Pipelines. Together, we will:

1. Create a local Kubernetes cluster.

1. Install GitOps Agent.

1. Demonstrate how we can progress changes through multiple environments by
   interacting with your GitOps repository and `ApplicationSet`.

1. Clean up.

## Prerequisites

* [Docker](https://www.docker.com/)
* [kind](https://kind.sigs.k8s.io/) or [k3d](https://k3d.io/): These
  instructions were tested with:
    * kind: v0.19.0
    * k3d: v5.4.9

### Starting a local cluster

<Tabs groupId="cluster-start">
<TabItem value="kind" label="kind">

```shell
kind create cluster \
  --wait 120s \
  --config - <<EOF
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: pr-quickstart
EOF
```

</TabItem>
<TabItem value="k3d" label="k3d">

```shell
k3d cluster create pr-quickstart \
  --no-lb \
  --k3s-arg '--disable=traefik@server:0' \
  --wait
```

</TabItem>
</Tabs>

### Installing GitOps Agent



### Create a GitOps repository

In this step, we will create a GitOps repo on GitHub to house variations of our
application manifests for different environments: `dev`, `stage`, and
`prod`.

Visit https://github.com/mansong1/pr-demo and fork the repository into your own
GitHub account.

You can explore the repository and see that the `main` branch contains common
configuration in a `base/` directory as well as environment-specific overlays in
paths of the form `env/<environment name>/`. [Kustomize](https://kustomize.io/)
is used as a configuration management tool that combines base configuration with
environment-specific configuration.

:::note
This layout is typical of a GitOps repository using Kustomize and is not at all
Harness specific.
:::


## Cleaning up

To clean up, we will simply destroy our kind or k3d cluster:

<Tabs groupId="cluster-start">
<TabItem value="kind" label="kind">

```shell
kind delete cluster --name pr-quickstart
```

</TabItem>
<TabItem value="k3d" label="k3d">

```shell
k3d cluster delete pr-quickstart
```

</TabItem>
</Tabs>
