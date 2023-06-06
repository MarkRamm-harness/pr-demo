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

1. In **GitOps**, click **Settings**, and then **Clusters**.
   
   ![](./static/harness-git-ops-application-set-tutorial-28.png)

2. Click **New Cluster**.
3. In **Name**, enter the name **appset-example**.
4. In **GitOps Agent**, select the Agent you installed earlier.
5. Click **Continue**.
6. In **Details**, click **Use the credentials of a specific Harness GitOps Agent**.
7. Click **Save and Continue**.
   The connections is verified.
   
   ![](./static/harness-git-ops-application-set-tutorial-29.png)

8. Click **Finish**.

### Dev Application Cluster

We'll add Harness GitOps Clusters for the two target clusters where we want to add our application.

1. Create a new Harness GitOps Cluster for your dev cluster.
2. Name the cluster **engineering-dev**.
3. In **GitOps Agent**, select the GitOps Agent you added earlier.
   
   ![](./static/harness-git-ops-application-set-tutorial-30.png)

4. In **Details**, select **Specify Kubernetes Cluster URL and credentials**.
5. In **Master URL**, enter the Endpoint URL for the target cluster (you can use `kubectl cluster-info` or your cloud console). Ensure that you use the `https://` scheme.Here's an example:
   
   ![](./static/harness-git-ops-application-set-tutorial-31.png)

6. In **Authentication**, use the authentication method you prefer. In this tutorial, we use the `default` namespace `service-account` token.
7. Click **Save and Continue**. The GitOps Cluster is verified.
8. Click **Finish**.
9.  Repeat the process for the **Prod** cluster.
	1. Name the cluster **engineering-prod**.
	2. Use the same Agent.
	3. For **Master URL**, ensure that you use the `https://` scheme.
	4. Use whatever authentication method you want.

When you're done, you will have three Harness GitOps Clusters: 1 for the GitOps Agent and two for the target clusters.

![](./static/harness-git-ops-application-set-tutorial-32.png)

You might see a Warning status. This status simply indicates that nothing has been deployed to the cluster yet.


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
