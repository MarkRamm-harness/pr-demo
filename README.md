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

   ![image](https://github.com/mansong1/pr-demo/assets/7550833/373da8be-50e1-4253-8d23-69b18a1512b0)

2. Click **New Cluster**.
3. In **Name**, enter the name **appset-example**.
4. In **GitOps Agent**, select the Agent you installed earlier.
5. Click **Continue**.
6. In **Details**, click **Use the credentials of a specific Harness GitOps Agent**.
7. Click **Save and Continue**.
   The connections is verified.
   
   ![image](https://github.com/mansong1/pr-demo/assets/7550833/c20442e1-dc07-4414-9a98-3c61814eaffc)

8. Click **Finish**.

### Dev Application Cluster

We'll add Harness GitOps Clusters for the two target clusters where we want to add our application.

1. Create a new Harness GitOps Cluster for your dev cluster.
2. Name the cluster **dev**.
3. In **GitOps Agent**, select the GitOps Agent you added earlier.
   
   ![Screenshot 2023-06-06 at 15 26 23](https://github.com/mansong1/pr-demo/assets/7550833/95f0cc5f-125e-4601-9534-6c196668cb84)


4. In **Details**, select **Specify Kubernetes Cluster URL and credentials**.
5. In **Master URL**, enter the Endpoint URL for the target cluster (you can use `kubectl cluster-info` or your cloud console). Ensure that you use the `https://` scheme. Here's an example:
   
   <img width="648" alt="Screenshot 2023-06-06 at 15 35 40" src="https://github.com/mansong1/pr-demo/assets/7550833/d623d67d-c1d0-4650-a17d-6f501cfbf0af">


6. In **Authentication**, use the authentication method you prefer. In this tutorial, we use the `se-name` namespace `service-account` token.
7. Click **Save and Continue**. The GitOps Cluster is verified.
8. Click **Finish**.

  ![Screenshot 2023-06-06 at 15 30 34](https://github.com/mansong1/pr-demo/assets/7550833/cf41f238-43b7-4eab-ae2c-c6d5d36622da)



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
