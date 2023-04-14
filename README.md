# Fluentbit, Otel, Prometheus, Pixie Demo

- [Tool setup](#tool-setup)
- [k8s Cluster Setup](#k8s-cluster-setup)
- [Install demo apps](#install-demo-apps)
- [Install Pixie](#install-pixie)
- [Port-Forward](#port-forward)

## Tool setup

This guide assumes that you have `helm`, `kubectl`, and (optionally) `minikube` installed.

If not, you can follow the install guides for [helm](https://helm.sh/docs/intro/install/), [kubectl](https://kubernetes.io/docs/tasks/tools/), and [minikube](https://minikube.sigs.k8s.io/docs/start/) before proceeding with the rest of this setup.

## k8s Cluster Setup

> :warning: Please review the cluster requirements for Pixie [here](https://docs.px.dev/installing-pixie/requirements/).

We strongly recommend using a cloud based k8s cluster for ease of deployment.
If you need help setting up a cluster, follow the [cluster setup guide](cluster_setup.md).

## Install demo apps

1. We use helm to install the demo apps

1. Add the otel demo repo

    ```console
    helm repo add otel-demo https://pixie-labs.github.io/opentelemetry-helm-charts
    helm repo update
    ```

1. Install the otel demos

    ```console
    helm install --create-namespace --namespace otel-demo otel-demo otel-demo/opentelemetry-demo
    ```

## Install Pixie

1. Get the Pixie CLI

    ```console
    bash -c "$(curl -fsSL https://withpixie.ai/install.sh)"
    ```

1. (optional) The CLI installer should prompt you to authenticate with Pixie but if it doesn't, you can auth manually by running the following

    ```console
    px auth login
    ```

1. We can now use helm to install Pixie. Add the pixie repo

    ```console
    helm repo add pixie-operator https://pixie-operator-charts.storage.googleapis.com
    helm repo update
    ```

1. Install pixie on the cluster

    ```console
    PIXIE_DEPLOY_KEY=$(px deploy-key create --short 2>&1 | grep px-dep)

    helm install --namespace pl --create-namespace pixie pixie-operator/pixie-operator-chart --set deployKey="${PIXIE_DEPLOY_KEY}" --set clusterName="${USER}_otel_demo"
    ```

## Port-Forward

In order to access the demo application and dashboards, you need to forward the correct ports:

```console
kubectl port-forward svc/my-otel-demo-frontendproxy 8080:8080
```

At this point, you should be able to go to [http://localhost:8080/](http://localhost:8080/) to access the demo application UI, and [http://localhost:8080/grafana](http://localhost:8080/grafana) to access the Grafana dashboards.
